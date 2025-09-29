# tsplib converter

This repository contains Python scripts that convert TSP, VRP and other variations using tsplib95 as backend, converting the files to a json format or creating a duckdb database. With it you may:

## core functionalities

### Convert tsplib files to json format and create duckdb database with instances and their characteristics

1. Convert tsplib files to json format (or other format, maybe yaml, parquet, etc) for easier parsing and data extraction.
2. Create a duckdb database with all the instances and their characteristics.

### Convert problems to tsp instances

1. Characterize problems with different extension types or parsing logics (e.g. Courdeau, Augerat, Solomon, etc) and convert them into ts instances.
2. Create a database with all the instances and their characteristics

- The scripts read TSPLIB files, extract relevant data, and save it in a more accessible format, i.e.

<https://neo.lcc.uma.es/vrp/vrp-instances/capacitated-vrp-instances/>
<https://neo.lcc.uma.es/vrp/vrp-instances/>
<http://vrp.atd-lab.inf.puc-rio.br/index.php/en/>
<https://www.mech.kuleuven.be/en/cib/op#autotoc-item-autotoc-2>
<https://neo.lcc.uma.es/vrp/vrp-instances/>
<https://neo.lcc.uma.es/vrp/vrp-instances/capacitated-vrp-instances/>
<https://neo.lcc.uma.es/vrp/vrp-instances/multiple-depot-vrp-instances/>
<https://neo.lcc.uma.es/vrp/vrp-instances/description-for-files-of-cordeaus-instances/>
<https://neo.lcc.uma.es/vrp/vrp-instances/multiple-depot-vrp-with-time-windows-instances/>
<https://neo.lcc.uma.es/vrp/vrp-instances/periodic-vrp-instances/>
<https://neo.lcc.uma.es/vrp/vrp-instances/vehicle-routing-problem-with-pick-up-and-deliveries/>
