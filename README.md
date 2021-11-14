# gqueue

gqueue is a CLI (command line interface) tool that computes carbon footprint of HPC computations on cluster running slurm. 
It follows the methodoloty laid out in:
 - Lannelongue, L., Grealey, J., Inouye, M., Green Algorithms: Quantifying the Carbon Footprint of Computation. Adv. Sci. 2021, 8, 2100707. https://doi.org/10.1002/advs.202100707 

and used by http://green-algorithms.org.


# Usage


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
 - `RAM_per_core`: in GB, this is the amount of RAM on a node divided by the number of cores of the given node. 
