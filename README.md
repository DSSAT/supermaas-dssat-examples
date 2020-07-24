# SuperMaaS Example Containers for DSSAT-pythia

These are the *Expert Modeler* supplied files and the Dockerfiles used to run [DSSAT-pythia](https://github.com/DSSAT/pythia/tree/develop "DSSAT-pythia development branch").

## Prerequisites

* Download and unpack the large data repositories.
    - *Base:* global soil databases, other generic shipped data.
    - *Ethiopia:* shape files, raster files, and templates for Ethiopia.
    - *Weather:* DSSAT-formatted weather files for Ethiopia.
* Docker

## Structure

This repository contains what _could_ be the results of multiple model registrations. The crop for the base model is Maize.

* `baseline` - The baseline model to which all other models in this example set should be compared against. Contains 4 management practices (irrigated/high nitrogen inputs, rainfed high N, rainred low N, rainfed 0N).
* `subsidy` - An example of multiple fertilizer subsidies, which is additional fertilizer compared to the baseline. values are 10,25,50 kg/ha.
* `forecast` - An example of running a forecast (in this case a backcast based on 2018 data which proxy years from 1984-2017).
* `suitability` - An example of planting Teff where Maize is grown. Uses the (unregistered) Teff model base and replaces the rasters for harvest area to the Maize areas.

The `includes` directory is custom cultivar files which would be provided by the Expert Modeler.

Files under the above directories are mounted under the `/userdata/` directory inside the image.

### Assumed directory structure

For the purposes of this documentation, the following directory structure is assumed.

```
.
├── baseline 
├── data
│   ├── base
│   ├── ethiopia
│   └── weather 
├── forecast
├── includes
├── outputs
├── subsidy
└── suitability
```

## Customizing the JSON files

Because we don't know the parameters of the system running DSSAT-pythia, the following should be modified inside the JSON file prior to building the image.
* `cores` - Number of cores to use
* `threads` - Number of threads to use (_Some testing has shown that using 1/2 cores for threads works most efficiently_)
* `sample` - Number of valid pixels to run per management. (_This can be removed to run the whole country_)

## Building the images

For these examples, we can execute `docker build` from within the above directory structure.

```
docker build -t supermaas-dssat-eth-baseline -f baseline/Dockerfile .
```

## Executing the images

```
docker run --rm  -it -v ${PWD}/data:/data -v ${PWD}/outputs:/outputs supermaas-dssat-eth-baseline --all /userdata/eth_maize_baseline.json
``` 

All outputs are generated in the `outputs/` directory. Each image has a different output directory underneath `outputs/` in this example. The csv file at the `outputs/<image>/` directory is a trimmed and aggregated file of all the CSV files underneath each directory.
