


## Scalable PyTorch Geometric Temporal

This repository is an extension of [PyTorch Geometric Temporal](https://github.com/benedekrozemberczki/pytorch_geometric_temporal) designed specifially for memory efficiency, scalability, and distributed training. In particular, we make the following high-level open-source software contributions:

* Intergrated batching and sequence to sequence prediction into PGT-DCRNN
* Implemented Dask distributed data parallel DCRNN training
* Implemented Index-batching - a novel technique that significantly reduces data memory consumption and enables training on previously intratable datasets
* Implemented Index-GPU-batching: performs ST-GNN data preprocessing and training entirely in GPU memory
* Implemented Lazy-Index-Batching: distributes data across workers and performs index batching
* Implemented Dask- and index-preprocessing scripts for all tested datasets 

--------------------------------------------------------------------------------
**Reproducibility**

Examples used for testing our methods can be found in the `scalability_testing` folder. To recreate our experiments, begin by `cd scalability_testing/`

To run single GPU experiments, run the script with the dataset in its name (e.g. `python3 chicken_pox_main.py`). Each script uses the following command-line arguments:


| Argument            | Short Form | Type    | Default  | Description                                                 |
|---------------------|------------|---------|----------|-------------------------------------------------------------|
| `--mode`            | `-m`       | `str`   | `base`   | Specifies which version of the program to run. Supported options are 'base' and 'index'.            |
| `--gpu`             | `-g`       | `str`   | `False`  | Indicates whether data should be preprocessed and migrated directly to the GPU. |
| `--debug`           | `-d`       | `str`   | `False`  | Enables or disables debugging information.                 |



To run Dask distributed data parallel training, use the following command: `python3 pems_ddp.py <options>`. This script supports the following command-line arguments: 


| Argument             | Short Form | Type    | Default    | Description                                                              |
|----------------------|------------|---------|------------|--------------------------------------------------------------------------|
| `--mode`             | `-m`       | `str`   | `index`    | Specifies which version of the program to run. Valid options include 'dask', 'index', and 'dask-index'.                     |
| `--gpu`              | `-g`       | `str`   | `False`    | Indicates whether data should be preprocessed and migrated directly to the GPU. |
| `--debug`            | `-d`       | `str`   | `False`    | Enables or disables debugging information.                              |
| `--dist`             |            | `str`   | `False`    | Specifies if computation is distributed across multiple nodes. To do so, we leverage the command line to spawn the following Dask tools: scheduler, client, and workers. For an example, see TODO and for more details, see  [Dask's Documentation](https://docs.dask.org/en/latest/deploying-cli.html)      |
| `--npar`             | `-np`      | `int`   | `1`        | The number of GPUs or workers per node.                                 |
| `--dataset`          |            | `str`   | `pems-bay` | Specifies the dataset to use. Valid options include 'pems-bay', 'pems-all-la', and 'pems'.                                |


For both our single-GPU and DDP implementations, once training completes, there will be three files in the current directory:
* stats.csv: summary statistics about the overall training such as minimum validation MAE, overall runtime, maximum system memory usage, etc.
* per_epoch_stats.csv: each epochs runtime, training MAE, and validation MAE.
* system_stats.csv: per-second system and GPU memory usage.

---------------------------------------------------------
**Datasets**

We tested and compared model performance with the following PyTorch Geometric Temporal datasets to establish that index-batching has no negative impact on accurary and reduces memory footprint:

* England Chickenpox 
* PeMS-BAY
* Windmill-Large

And we employed the following two larger datasets to test single-GPU scalability and multi-GPU, multi-node distributed DDP training scalability:
* PeMS-All-LA
* The full PeMS dataset
---
---------------------------------------------------------
**Installation**

Clone our repository and change directory to the downloaded repository. Then run

```sh
pip3 install .
```

[pytorch-install]: https://pytorch.org/get-started/locally/
[pyg-install]: https://pytorch-geometric.readthedocs.io/en/latest/notes/installation.html


--------------------------------------------------------------------------------
**Our code**

While not an exhaustive list, these are the primary notable changes to the internal PyTorch Geometric Temporal code.

We modified the following PyTorch Geometric Temporal files:
* dcrnn.py: added a version of DCRNN that supports seq-to-seq prediction and batching
* windmilllarge.py: added seq-to-seq preproccessing options
* chickenpox.py: added seq-to-seq preproccessing options

We added the following files to the PyTorch Geometric Temporal package:
* dask_batching.py: implements Dask preprocessing and Dask-Pytorch compatible batching
* index_batching.py: implements index preprocessing and index-Pytorch compatible batching

--------------------------------------------------------------------------------

### Other Examples of Index-Batching
Before deciding to focus on extending Pytorch Geometric Temporal, we modified ST-LLM to support Dask DDP and index-batching, and our source code is [available on Github](https://anonymous.4open.science/r/DDP-Index-Batching-ST-LLM-83E9/README.md) (TODO after review period, update to non-anon link). 


**License**


- [MIT License](TODO: Add after review)
