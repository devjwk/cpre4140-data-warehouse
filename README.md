<div align="center">

<img src="assets/banner.svg" alt="DATA WAREHOUSE DESIGN — From nine requirements to a cube you can query" width="100%">

![Database](https://img.shields.io/badge/Database-SQL%20Server-047857?style=flat-square&labelColor=022C22)
![Cube](https://img.shields.io/badge/Cube-SSAS%20multidimensional-064E3B?style=flat-square&labelColor=022C22)
![Queries](https://img.shields.io/badge/Queries-MDX-0F766E?style=flat-square&labelColor=022C22)
![Stage](https://img.shields.io/badge/Stage-In%20progress-B45309?style=flat-square&labelColor=022C22)

Iowa State University · CprE/SE 4140 · Fall 2026

[Overview](#overview) · [Cube structure](#cube-structure) · [My role](#my-role) · [Results](#results) · [Limitations](#limitations-and-next-steps)

</div>

---

> **Where it stands — Project 1 and Lab 3 complete**  
> A multidimensional model of AdventureWorksDW2014, implemented as an SSAS cube and queried with MDX.  
> The course is ongoing; later labs will be added.

| Measure groups | Dimensions | Requirements covered | Date roles |
| :---: | :---: | :---: | :---: |
| **2** | **8** | **9 / 9** | **3** |

| | |
|---|---|
| Period | September – October 2026 (Fall 2026, in progress) |
| Team | Individual |
| Stack | SQL Server, SSAS (multidimensional), MDX, MultiDim notation, draw.io |

## Overview

- **Problem:** AdventureWorksDW2014 holds sales data across many tables. Business questions such as "sales by product category, by year, for customers in one country" need a model that makes those slices direct.
- **Approach:** a combined analysis-driven and source-driven design. I mapped each requirement in the project handout to facts, measures, dimensions and hierarchies, then matched each one to the tables available in the source.

| Folder | Contents |
|---|---|
| `dataproj1/Project_1/` | Project 1: MultiDim conceptual schema (PDF and editable draw.io), exported SSAS project, design notes |
| `lab3/` | Lab 3: MDX queries against the cube, with a result screenshot |

## Cube structure

```mermaid
%%{init: {"theme": "base", "themeVariables": {"primaryColor": "#047857", "primaryTextColor": "#ffffff", "primaryBorderColor": "#022C22", "lineColor": "#94A3B8", "secondaryColor": "#047857", "tertiaryColor": "#022C22", "clusterBkg": "#F8FAFC", "clusterBorder": "#94A3B8", "edgeLabelBackground": "#F1F5F9", "fontFamily": "ui-sans-serif, system-ui, sans-serif"}}}%%
flowchart LR
    IS(["Internet Sales"]):::fact
    SQ(["Sales Quota"]):::fact
    IS --- D1["Date · order, due, ship"]
    IS --- PR["Product"]
    IS --- CU["Customer"]
    IS --- ST["Sales Territory"]
    IS --- CY["Currency"]
    SQ --- EM["Employee"]
    SQ --- D1
    RE["Reseller"]
    ORG["Organization"]
    classDef fact fill:#6EE7B7,color:#0B1220,stroke:#022C22
```

Reseller and Organization are defined but not yet linked to a fact.

## My role

Individual work: requirements analysis, the conceptual schema, the cube, the MDX queries, and the write-up.

## What I learned

- Conceptual modeling with MultiDim: facts, levels, and hierarchies.
- Role-playing dimensions. One Date dimension serves as order date, due date and ship date.
- Parent-child hierarchies for self-referencing tables (employee supervision, organization structure).
- When not to link a dimension to a fact. SSAS related Organization to Internet Sales through the currency key automatically; I removed that link because it did not mean what it implied.
- MDX: members, tuples, `CHILDREN`, `ASCENDANTS`, `ANCESTOR`, and slicing with `WHERE`.

## Resources used

- Vaisman and Zimányi, *Data Warehouse Systems: Design and Implementation* (MultiDim notation)
- Microsoft SSAS and MDX documentation
- Course labs 1–3 and the Project 1 handout

## Results

- A cube with 2 measure groups (Internet Sales, Sales Quota) and 8 dimensions (Date, Product, Customer, Sales Territory, Employee, Reseller, Currency, Organization).
- All 9 requirements in the handout are covered; the mapping is in [`dataproj1/Project_1/README.txt`](dataproj1/Project_1/README.txt).
- Schema: [`Project1_MultiDim.pdf`](dataproj1/Project_1/Project1_MultiDim.pdf)

![Top 5 products in France (MDX result)](lab3/screenshots/lab3_top5_products_france.png)

## Limitations and next steps

- Hierarchies were built without defining attribute relationships between every level. SSAS raises a best-practice warning; results are unaffected, but query performance could improve.
- Sales quota is recorded per employee per quarter, so analysis below the quarter level is not meaningful.
- The deployment wizard was unavailable on the lab VM, so the cube is exported as a full SSAS project instead of an XMLA script.
- Reseller and Organization are not linked to a fact yet. They are ready to be related once a reseller sales or finance fact is added.
