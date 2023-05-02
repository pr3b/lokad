---
title: "How to Do ABC XYZ Analysis Using Envision"
date: 2023-05-02T15:10:59+07:00
draft: false
---

ABC-XYZ analysis is a categorization tool to identify the best-performing products in one’s catalog to determine appropriate service and safety stock levels. The goal is to prioritize actions and resources accordingly.

This guide will walk you through the steps to create an ABC XYZ analysis using the Envision programming language.

**Prerequisites**

- Prior understanding of the Envision programming language.
- Ensure that you have access to the Envision Playground and the Lokad dataset.

**Steps to Perform the ABC XYZ Analysis**

**1. Data Collection:** The first step is to collect data on inventory, such as quantity on hand, cost of goods, and number of times an item has been sold in a specific period. For this guide, we will use the packaged dataset available at https://docs.lokad.com/gallery/dataset-one-echelon-2017/.

**2. Define ABC and XYZ Classification Rules:** Before conducting an ABC XYZ analysis, define the ABC and XYZ classification rules.

- ABC Classification: Classify products into three categories: A, B, and C, based on their revenue contribution:

  1.  A products generate a cumulative revenue of less than 50%.
  2.  B products generate cumulative revenue between 50-80%.
  3.  C products generate more than 80% cumulative revenue.

- XYZ Classification: Classify products into three categories: X, Y, and Z, based on their demand variability:
  1.  X products have stable demand (<50%).
  2.  Y products have moderate demand variability (50-100%).
  3.  Z products have high demand variability (>100%).

**Upload the Lokad Dataset:** Load data from the Lokad dataset; this can be done in two ways:

**Envision Playground**

- Access https://try.lokad.com using a browser of your choice.
- Click on the **Files** tab.
- Click **Upload** and load the downloaded files of the [Lokad data set.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/)
  {{ $image := resources.Get "images/files.png" }}

**Use Your Lokad Account**

- Download the TSV files using the links present [here.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/)
- Then, go to the [Files tab](https://go.lokad.com/files/#/) of your Lokad account.
- Create a folder named **/sample** – the path **/sample** is the convention that we follow in the rest of the documentation.
- Re-upload these files into this newly created folder

Your Lokad account should look like this:
{{ $image := resources.Get "images/files.png" }}

For the purpose of this guide, we will be using the [Envision playground.](https://try.lokad.com/)

**4. Load the Lokad Dataset:** Load the **Items** and **Orders** tables from the Lokad dataset by using the following script:

```Python
/// Load Items dataset

read "/sample/Lokad_Items.tsv" as Items[Id] with
Id : text
Name : text
Category: text
SubCategory: text
Brand: text
ColorCode: text
Supplier: text
BuyPrice: number
SellPrice: number
SellCurrency: text
SupplierLeadTime: number
StockOnHand: number
StockOnOrder: number
```

```Python
/// Load Orders dataset

read "/sample/Lokad_Orders.tsv" as Orders expect [Id, Date] with
Id : text
Date : date
Quantity : number
NetAmount : number
Currency : text
```

This script joins the Orders table and the Items table on the Id column so you have the SKU's sell price and the quantity sold for each order.

**5. Calculate Total Revenue for Each Product:** Calculate the total annual revenue for each product by using this script:

```Python
   // Join Items and Orders

  show table "OrderedItems" with
    Items.Id
    Items.Name
    Items.SellPrice
    Orders.Date
    Orders.Quantity
    Orders.NetAmount
    Orders.Currency
```

This script multiplies the SKU's sell price by the quantity sold for each order to get the net revenue for that SKU and then adds the net revenue for each SKU to get the total revenue for that SKU.

**6. Calculate Cumulative Revenue:** Calculate the cumulative revenue percentages for each product by using the following script:

This script also sorts the items by revenue.

**7. Calculate ABC Classification:** Classify products into A, B, or C categories based on their revenue contribution using the following script:

**8. Calculate the Coefficient of Variation:** Calculate the Coefficient of Variation for each product by using the following script:

This script also sorts the items by demand variability

**9. Calculate XYZ Classification:** Classify products into X, Y, or Z categories based on their demand variability using the following script.

The final table displays the ABC XYZ analysis for each SKU, with the ABC classification determined by the product's revenue percentiles and the XYZ classification determined by the coefficient of variation of the product's demand.

image of the final abcxyz analysis table
