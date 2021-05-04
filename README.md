# cde-in-box
`CDE in a box` is a collection of software applications which enables creation, storing and publishing of "Common Data Elements" according to the [CDE semantic model](https://github.com/ejp-rd-vp/CDE-semantic-model).

## Requirements
In order to use the cde-in-box solution you `have to` meet following requirements.

**User requirements (Person who is deploying this solution)**

* Basic knowledge on Docker​
* Basic GitHub knowledge​
* Awareness of CDE semantic model

**System requirements​ (Machine where this solution is being deployed)**

* Docker engine​
* Docker-compose application​

## Softwares used in CDE in a box
The image below gives an overview of softwares used in the `CDE in a box` solutions.

<p align="center"> 
    <a href="docs/images/components_overview.jpg" target="_blank">
        <img src="docs/images/components_overview.jpg"> 
    </a>
</p>

**Triple store:**
To store the `rdf` documents generated by the `CDE in a box` solution we need to have a triplestore which stores these document. In the `CDE in a box` solution we use graphDB as a triplestore. To know more about the graphDB triplestore please visit this [link](https://graphdb.ontotext.com)

**FAIR Data Point:**
To describe the content of your resource we need a `metadata provider` component. For the `CDE in a box` solution we use `FAIR Data Point` software that provides description (metadata) of you resource. To learn more about the FAIR Data Point please visit this [link](https://fairdatapoint.readthedocs.io/en/latest/)

## Other known CDE​ in a box solutions
In this section we list other known `CDE in a box` solutions.

**MOLGENIS CDE in a box**     
MOLGENIS EDC provider also provides a complete set of `CDE in a box` with EDC system. To learn more about MOLGENIS implementation of the `CDE in a box` solution please visit this [link](https://github.com/fdlk/cde-in-box/tree/feat/molgenis)

## How to use CDE in a box solution

To use CDE in box solutions clone this repository to your machine.

```sh
git clone https://github.com/ejp-rd-vp/cde-in-box
```

Once you have cloned this repository please follow the instructions below to properly configure the `CDE in a box` solution. 
## Instructions

### Configuring bootstrap services
#### GraphDB
The `docker-compose.yml` file in directory `cde-in-box/bootstrap` will setup up graphDB triple store and creates `fdp` and `cde` repositories in graphDB. These two repositories are used by other services in CDE in box so make sure that bootstrap services are property setup before you proceed further.
   
To run `docker-compose.yml` file in `cde-in-box/bootstrap` you need graphDB triple store free edition. Follow the steps below to get free edition of graphdb.


**Step 1:** GO to this [url](https://www.ontotext.com/products/graphdb/graphdb-free/) and registry to download GraphDB free edition.


**Step 2:** The download will be sent to your email. From the email follow link to download page and `click` on "Download as a stand-alone server". This step will download "graphdb-free-{version}-dist.zip" file to your machine.


**Step 3:** Move "graphdb-free-{version}-dist.zip" file to the following location

```sh
mv graphdb-free-{version}-dist.zip cde-in-box/bootstrap/graph-db
```

**Step 4:** If your `graphdb version` is different from `9.7.0` then change the version number of graph DB in the docker-compose file.

```sh
graph_db:
    build:
      context: ./graph-db
      dockerfile: Dockerfile        
      args:
        version: 9.7.0
```
#### Running bootstrap services
Once you have done above configurations you can run `bootstrap` services by running `docker-compose.yml` file in `cde-in-box/bootstrap` directory.

```sh
docker-compose up -d
```

If the deployment is successful then you can access the graphDB by visiting the following URL.

**Note:** If you deploy `CDE in a box` solution in your laptop then check only for **local deployment** url.

| Service name | Local deployment | Production deployment |
| --- | --- | --- |
| GraphDB | [http://localhost:7200](http://localhost:7200/) | http://SERVER-IP:7200 |

By default GraphDB service is secured so you need credentials to login to the graphDB. Please find the default graphDB's credentials in the table below.

| Username| Password |
| --- | --- |
| `admin` | `root` |

### Configuring metadata services
#### FAIR Data Point
The `docker-compose.yml` file in directory `cde-in-box/metadata` will setup up `FAIR Data Point` and connects FAIR Data Point to triple store created in the bootstrapping step.



**Step 1:** Before you run metadata services make sure that graphDB triple store is up running. You can check by going to following url. For local deployment (in your laptop) `http://localhost:7200` for production deployment (in your server) `http:server_ip:7200`



**Step 2:** Check if `fdp` repository is available in the graphDB triple store.


#### Running metadata services
Once you have done above checks you can run `metadata` services by running `docker-compose.yml` file in `cde-in-box/metadata` directory.

```sh
docker-compose up -d
```

If the deployment is successful then you can access the FAIR Data Point by visiting the following URL.

| Service name | Local deployment | Production deployment |
| --- | --- | --- |
| FAIR Data Point | [http://localhost:8080](http://localhost:8080) | http://SERVER-IP:8080 |

**Note:** If you deploy `CDE in a box` solution in your laptop then check only for **local deployment** url.

In order to add content to the FAIR Data Point you need credentials with write access. Please find the default FAIR Data Point's credentials in the table below.

| Username| Password |
| --- | --- |
| `albert.einstein@example.com` | `password` |

### Configuring data transformation services

#### Preparing input data

The transformation services take `CSV` as input files. We provide `CSVs` with example data and `YARRRML` templates for each CDE module [here](https://github.com/ejp-rd-vp/CDE-semantic-model-implementations/tree/master/YARRRML_Transform_Templates).
The `YARRRML` templates are always loaded from GitHub automatically, so they stay up-to-date as we change the models in EJP-RD, but the `CSV` files must be added by the user.


#### Configuring configuration and data folders 


**Step 1:** Folder structure

Make sure the following folder structure, relative to where you plan to keep your pre and post-transformed data, is available:
```
        .
        ./data/   
        ./data/mydataX.csv  (input csv files, e.g. "height.csv")
        ./data/mydataY.csv...
        ./data/triples/  (output FAIR data ends up here)
        ./config/
        ./config/XXXX_yarrrml_template.yaml (XXXX is a one-word tag of the "type" of data, e.g. "height")
``` 


**Step 2:**  Running data transformation services

Once you have done the above configurations you can run `data transformation services` setup by running `docker-compose.yml` file in `cde-in-box/cde-ready-to-go` directory.
**THE docker-compose IN THE SAME FOLDER THAT CONTAINS THE ./data/triples and ./config and subfolders**

```sh
docker-compose up -d
```


**Step 3:** Input CSV files

Put an appropriately columned `XXXX.csv` into the `cde-in-box/cde-ready-to-go/data`. Please look into [this](https://github.com/ejp-rd-vp/CDE-semantic-model-implementations/tree/master/YARRRML_Transform_Templates) github repository for examples of CDEs `CSV` files.


**Step 4:** Input YARRRML templates

The `YARRRML` templates are always loaded from GitHub automatically on step 5, so they stay up-to-date as we change the models in EJP-RD.

Make sure the `YARRRML` templates files are matching your `CSV` files names `XXXX_yarrrml_template.yaml` into the `cde-in-box/cde-ready-to-go/config` folder. Please look into [this](https://github.com/ejp-rd-vp/CDE-semantic-model-implementations/tree/master/YARRRML_Transform_Templates) github repository for CDEs `YARRRML` templates.


**Step 5:**  Executing transformations

Call the url:  http://localhost:4567 or http://SERVER-IP:4567  to trigger the transformation of each CSV file, and auto-load into graphDB (this will over-write what is currrently loaded!  We will make this behaviour more flexible later)
**Note:** If you deploy `CDE in a box` solution in your laptop then check only for **localhost** url.

**There is sample data (height.csv) in the "cde-ready-to-go/data" folder that can be used to test your installation.**


### How to modify semantic model in data transformation service

YARRRML is one the core technology which has been used in our data transformation service. If you like to extend the CDE semantic model or add other semantic model to describe your data then, you have to provide custom YARRRML templates to the data transformation service. To learn more about building custom YARRRML templates please try [matey webapp](https://rml.io/yarrrml/matey/).
