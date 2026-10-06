CprE/SE 4140 Project 1
Data Warehouse Design on AdventureWorksDW2014

Team Members
Jongwoo Kim (jwk0425)

Overview
This project designs and implements a data warehouse cube for AdventureWorksDW2014. The design follows a combined analysis-driven / source-driven approach: the user requirements in the project handout were mapped to facts, measures, dimensions, and hierarchies, and each element was then matched to the tables available in AdventureWorksDW2014.

The conceptual schema is modeled in MultiDim, and the cube is implemented as an SQL Server Analysis Services (multidimensional) project named Project1, with one cube called AW Project1.

Submission Contents
Project1_MultiDim.pdf: MultiDim conceptual schema
Project1_MultiDim.drawio: editable source of the MultiDim diagram (draw.io)
Project1_cube_SSAS.zip: exported cube (complete SSAS project, including bin/Project1.asdatabase)
README.txt: this file

Cube Summary

Facts and Measures
Internet Sales (FactInternetSales): Order Quantity, Product Standard Cost, Freight, Sales Amount, Fact Internet Sales Count
Sales Quota (FactSalesQuota): Sales Amount Quota

Dimensions
Date: Calendar hierarchy (Year -> Quarter -> Month -> Date) and Fiscal hierarchy (Fiscal Year -> Fiscal Quarter -> Month -> Date)
Product: Product Categories hierarchy (Category -> Subcategory -> Product), with Weight
Customer: Customer Geography hierarchy (Country -> State-Province -> City -> Postal Code -> Customer), with Birth Date and Date First Purchase
Sales Territory: Territory hierarchy (Group -> Country -> Region)
Employee: Employee Territory hierarchy (Group -> Country -> Region -> Employee) and a parent-child supervision hierarchy
Reseller: Reseller Geography hierarchy (Country -> State-Province -> City -> Postal Code -> Reseller)
Currency: Currency
Organization: Organization Currency hierarchy (Currency -> Organization) and a parent-child organization hierarchy

Requirements Coverage

Requirement 1 — Calendar and Fiscal Time
Date is the lowest temporal level. Two hierarchies share the Month level: Calendar (month, quarter, year) and Fiscal (month, fiscal quarter, fiscal year).

Requirement 2 — Sales Quota Fact
FactSalesQuota is a second measure group, related to Employee and Date.

Requirement 3 — Product Categories and Weight
Product rolls up to Subcategory and Category, and Weight is a descriptive attribute of Product.

Requirement 4 — Customer Spatial and Temporal Properties
Spatial: the full geography of the customer, from Postal Code up to Country. Temporal: Birth Date and Date First Purchase are kept as customer attributes.

Requirement 5 — Organization and Currency
The Organization dimension includes the currency each organization uses (Organization Currency hierarchy) and the organization's own parent-child structure. Currency is also a separate dimension of Internet Sales.

Requirement 6 — English and Spanish Names
Wherever the source provides both, both versions are available: month and day names (Date), product, subcategory and category names (Product), country, education and occupation (Customer), and country (Reseller). Sales Territory, Employee, Currency, and Organization have no Spanish columns in the source, so only English is available for them.

Requirement 7 — Sales Territory for Employees and Sales
Internet Sales is related to Sales Territory directly. Employees are related to their territory through the Employee Territory hierarchy, so sales quota can be analyzed by territory as well.

Requirement 8 — Reseller Geo-Analytics
Reseller has its own geography hierarchy. In the MultiDim schema, Customer and Reseller share the same geography levels (Postal Code -> City -> State-Province -> Country), since both come from DimGeography.

Requirement 9 — Measures
Internet Sales: order quantity, product standard cost, freight, sales count, and sales amount. Sales Quota: only the quota amount. All other numeric columns of the fact tables were left out.

Design Decisions

Role-Playing Date Dimension
One Date dimension is used in several roles. Internet Sales uses it three times (Order Date, Due Date, Ship Date), and Sales Quota uses it once (Date).

Parent-Child Hierarchies
Employee (ParentEmployeeKey) and Organization (ParentOrganizationKey) reference themselves, so both are modeled as recursive (parent-child) hierarchies: Supervision (subordinate -> supervisor) and Organization Structure (child -> parent organization).

Reseller and Organization Not Linked to a Fact
The handout does not request any reseller or organization measures, so these two dimensions are not related to either measure group. They are available for analysis of their own attributes and hierarchies, and are ready to be related once a reseller sales or finance fact is added. SSAS automatically related Organization to Internet Sales through Currency Key; this link was removed on purpose, because Internet Sales is not recorded per organization, and the link would repeat the same sales value for every organization that uses a given currency.

Measure Additivity
All measures are additive (summed). Fact Internet Sales Count is a count of the Internet Sales rows.

MultiDim Notation
Lines between a fact and a level are one-to-many. On hierarchy lines, the crow's foot marks the child (many) side and the bar marks the parent (one) side.

Known Limitations
The hierarchies were built in the same way as in the labs (by dragging attributes), without defining attribute relationships between every level. SSAS shows a best-practice warning for this, but it does not affect the results.

FactSalesQuota records one quota per employee per quarter, so analysis of the quota below the quarter level is not meaningful.

The Analysis Services Deployment Wizard and the Analysis Services connection in SSMS were not available on the lab VM, so the cube could not be scripted to XMLA. The cube is exported instead as the complete SSAS project, which contains every object definition and the compiled database file bin/Project1.asdatabase.

How to Deploy the Cube

To open the project:
Unzip Project1_cube_SSAS.zip and open Project1.slnx in Visual Studio with the Analysis Services extension.
To point it at a server:
Project Properties -> Deployment -> Server = your SQL Server Analysis Services instance
To build the cube:
Deploy, then Process the AW Project1 cube.
