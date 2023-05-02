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
  ![Lokad Datasets](/images/dataset.png "Datasets")

**Use Your Lokad Account**

- Download the TSV files using the links present [here.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/)
- Then, go to the [Files tab](https://go.lokad.com/files/#/) of your Lokad account.
- Create a folder named **/sample** – the path **/sample** is the convention that we follow in the rest of the documentation.
- Re-upload these files into this newly created folder

Your Lokad account should look like this:
![Lokad Dashboard](/images/dashboard.png "Dashboard")

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

```Python
  // Items sold per month

  Items.Sold = sum(Orders.Quantity)
    when (Orders.Date <= date(2017, 8, 31))
    default Items.SellPrice

  Items.MonthlySold = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date <= date(2017, 8, 31))
    default Items.SellPrice

  show table "Products" with
    Items.Id
    Items.Name
    Items.Sold
    Items.MonthlySold
```

**5. Calculate Total Revenue for Each Product:** Calculate the total annual revenue for each product by using this script:

```Python

  // Items sold in 2017

  // Items sold per month
  Items.SoldJanuary = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 1, 1) and Orders.Date <= date(2017, 1, 31))

  Items.SoldFebruary = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 2, 1) and Orders.Date <= date(2017, 2, 28))

  Items.SoldMarch = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 3, 1) and Orders.Date <= date(2017, 3, 31))

  Items.SoldApril = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 4, 1) and Orders.Date <= date(2017, 4, 30))

  Items.SoldMay = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 5, 1) and Orders.Date <= date(2017, 5, 31))

  Items.SoldJune = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 6, 1) and Orders.Date <= date(2017, 6, 30))

  Items.SoldJuly = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 7, 1) and Orders.Date <= date(2017, 7, 31))

  Items.SoldAugust = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 8, 1) and Orders.Date <= date(2017, 8, 31))

  Items.SoldSeptember = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 9, 1) and Orders.Date <= date(2017, 9, 30))

  Items.SoldOctober = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 10, 1) and Orders.Date <= date(2017, 10, 31))

  Items.SoldNovember = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 11, 1) and Orders.Date <= date(2017, 11, 30))

  Items.SoldDecember = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 12, 1) and Orders.Date <= date(2017, 12, 31))

  // Default Value Each month based on Sold Per month quantity
  Items.defaultValueJan = if Items.SoldJanuary == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueFeb = if Items.SoldFebruary == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueMarch = if Items.SoldMarch == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueApril = if Items.SoldApril == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueMay = if Items.SoldMay == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueJune = if Items.SoldJune == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueJuly = if Items.SoldJuly == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueAugust = if Items.SoldAugust == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueSeptember = if Items.SoldSeptember == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueOctober = if Items.SoldOctober == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueNovember = if Items.SoldNovember == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  Items.defaultValueDecember = if Items.SoldDecember == 0 then
        Items.SellPrice * 0
      else
        Items.SellPrice

  // Sum of Items Sold Price per Month
  Items.JanuarySoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 1, 1) and Orders.Date <= date(2017, 1, 31))
    default Items.defaultValueJan

  Items.FebruarySoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 2, 1) and Orders.Date <= date(2017, 2, 28))
    default Items.defaultValueFeb

  Items.MarchSoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 3, 1) and Orders.Date <= date(2017, 3, 31))
    default Items.defaultValueMarch

  Items.AprilSoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 4, 1) and Orders.Date <= date(2017, 4, 30))
    default Items.defaultValueApril

  Items.MaySoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 5, 1) and Orders.Date <= date(2017, 5, 31))
    default Items.defaultValueMay

  Items.JuneSoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 6, 1) and Orders.Date <= date(2017, 6, 30))
    default Items.defaultValueJune

  Items.JulySoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 7, 1) and Orders.Date <= date(2017, 7, 31))
    default Items.defaultValueJuly

  Items.AugustSoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 8, 1) and Orders.Date <= date(2017, 8, 31))
    default Items.defaultValueAugust

  Items.SeptemberSoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 9, 1) and Orders.Date <= date(2017, 9, 30))
    default Items.defaultValueSeptember

  Items.OctoberSoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 10, 1) and Orders.Date <= date(2017, 10, 31))
    default Items.defaultValueOctober

  Items.NovemberSoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 11, 1) and Orders.Date <= date(2017, 11, 30))
    default Items.defaultValueNovember

  Items.DecemberSoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)
    when (Orders.Date >= date(2017, 12, 1) and Orders.Date <= date(2017, 12, 31))
    default Items.defaultValueDecember

  Items.YearlySold = Items.JanuarySoldTotalPrice + Items.FebruarySoldTotalPrice + Items.MarchSoldTotalPrice + Items.AprilSoldTotalPrice + Items.MaySoldTotalPrice + Items.JuneSoldTotalPrice + Items.JulySoldTotalPrice + Items.AugustSoldTotalPrice + Items.SeptemberSoldTotalPrice + Items.OctoberSoldTotalPrice  + Items.NovemberSoldTotalPrice + Items.DecemberSoldTotalPrice

  // Calculate total sales per item
  Items.SoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)

  // Calculate total sales per item per month
  Items.SoldItemsPerMonth = sum(Orders.Quantity)
    when (Orders.Date >= date(2017, 11, 1) and Orders.Date <= date(2017, 11, 30))

  // Calculate the total sales per item as a percentage of total sales
  Items.SoldTotalPricePercentage = Items.SoldTotalPrice / Items.YearlySold * 100

  show table "Products sold in 2017" with
  Items.Id
  Items.Name
  Items.SellPrice
  Items.SoldJanuary
  Items.JanuarySoldTotalPrice
  Items.SoldFebruary
  Items.FebruarySoldTotalPrice
  Items.SoldMarch
  Items.MarchSoldTotalPrice
  Items.SoldApril
  Items.AprilSoldTotalPrice
  Items.SoldMay
  Items.MaySoldTotalPrice
  Items.SoldJune
  Items.JuneSoldTotalPrice
  Items.SoldJuly
  Items.JulySoldTotalPrice
  Items.SoldAugust
  Items.AugustSoldTotalPrice
  Items.SoldSeptember
  Items.SeptemberSoldTotalPrice
  Items.SoldOctober
  Items.OctoberSoldTotalPrice
  Items.SoldNovember
  Items.NovemberSoldTotalPrice
  Items.SoldDecember
  Items.DecemberSoldTotalPrice
  Items.SoldTotalPrice
  Items.SoldItemsPerMonth
  Items.YearlySold
  Items.CumulativeSoldTotalPricePercentage
  Items.SoldTotalPricePercentage
  Items.ABCClassification
  Items.XYZClassificationSwitch

```

This script multiplies the SKU's sell price by the quantity sold for each order to get the net revenue for that SKU and then adds the net revenue for each SKU to get the total revenue for that SKU.

**6. Calculate Cumulative Revenue:** Calculate the cumulative revenue percentages for each product by using the following script:

```Python
  // Calculate the cumulative percentage of sales
  Items.CumulativeSoldTotalPricePercentage = cumsum(Items.SoldTotalPricePercentage) scan Items.Id
```

This script also sorts the items by revenue.

**7. Calculate ABC Classification:** Classify products into A, B, or C categories based on their revenue contribution using the following script:

```Python
  // Calculate ABC Classification
  Items.ABCClassification = if Items.SoldTotalPricePercentage <= 50 then
        "A"
      else if Items.SoldTotalPricePercentage <= 80 then
        "B"
      else
        "C"
```

**8. Calculate the Coefficient of Variation:** Calculate the Coefficient of Variation for each product by using the following script:

This script also sorts the items by demand variability

**9. Calculate XYZ Classification:** Classify products into X, Y, or Z categories based on their demand variability using the following script.

```Python
  // Calculate XYZ Classification
  Items.XYZClassificationSwitch = if Items.SoldItemsPerMonth / Items.StockOnHand > 1 then
      "X"
    else if Items.SoldItemsPerMonth / Items.StockOnHand > 0.5 then
      "Y"
    else
      "Z"
```

The final table displays the ABC XYZ analysis for each SKU, with the ABC classification determined by the product's revenue percentiles and the XYZ classification determined by the coefficient of variation of the product's demand.

![Table Result](/images/abcxyz-analysis-table.png "Result")
![Table Result 2](/images/abcxyz-analysis-table-2.png "Result-2")
