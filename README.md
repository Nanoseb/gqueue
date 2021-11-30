# gqueue

gqueue is a CLI (command line interface) tool that computes carbon footprint of HPC computations on clusters running slurm. 
It follows the methodology used by http://green-algorithms.org and laid out in:
 - Lannelongue, L., Grealey, J., Inouye, M., Green Algorithms: Quantifying the Carbon Footprint of Computation. Adv. Sci. 2021, 8, 2100707. https://doi.org/10.1002/advs.202100707 


Here is an example output:
``` bash
$ gqueue -a
Config filename: iridis5.json
Carbon intensity: 253.19 gCO2e/kWh (GB)

Partition  TDP per core  RAM per core  Footprint per CPU.h
──────────────────────────────────────────────────────────
batch*     6.25 W        4.8 GB        3.40 gCO2e/(CPU.h) 
largejobs  6.25 W        4.8 GB        3.40 gCO2e/(CPU.h) 
amd        4.84375 W     4.8 GB        2.80 gCO2e/(CPU.h) 

Directory  Job name  Job-ID  Partition  Cores  Status      CO2          Total CO2  
───────────────────────────────────────────────────────────────────────────────────
G3         TEST_400  761980  batch      400    ███████───  59.4 kgCO2e  81.6 kgCO2e
G1         TEST_400  725609  batch      400    PENDING     -            81.6 kgCO2e
G2         TEST_400  725610  batch      400    PENDING     -            81.6 kgCO2e

Carbon footprint over the last:
1 day: 1.4 kgCO2e
7 days: 108.3 kgCO2e
30 days: 641.2 kgCO2e
90 days: 2.7 TCO2e

```


# Usage

```
usage: gqueue [-h] [-p] [-j] [-r] [-c CLUSTER] [-u USER]
              [--carbon-intensity CARBON_INTENSITY] [-a]
              
CLI tool to compute CO2 emissions of HPC computations following green-algorithms.org 
methodology on slurm systems.

optional arguments:
  -h, --help            show this help message and exit
  -p, --partition       Displays partitions information
  -j, --job             Displays footprint of jobs in queue
  -r, --history         Displays footprint of past jobs over the last 1, 7, 30 and 90 days
  -c CLUSTER, --cluster CLUSTER
                        Cluster .json file to use, if omited autodetection is used
  -u USER, --user USER  User to show analysis about, if omited the current user is used
  --carbon-intensity CARBON_INTENSITY
                        Overwrites carbon internsity information from cluster data, specify 
                        value in gCO2/kWh
  -a, --all             Displays everything, equivalent to -p -j -r
  ```



# Add new cluster information
Each cluster information is stored in a .json file in the `cluster_data` folder. One file correspond to a single cluster and is defined as follow:

```json
{
  "hostnames": [ "cyan51.cluster.local", "cyan52.cluster.local" ],
  "country": "GB",
  "carbon_intensity": 253.19,
  "default_partition": "batch",
  "partitions": [
    {
      "partition_names": ["batch", "largejobs"],
      "TDP_per_core": 6.25,
      "RAM_per_core": 4.8
    },
 {
      "partition_names": ["amd"],
      "TDP_per_core": 4.84375,
      "RAM_per_core": 4.8
    }
  ]
}
```
- `hostnames`: this is the list of hostnames of the cluster's login nodes. They are used to automatically select the right json file.
- `carbon_intensity`: in gCO2e/kWh, this is the carbon footprint of producing a quantity of energy. It is country/grid specific and can be retrieved from https://github.com/GreenAlgorithms/green-algorithms-tool/blob/master/data/CI_aggregated.csv
- `country`: two letter location id used for reference only.
- `default_partition`: name of the default partition, if data isn't provided for a specific partition in the `partitions` list, the `default_partition` is used.
- `partitions`: partitions often have different hardware in a cluster, hence, each of them is defined separatly.
  - `partition_names`: list of partition names sharing a given hardware.
  - `TDP_per_core`: in W, this is the TDP of the node CPU divided by its number of cores. The CPU TDP can be retrieved from the CPU manufacturer website and needs to be divided by its number of cores.
  - `RAM_per_core`: in GB, this is the amount of RAM available on a node divided by the number of cores of the given node. 
