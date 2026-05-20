# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build System

GNU Autotools + Fortran 77 compiler (gfortran). The build produces two binaries.

```sh
autoreconf -i          # only if configure is missing
./configure
make
sudo make install
```

Array dimensions in the Fortran source are static — large models need the `-mcmodel=medium` flag (already set in each `src/*/Makefile.am`).

## Architecture

Two-stage pipeline run sequentially:

1. **PSGRN** (`src/psgrn/`, binary `fomosto_psgrn2008a`) — Computes Green's functions for a multi-layered viscoelastic halfspace at a set of source depths. Wavenumber-frequency spectra are computed via the propagator matrix method (PSV + SH), then Hankel-transformed to the spatial domain. Output is a Green's function database file.

2. **PSCMP** (`src/pscmp/`, binary `fomosto_pscmp2008a`) — Reads the PSGRN database plus a fault geometry input, discretizes the fault into point sources, and superposes Green's functions to compute time-dependent displacement, stress, tilt, geoid, and gravity changes at receiver locations.

Both programs read input filenames interactively from stdin at runtime.

Header files (`psgglob.h` / `pscglob.h`) contain all static array dimensions. To increase capacity (more layers, receivers, source points, time steps), edit those headers and recompile with `make clean && make`.

## Source Organization

- **PSGRN** (30 source files): `psgmain.f` is the entry point. The computation chain is: sublayer construction → Bessel function tables → wavenumber spectrum (propagator matrices for PSV/SH waves) → wavenumber integration → Green's function output. `psgmoduli.f` handles viscoelastic rheology (Burgers body), `psgsource.f` handles point dislocation sources.
- **PSCMP** (13 source files): `pscmain.f` is the entry point. `pscgrn.f` reads/interpolates the Green's function database, `pscdisc.f` discretizes fault planes, `pscout.f` writes output files. Coulomb stress computation is in `cmbfix.f` / `cmbopt.f`.
- `skip_comments.f` is shared between both programs (input file parsing).
- `examples/` contains Wenchuan earthquake input files for validation.

## Testing

There is no test suite. CI (Travis) only validates that the code compiles. Manual validation is done by running the example input files and inspecting output.

## Context

This is a Pyrocko fomosto backend — in production, Pyrocko's `fomosto` framework calls these binaries to manage Green's function databases. This repo itself contains only Fortran 77 with no Python code.
