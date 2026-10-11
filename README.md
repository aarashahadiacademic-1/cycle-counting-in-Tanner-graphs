# Tanner-graph cycle-counting reproducibility bundle

This repository contains the C++ implementations, fixed graph inputs, graph manifests, fresh benchmark records, and scripts used to generate the combined Section 4 table. It has **24 measurement rows**: 13 girth-only rows and 11 multiple-cycle-length rows. The manuscript table itself is not stored; `build_combined_table.py` generates its LaTeX from `results/measurements.csv`.

## Reproduce the included table

Requirements: Python 3 and a C++17 compiler (`g++`). All scripts use the Python standard library.

```sh
python3 verify_results.py
python3 build_combined_table.py
```

The first command checks that all 24 aggregate rows derive from the 1,804 per-run records in `results/raw_timings.csv`, verifies graph hashes, and checks the Exact, KB, IMPA, Hybrid, and MARE aggregations. The second prints the complete LaTeX table and writes `results/combined_table.tex` locally. That generated TeX file is ignored by Git.

## Re-run the experiments

```sh
python3 generate_added_graphs.py
python3 run_complete_benchmark.py
python3 verify_results.py
python3 build_combined_table.py
```

`generate_added_graphs.py` recreates D16r, D32r, and P2400 with fixed seeds and writes their construction and SHA-256 records to `added_graphs_manifest.json`. The benchmark runner compiles all three counting programs, validates counts across independent algorithms, and writes fresh aggregate and per-run data under `results/`. A fresh run overwrites the archived measurements; preserve a copy first if you need this exact table.

## Algorithms and measurements

- `counter.cpp`: local exact cycle counter and Karimi–Banihashemi (KB) message-passing baseline. Exact and KB are each timed for seven repetitions; medians are reported.
- `impa_count.cpp`: Li–Lin–Abdel-Ghaffar IMPA implementation. It is timed for seven repetitions; the median is reported.
- `chunk_hybrid.cpp` and `bench_core.cpp`: bounded-microtask interleaved exact/sampling method. It uses ε=0.08, r=101, Q=4096 credits, and 100 seeded runs. Hybrid runtime and MARE are means over those runs.
- `run_complete_benchmark.py`: complete 24-row benchmark runner.
- `build_combined_table.py`: table renderer from the measurement CSV.
- `verify_results.py`: aggregation and consistency checks.
- `graphs/`: exact edge-list inputs used in the archived run. `graph_manifest.json` records sizes, degrees, girth, construction notes, and hashes.

The archived measurements were run in a shared AMD EPYC 9V74 container with `g++ 13.3.0 -O3`. The LaTeX text previously described `g++ 14.2`; update that description if this fresh run is used in the paper. Timings are machine- and load-dependent, so new runs are expected to produce different times while preserving counts and fixed-seed sampling outcomes.
