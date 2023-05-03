---
title: "How to Do ABC XYZ Analysis Using Envision"
date: 2023-05-02T15:10:59+07:00
draft: false
---

ABC-XYZ analysis is a categorization tool used to identify the best-performing products in a catalog and to determine appropriate service and safety stock levels. The goal is to prioritize actions and allocate resources accordingly.

This guide will walk you through the steps to create an ABC-XYZ analysis using the Envision programming language.

## Prerequisites

- Prior understanding of the Envision programming language.
- Access to the Envision Playground and the Lokad dataset.

## Steps to Perform the ABC XYZ Analysis

## 1. Data Collection

The first step is to collect data on sales, such as items, sell price, and number of times an item has been sold in a specific period. For this guide, we will use the packaged dataset available at [here.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/.)

## 2. Define ABC and XYZ Classification Rules

Before conducting an ABC-XYZ analysis, define the ABC and XYZ classification rules.

- **ABC Classification:** Classify products into three categories: A, B, and C, based on their revenue contribution:
  - **A** products generate a cumulative revenue of less than or equal to 50%.
  - **B** products generate cumulative revenue between 50-80%.
  - **C** products generate more than 80% cumulative revenue.
- **XYZ Classification:** Classify products into three categories: X, Y, and Z, based on their demand variability:
  - **X** products have stable demand (=<50%).
  - **Y** products have moderate demand variability (50-100%).
  - **Z** products have high demand variability (>100%).

## 3. Upload the Lokad Dataset

Load data from the Lokad dataset; this can be done in two ways:

**3. a) Envision Playground**

- Access [https://try.lokad.com](https://try.lokad.com) using a browser of your choice.
- Click on the **Files** tab.
- Click **Upload** and load the downloaded files of the [Lokad data set.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/)
  ![Lokad Datasets](/images/dataset.png "Datasets")

**3. b) Use Your Lokad Account**

- Download the TSV files using the links present [here.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/)
- Then, go to the [Files tab](https://go.lokad.com/files/#/) of your Lokad account.
- Create a folder named `/sample` – the path `/sample` is the convention that we follow in the rest of the documentation.
- Re-upload these files into this newly created folder

Your Lokad account should look like this:

![Lokad Dashboard](/images/dashboard.png "Dashboard")

> For the purpose of this guide, we will be using the [Envision playground.](https://try.lokad.com/)

## 4. Load the Lokad dataset

Load the **Items** and **Orders** tables from the Lokad dataset by using the following script:

```js
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

```js
/// Load Orders dataset

read "/sample/Lokad_Orders.tsv" as Orders expect [Id, Date] with
Id : text
Date : date
Quantity : number
NetAmount : number
Currency : text
```

```js
  // Join table Orders and Items

  show table "OrderedItems" with
  Items.Id
  Items.Name
  Items.SellPrice
  Orders.Date
  Orders.Quantity
  Orders.NetAmount
  Orders.Currency
```

![Joined Table](/images/joined-table.png "Joined")

This script joins the **Orders** table and the **Items** table on the Id column so you have the product's sell price and the quantity sold for each order. We also filtered orders from 2017 only - You can change it according to the year of analysis you want to run.

## 5. Calculate the total product sold per Month

Calculate the total sales of each product per month by using this script:

```js
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

![Items Sold per Month](/images/soldpermonth.png "MonthlySales")

## 6. Calculate the annual revenue for each product

Calculate the total annual revenue for each product by using this script:

```js

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

  //Sum of Items Sold Price per Month
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

  // Calculate the total sales value per item
  Items.SoldTotalPrice = sum(Orders.Quantity * Items.SellPrice)

  // calculate total sales per item per month
  Items.SoldItemsPerMonth = sum(Orders.Quantity) / 12

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
```

![Items Sold per Year](/images/soldperyear.png "YearlySales")

This script will add all the total monthly sales of each product into the annual revenue of that product.

## 7. Calculate cumulative revenue

Calculate the cumulative revenue percentages for each product by using the following script:

```js
  // Cummulative Sold Total Percentage
  MaxTotalSoldCummulative = max(cumsum(Items.AnnualSoldTotalPrice) scan Items.Id)
  Items.CumulativeSold = cumsum(Items.AnnualSoldTotalPrice) scan Items.Id
  Items.CumulativeSoldTotalPricePercentage = (Items.CumulativeSold/ MaxTotalSoldCummulative) * 100

  show table "Products sold in 2017" with
  Items.CumulativeSold
  Items.CumulativeSoldTotalPricePercentage
```

![Cumulative Revenue](/images/cumulative.png "Cumulative")

## 8. Calculate the coefficient of variation

Calculate the Coefficient of Variation for each product by using the following script:

```js
  // Standard Deviation
  // Calculate the standard deviation of the demand per item (XYZ classification)
  Items.DemandStandardDeviation = stdev(Orders.Quantity);

  // Calculate the coefficient of variation (CV) per item
  Items.CoefficientOfVariation =
  Items.DemandStandardDeviation / avg(Orders.Quantity);

  show table "Products sold in 2017" with
  Items.DemandStandardDeviation
  Items.CoefficientOfVariation
```

![Standard Deviation](/images/stdev.png "Stdev")

## 9. Calculate ABC Classification

Classify products into A, B, or C categories based on their revenue contribution using the following script:

```js
  // ABC Classification
  // calculate the total sales per item as a percentage of total sales
  Items.SoldTotalPricePercentage = (Items.YearlySold/ Items.SoldTotalPrice) * 100

  // Calculate the ABC classification based on the cumulative sales percentage
  Items.ABCClassification = if Items.SoldTotalPricePercentage <= 50 then
        "A"
      else if Items.SoldTotalPricePercentage <= 80 then
        "B"
      else
        "C"

  show table "Products sold in 2017" with
  Items.SoldTotalPricePercentage
  Items.ABCClassification
```

![ABC Classification](/images/abc-class.png "ABC-Class")

This script also sorts the items by demand variability

## 10. Calculate XYZ Classification

Using the following script, classify products into X, Y, or Z categories based on their demand variability.

```js
  /**
  XYZ Classification
  */
  // Define the thresholds for classifying items as X, Y, or Z
  VarianceThresholdX = 50
  VarianceThresholdY = 100

  Items.STDVariance = stdev(Orders.Quantity)

  Items.XYZClassification = if Items.STDVariance <= VarianceThresholdX then
        "X"
      else if Items.STDVariance <= VarianceThresholdY then
        "Y"
      else
        "Z"

  show table "Products sold in 2017" with
  Items.XYZClassification
```

![XYZ Classification](/images/xyz-class.png "XYZ-Class")

The final table displays the ABC XYZ analysis for each SKU, with the ABC classification determined by the product's revenue percentiles and the XYZ classification determined by the coefficient of variation of the product's demand.

![Table Result](/images/abcxyz-analysis-table.png "Result")
![Table Result 2](/images/abcxyz-analysis-table-2.png "Result-2")
