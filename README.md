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
 
#### Example data:
- [Input data 1](https://vm4072.kaj.pouta.csc.fi/ddas/oapif/collections/lva_secchi/items?f=json&limit=3000&bbox=21.133617932884032%2C56.23417469881417%2C26.748969313655106%2C57.99442871695254): The URL points to data provided by an OGC API Features service and contains all necessary parameters to query the dataset.
- [Input data 2](https://maps.helcom.fi/website/MADS/download/?id=67d653b1-aad1-4af4-920e-0683af3c4a48): The URL points to a page where users first need to accept the data usage disclaimer. Afterwards, they can copy the URL to the actual data. 


#### To-do if the data is generated by you:
- [x] Use open data formats or binary formats following an open specification.
- [x] Publish the data in a repository where you can get a DOI and add metadata.
- [x] Release the data under an open and permissive license.


#### To-do if you're using third-party data:
- [x] Collect the links to all input datasets (see, e.g., the example data above).

**Note:** The To-dos combine technical and conceptual requirements considering open research practices. For instance, technically, you could simply upload your input data to Dropbox and generate a shared URL. However, since this approach is not good scientific practice and FAIR at all, it is not considered here, though technically possible.
If you’re using third-party data, it can happen that the data does not fully comply with the FAIR principles. The example data, for instance, is not made available under a permanent identifier. In this case you have two options:
Option 1: Use the link as it is and consider that the results might not be reproducible.
Option 2: If the license permits, upload the data to a repository, such as Zenodo.


---

### Step 2: Preparing the toolbox


The toolbox contains containerized analysis code (e.g., R or Python) used to generate research results. It includes the code, structured into self-contained reusable functions, and a specification of the computational environment.

#### Creating reusable functions

Each function should perform a distinct task (e.g., processing, analyzing, or visualizing data) following an input-processing-output logic, meaning that they receive data (e.g., a table or figure) and parameter configurations as inputs, process the data, and produce outputs (e.g., a modified table or figure). The number of functions in a toolbox varies depending on the complexity of the analysis. At a minimum, a single function can encapsulate the entire analysis, but greater reusability is achieved by dividing the workflow into multiple functions that users can call individually and sequentially. Even simple statistical visualizations should be separated into at least two functions: one for statistical computations and another for plotting.
Functions should be designed to accept data links (i.e., URLs) as inputs and return URLs as outputs. In this way, a toolbox function reads an input dataset from a URL and generates an output URL pointing to the output dataset, which can then serve as input for the next function in the workflow. Furthermore, toolbox functions are designed to be independent, meaning they can be called individually. The functions should be stored in separate files and be executable from the outside, e.g., by using command-line-based tools and commands in the form of: 

```
R_SCRIPT "script.R"  "URL to input dataset_1" "URL to input dataset_2"
"parameter_1" "parameter_2" "output filename"
```

For a guide on how to create such reusable functions, check these [slides](https://docs.google.com/presentation/d/1PUlmOenUNQV0RKG_rQMBt8oku6Tww5uNXPEpcmyNdx0/edit?usp=sharing).

#### Defining the computational environment

To ensure that the original computational environment can be recreated, the toolbox includes a specification of the runtime (e.g., R version) and a list of required libraries with their versions. All you need to do is create a folder called .binder in the toolbox and create the configuration files defining the computational environment runtime.txt and install.R to that folder. In the case of python, do the same with the environment.yml (or requirements.txt + runtime.txt).

**Recommendation for R users**: Instead of creating the runtime.txt and install.R files including libraries maintained by CRAN, we recommend using an environment.yml and install the libraries via Conda (see also here). Installations via [Conda](https://anaconda.org/conda-forge/r-base#) are much faster. 

#### Containerizing the materials
We recommend using Docker to containerize both the source code and the computational environment. The Dockerfile should be designed to allow users to execute individual functions by specifying the function name as a configuration parameter alongside other input parameters:

```
docker run -it -v ./in:/in -v ./out:/out -e R_SCRIPT="script.R" 
daugava-workflow-image -- "URL to input dataset_1" "URL to input dataset_2" 
"parameter_1" "parameter_2" "output filename"
```

**Example toolbox**: [Toolbox](https://github.com/AstraLabuce/aquainfra-usecase-Daugava/tree/containerize) containing the functions ( under /src), the computational environment (under /.binder), and the Dockerfile

#### To-do if the data is generated by you:
- [x] Fork the toolbox template repository (doesn’t exist yet!).
- [x] Create reusable functions and store each function in a separate file under /src.
- [x] Specify the computational environment under /.binder.
- [x] Adapt the Dockerfile if needed.
- [x] Publish the toolbox on Zenodo to get a DOI (see [GitHub → Zenodo integration](https://docs.github.com/en/repositories/archiving-a-github-repository/referencing-and-citing-content)).

### Step 3: Creating to a virtual lab
If you successfully managed to create a Toolbox as described in Step 2, you can easily create a virtual lab. Copy the URL of the repository and paste it to the input field of the MyBinder web application. You can also specify the branch or commit if necessary. Based on this information, MyBinder creates a Dockerfile (not the same as the one in the toolbox), builds the image, and launches the container where users can engage with the code more deeply in a ready-to-use virtual lab providing the corresponding programming environment (in this case RStudio). The resulting Binder can be accessed and shared via a URL with other users and thus become part of a D2K-Package. For more detailed instructions on using the Binder service, visit their excellent [how-to guide](https://mybinder.readthedocs.io/en/latest/howto/index.html) and [get-started introduction](https://mybinder.readthedocs.io/en/latest/introduction.html).

#### Example virtual lab: 
- [Computational environment](https://github.com/AstraLabuce/aquainfra-usecase-Daugava/tree/containerize/.binder)
- [MyBinder](https://mybinder.org/v2/gh/AstraLabuce/aquainfra-usecase-Daugava/containerize) 

#### To-do:
- [x] Copy the repository URL and paste it to the MyBinder UI.
- [x] Copy the resulting MyBinder URL and add a badge to your repository.

### Step 4: Providing a web API service
A web API service allows users to send requests to that service and receive a response via HTTP. Based on this request-response mechanism (also known as client-server communication), the developer of a D2K-Package can expose the functions in a toolbox by setting up a server and deploying a web API service. The concept of a toolbox can be seamlessly integrated into this mechanism since its functions are encapsulated, containerized, and well-defined regarding input and output. The web API service acts as a wrapper that enables immediate reuse of a toolbox function. The wrapper takes the input information sent by the user (i.e., data, parameter configuration), executes the function, and outputs the result as part of the response for further processing in the user’s source code. To achieve that, the wrapper transforms the information in the request (i.e., input data, parameter configuration) into a docker command that runs the container and the corresponding function in that container. Every function in the toolbox is wrapped in such a way and has a dedicated endpoint (OGC API Process) that can be reached via a URL and is in charge for running the container. As a result, users can develop their analysis script and include HTTP requests to that web API service including the necessary input information. The output of the executed function (a link to a table or a figure) can then be sent back as part of the response for further use in the code. Here, we also see a key advantage of passing URLs to datasets instead of sending the actual dataset: The data is often too large to be sent around between processes. A D2K-Package links to such a web API service provided by the developer of the D2K-Package.
[Pygeoapi](https://pygeoapi.io/) provides a guide to deploy an OGC API service. 

Every input parameter (i.e., input dataset and configuration) has to be defined in the json file including information on the title and description of the parameter, the data type (e.g, string, number) and whether it is optional or required. For a full list of metadata elements, check the [specification](https://ogcapi.ogc.org/processes/). Accordingly, the python file.

A key benefit of creating such a web API service is that every function is callable via a simple HTTP request. In addition a bunch of functions can together be integrated into the Galaxy platform. Moreover, the processes can be deployed where the process scripts are reducing data transfer. However, it is possible to create a complete D2K-Package without a web API service. Besides no accessibility via requests, the main implication would be that every function would need to be deployed on Galaxy separately. More on the integration into the Galaxy platform later.

#### Example OGC API Processes: 
https://aquainfra.ogc.igb-berlin.de/pygeoapi/processes?f=html 
Python and json files defining the processes.

#### Server requirements:
tbc

#### To-do:
Get a server that meets the server requirements mentioned above.
Install pygeoapi.
Create and upload the python and json files defining the process.





