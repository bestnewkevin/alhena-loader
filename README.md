# Alhena Loader

This project contains Elasticsearch loaders for Alhena

Using the loaders will populate Elasticsearch with:

- QC metrics data for each cell
- Read (bin) copy number data for each cell
- Segment copy number data for each cell
- GC bias metrics for each cell
- Analysis metadata (sample ID, library ID)

## Input

Alhena was built to support datasets generated by the [single cell pipeline](https://github.com/shahcompbio/single_cell_pipeline).

The loader assumes that all data files are kept in the same directory. Below is a description of the files.

### metadata.json

This must be kept in the top of the directory. This contains relevant metadata for the dashboard.

Required columns:

- sample_id
- library_id
- description

### Data files

Alhena currently supports four different data types: QC, bins (reads), segments, and GC bias.

We use [SCGenome](https://github.com/shahcompbio/scgenome) to read the data, which assumes that `align`, `annotation`, and `hmmcopy` are subdirectories, and that there are metadata.yaml files in each of those directories. The pipeline should already return data in this structure.

## System Requirements

- Python 3

## Setup Virtual Environment

Clone Alhena's database loader repository:

```
git clone https://github.com/shahcompbio/alhena-loader
```

Create and start a new python3 environment

```
python3 -m venv <!!! /path/to/new/virtual/environment>

source <!!! /path/to/new/virtual/environment>/bin/activate
```

Install dependencies

```
pip install -r requirements.txt
```

You will need to make sure these are contained in your `bash_profile` (replace !!! with the username and password you generated for the Elasticsearch instance)

```
export ALHENA_ES_USER=<!!! username>
export ALHENA_ES_PASSWORD=<!!! password>
```

## Loading Data

To load one analysis into an ElasticSearch instance at `localhost:9200`, you can run the following command

```
python alhena_cli.py load-analysis --id <dashboard_id> <path/to/data/directory>
```

Additional (optional) options:

- `--host` : ElasticSearch host. Defaults to `localhost`
- `--port` : ElasticSearch port. Defaults to `9200`

```
python alhena_cli.py --host <ES_host> --port <ES_port> load-analysis --id <dashboard_id> <path/to/data/directory>
```

At the end, you should expect:

1. A new index called `<dashboard_id>_qc`
2. A new index called `<dashboard_id>_segs`
3. A new index called `<dashboard_id>_bins`
4. A new index called `<dashboard_id>_gc_bias`
5. One additional record in `dashboard_entry`

## Deleting data

To delete data:

```
python alhena_cli.py --host <ES_host> --port <ES_port> clean-analysis <dashboard_id>
```

If you're interested in reloading data, the loading function has a reload flag:

```
python alhena_cli.py --host <ES_host> --port <ES_port> load-analysis --reload --id <dashboard_id> <path/to/data/directory>
```
