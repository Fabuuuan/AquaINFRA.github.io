# User Guide: Creating a Data-to-Knowledge Package

**Note:** This document is a draft. Please send your questions, comments, and suggestions to m.konkol[at]52north.org.

### Problem statement

Publishing and reusing reproducible research is challenging. Users still need to make considerable efforts to understand the data and analysis code before they can reuse parts of the analysis in other contexts.

### Solution

Based on a reproducible basis (code + data + computational environment), further applications including virtual labs, web API services, and workflows are built to provide an entry point to the analysis and encourage reuse.

### Target audience

Users who have an analysis pipeline and would like to create a D2K-Package out of it.

---

## What is a Data-to-Knowledge Package?

The Data-to-Knowledge Package (D2K-Package) is a framework designed to enhance the reusability of computational research outputs by integrating key digital research assets. It addresses challenges in reproducibility by linking essential components, including data, code, virtual labs, web API services, and computational workflows. Unlike traditional research compendiums, the D2K-Package does not duplicate data but instead provides structured links to existing resources, reducing redundancy and ensuring synchronization.

At its core, the D2K-Package comprises a reproducible basis — data and a structured code toolbox — enabling the derivation of additional research components, such as virtual labs, web API services, and computational workflows. By offering self-contained reusable functions in a containerized toolbox, the D2K-Package facilitates adaptation in various research contexts. The D2K-Package also supports computational environments, mitigating technical barriers for users with diverse expertise levels.

A primary goal of the D2K-Package is to promote the reuse of research by allowing researchers to inspect, execute, and modify analysis workflows efficiently. This approach streamlines the research cycle, fosters collaboration, and accelerates knowledge generation. Moreover, its metadata structure ensures findability and accessibility, aligning with the FAIR Principles. Ultimately, it provides a scalable solution for improving transparency, credibility, and the impact of reproducible research. Before you start creating your own D2K-Package, feel free to visit a demo D2K-Package to get an impression


**Visit the demo:**
- [AquaINFRA Interaction Platform](https://aquainfra.dev.52north.org/search?searchterm=data-to-knowledge+package&pageSize=20&sort=&dataProvider=zenodo)
- [Zenodo Example](https://doi.org/10.5281/zenodo.14830529)

---

## How can I create a D2K-Package?

### Technologies
In this user guide, we use the following technologies to create a D2K-Package:

- [**R**](https://www.google.com/search?client=firefox-b-d&q=r+tutorial)  and [**Python**](https://docs.python.org/3/tutorial/index.html) to write the analysis code  
- [**GitHub**](https://docs.github.com/en/get-started/start-your-journey/about-github-and-git) to host the toolbox  
  - *Note:* GitLab is possible, too, but at some point you will need to create a Pull Request to the Galaxy platform, which is only possible via GitHub.  
- [**Docker**](https://www.docker.com/101-tutorial/) to containerize the source code  
- [**MyBinder**](https://mybinder.readthedocs.io/en/latest/howto/index.html) to provide the virtual lab  
- [**OGC API Processes**](https://opengeospatial.github.io/e-learning/ogcapi-processes/text/basic-main.html) to provide OGC API Web Services based on [**pygeoapi**](https://dive.pygeoapi.io/)  
- [**OGCAPIProcess2Galaxy tool**](https://github.com/AquaINFRA/OGC-API-Process2Galaxy)   
- [**Galaxy**](https://training.galaxyproject.org/) to develop and run workflows 
- [**RO-Crate**](https://www.researchobject.org/ro-crate/tutorials) to create the D2K-Package  
- [**Zenodo**](https://help.zenodo.org/docs/get-started/quickstart/) to store the materials  

You will need access to a server to deploy the OGC API Process using, for instance, pygeoapi. If you are a member of the AquaINFRA project and would like to deploy your processes on the AquaINFRA server, please get in touch with us (<mail_address>).

---
### Step 1: Linking to the input data

A D2K-Package links to all input datasets that are needed to run the analysis pipeline. Technically, a D2K-Package can link to any data that is publicly available via a URL, but never to data that is stored locally on your laptop. If you’re using private or sensitive data, linking to dummy data is recommended (state that clearly!), so other users can still run the analysis and see how the input data has to look like. Conceptually, the D2K-Package is designed to promote reuse. Therefore, the data should adhere to at least some of the [FAIR Principles](https://doi.org/10.1038/sdata.2016.18) and be openly accessible. This implies that the data should be accompanied by detailed metadata to enhance its findability.

Ideally, the links to the datasets are permanent identifiers (e.g., DOIs) and directly lead to downloadable data files that can be passed to the functions in the toolbox we will create later. In another scenario, if the data comes from an online service that provides an API requiring query parameters (e.g., OGC API Features service), the D2K-Package should include the full URL with query parameters. Be aware that using URLs instead of DOIs might limit reproducibility as the URL or data might change making the original data inaccessible and the results unreproducible! 
To ensure interoperability, data should be stored in text-based formats whenever possible. Acceptable formats include, e.g., CSV and JSON. However, binary formats (e.g., GeoPackage, Shapefile) are acceptable if they follow an open specification. Zipped datasets are also allowed. A D2K-Package can only link to data released under an open license to ensure reusability, preferably CC0. Datasets published under restrictive licenses are excluded, even if linking is technically possible. This includes licenses that restrict modifications, transformations, or derivative works (e.g., CC-BY NoDerivatives).
 
### Example data:
- [Input data 1](https://vm4072.kaj.pouta.csc.fi/ddas/oapif/collections/lva_secchi/items?f=json&limit=3000&bbox=21.133617932884032%2C56.23417469881417%2C26.748969313655106%2C57.99442871695254): The URL points to data provided by an OGC API Features service and contains all necessary parameters to query the dataset.
- [Input data 2](https://maps.helcom.fi/website/MADS/download/?id=67d653b1-aad1-4af4-920e-0683af3c4a48): The URL points to a page where users first need to accept the data usage disclaimer. Afterwards, they can copy the URL to the actual data. 


### To-do if the data is generated by you:
- [x] Use open data formats or binary formats following an open specification.
- [x] Publish the data in a repository where you can get a DOI and add metadata.
- [x] Release the data under an open and permissive license.


### To-do if you're using third-party data:
- [x] Collect the links to all input datasets (see, e.g., the example data above).

**Note:** The To-dos combine technical and conceptual requirements considering open research practices. For instance, technically, you could simply upload your input data to Dropbox and generate a shared URL. However, since this approach is not good scientific practice and FAIR at all, it is not considered here, though technically possible.
If you’re using third-party data, it can happen that the data does not fully comply with the FAIR principles. The example data, for instance, is not made available under a permanent identifier. In this case you have two options:
Option 1: Use the link as it is and consider that the results might not be reproducible.
Option 2: If the license permits, upload the data to a repository, such as Zenodo.


---

## Step 2: Preparing the toolbox

Includes:
- Containerized **analysis code** (Python or R)
- **Self-contained functions** (input → processing → output)
- A defined **computational environment**

### Function design

Each function:
- Takes **URLs** as inputs
- Outputs a **URL**
- Is **independent**
- Executed via command line, e.g.:

```bash
R_SCRIPT "script.R" "URL_input_1" "URL_input_2" "param_1" "param_2" "output_filename"
