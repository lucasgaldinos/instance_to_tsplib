## AI agent guide for this repo (tsplib95-based converter)

This workspace vendors tsplib95 under `src/tsplib95` and includes tests in `tests/`. Goal (see `README.md`): parse TSPLIB/VRP instances and convert them into JSON or a DuckDB database. Datasets live under `datasets/`.

Architecture overview

- `models.py`: `Problem` base and `StandardProblem` with metaclass `FileMeta` mapping TSPLIB keywords to fields. Key methods: `parse/read/load`, `as_name_dict()/as_keyword_dict()`, `get_nodes()/get_edges()`, `get_graph(normalize)`, `get_weight(i,j)`, `trace_tours`.
- `fields.py`: TSPLIB sections as fields (e.g., `NAME`, `TYPE`, `DIMENSION`, `EDGE_WEIGHT_SECTION`, `NODE_COORD_SECTION`, `TOUR_SECTION`).
- `transformers.py`: Reusable text↔data transformers (`ListT`, `MapT`, `UnionT`, `FuncT`, `NumberT`) with separator/terminal handling and contextual errors.
- `distances.py`: `TYPES` maps `EDGE_WEIGHT_TYPE` to functions (`EUC_2D`, `MAN_2D`, `CEIL_2D`, `GEO`, `ATT`, `XRAY*`).
- `matrix.py`: `TYPES` maps `EDGE_WEIGHT_FORMAT` to matrix classes (`FULL_MATRIX`, `UPPER_DIAG_ROW`, etc.).
- `loaders.py`: Thin façade (`load/read/parse`) with optional `problem_class` and `special` function.
- `cli.py`: Click command `summarize` printing columns such as `DIMENSION`, `TYPE`, `EDGE_WEIGHT_TYPE`, `EDGE_WEIGHT_FORMAT`.

Project structure & current state

- `datasets/problems/{tsp,vrp,atsp,hcp,sop,tour}/` - Raw problem files organized by type
- `datasets/zips/` - Downloaded archived datasets
- **Missing**: DuckDB dependencies & converter scripts (per README TODOs - need `uv add duckdb`)
- **Missing**: `datasets/parsed_datasets/json` output directory (target for JSON conversions) and `datasets/parsed_datasets/db`

Conventions and gotchas

- Field mapping lives on `StandardProblem` (e.g., `dimension = F.IntegerField('DIMENSION')`); `FileMeta` builds name↔keyword maps automatically.
- Defaults are lazy: `Problem.__getattribute__` uses `Field.get_default_value()`; to serialize only non-defaults, prefer `as_name_dict()` / `as_keyword_dict()`.
- Weight source: if `is_explicit()` use a matrix from `EDGE_WEIGHT_SECTION`; otherwise use `edge_weight_type` from `distances.TYPES`. For `TYPE=SPECIAL`, pass a callable via `special=` to `loaders.load`.
- Indices/tours: node indices are typically 1-based; `Matrix` respects `min_index`. `ToursField` uses `-1` as a required terminator.
- Graphs: `get_graph(normalize=True)` yields 0-based sequential node IDs; node attrs: `coord`, `display`, `demand`, `is_depot`; edge attrs: `weight`, `is_fixed`.

Developer workflow

- Setup: Use `uv` for venv management (already present). Install missing deps: `uv add duckdb tsplib95 networkx click tabulate deprecated pytest`.
- Runtime deps: `networkx`, `click`, `tabulate`, `deprecated`; tests use `pytest`. Python ≥ 3.11.
- Run tests from repo root: `pytest -q` or target a single test like `tests/test_models.py::TestStandardProblem::test_get_graph`.
- CLI example: `python -m tsplib95.cli summarize datasets/problems/tsp/gr17.tsp` to inspect parsed metadata.
- Current development focus: Create converter scripts to parse `datasets/problems/` and output JSON to `datasets/parsed_datasets/`.

Typical usage

- Load/inspect: `from tsplib95 import loaders; p = loaders.load('datasets/problems/tsp/gr17.tsp'); p.get_weight(1, 2); p.as_keyword_dict(); G = p.get_graph(normalize=True)`.
- SPECIAL distance: define `def w(a, b): ...` where `a`/`b` are coords, then `p = loaders.load(path, special=w)`.
- Convert to JSON: build from `p.as_name_dict()` (or `as_keyword_dict()` if mirroring TSPLIB keywords). For tours, remember `-1` termination.

Extending for new VRP datasets

- Parse source files into a `StandardProblem` (populate `dimension`, `node_coords`, `demands`, `depots`, `edge_weights` or provide `special`). Reuse `get_weight`, `get_graph`, and `as_*_dict` for downstream conversion.
- Target VRP formats include Cordeau instances (`pr01`-`pr20`) for MDVRP/VRPPDTW - see `docs/vrps.md` for detailed analysis of problem characteristics and recommendations.

Where to start reading

- `src/tsplib95/models.py` (weight/graph), `fields.py` (available sections), `transformers.py` (parsing rules), `distances.py` and `matrix.py` (selection logic), and `tests/` (behavioral examples, sample inputs under `tests/data`).
