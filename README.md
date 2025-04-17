# User Guide:Creating a Data-to-Knowledge Package

## Note: This document is a draft. Please send your questions, comments, and suggestions to m.konkol[at]52north.org.
This user guide provides instructions on how to create a Data-to-Knowledge Package (D2K-Package) as described in this paper (<INSERT LINK>).
## Problem statement:
Publishing and reusing reproducible research is challenging. Users still need to make considerable efforts to understand the data and analysis code before they can reuse parts of the analysis in other contexts.
## Solution:
Based on a reproducible basis (code + data + computational environment), further applications including virtual labs, web API services, and workflows are built to provide an entry point to the analysis and encourage reuse.
Target audience:
Users who have an analysis pipeline and would like to create a D2K-Package out of it.





# User Guide: Creating a Data-to-Knowledge Package

**Note:** This document is a draft. Please send your questions, comments, and suggestions to m.konkol[at]52north.org.

### Problem statement

Publishing and reusing reproducible research is challenging. Users still need to make considerable efforts to understand the data and analysis code before they can reuse parts of the analysis in other contexts.

### Solution

Based on a reproducible basis (code + data + computational environment), further applications including virtual labs, web API services, and workflows are built to provide an entry point to the analysis and encourage reuse.

### Target audience

Users who have an analysis pipeline and would like to create a D2K-Package out of it.

### What is a Data-to-Knowledge Package?

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
- **GitHub** to host the toolbox  
  - *Note:* GitLab is possible, too, but at some point you will need to create a Pull Request to the Galaxy platform, which is only possible via GitHub.  
- **Docker** to containerize the source code  
- **MyBinder** to provide the virtual lab  
- **OGC API Processes** to provide OGC API Web Services based on **pygeoapi**  
- **OGCAPIProcess2Galaxy**   
- **Galaxy** to develop and run workflows 
- **RO-Crate** to create the D2K-Package  
- **Zenodo** to store the materials  

You will need access to a server to deploy the OGC API Process using, for instance, pygeoapi. If you are a member of the AquaINFRA project and would like to deploy your processes on the AquaINFRA server, please get in touch with us (<mail_address>).

---
### Step 1: Linking to the input data

- A D2K-Package links to **publicly accessible** data via **URL**.
- **Never** link to data stored **locally**.
- If sensitive, use **dummy data** (and clearly state that).
- Prefer **permanent identifiers** (e.g., DOI).
- Recommended data formats: **CSV, JSON, GeoPackage, Shapefile**.
- Must be under an **open license** (preferably CC0).

### To-dos

- **If data is yours:**
  - Use open formats
  - Publish in a DOI-enabled repository
  - Open license
- **If third-party data:**
  - Collect public links
  - Avoid Dropbox etc.

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
