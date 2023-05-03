---
title: "Performing ABC-XYZ Analysis Using Envision"
date: 2023-05-02T15:10:59+07:00
draft: false
---

ABC-XYZ analysis is a powerful inventory management tool that classifies products based on their performance and demand variability.

This guide will walk you through the process of conducting an ABC-XYZ analysis using the Envision programming language.

## Prerequisites

- Basic understanding of the Envision programming language.
- Access to the Envision Playground and the Lokad dataset.

## Steps to Perform the ABC XYZ Analysis

## 1. Data Collection

Collect sales data, such as items, sell price, and the number of times an item has been sold within a specific period. For this guide, we will use the packaged dataset available [here.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/.)

## 2. Define ABC and XYZ Classification Rules

Establish rules for ABC and XYZ classifications

- **ABC Classification:** Categorize products into A, B, and C based on their revenue contribution:
  - **A:** <=50% cumulative revenue.
  - **B:** 50-80% cumulative revenue.
  - **C:** >80% cumulative revenue.
- **XYZ Classification:** Categorize products into X, Y, and Z based on their demand variability:
  - **X:** Stable demand (=<50%).
  - **Y:** Moderate demand variability (50-100%).
  - **Z:** High demand variability (>100%).

## 3. Upload the Dataset

### 3.1. Envision Playground

- Access the [Lokad Playground](https://try.lokad.com) using your browser.
- Click the **Files** tab.
- Click **Upload** and load the [dataset.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/)

![Lokad Dataset](/images/ezgif.com-video-to-gif.gif "Dataset")

### 3.2. Use Your Lokad Account

- Download the TSV files using the links [here.](https://docs.lokad.com/gallery/dataset-one-echelon-2017/)
- Go to the [Files tab](https://go.lokad.com/files/#/) tab of your Lokad account.
- Create a new folder.
  {{< hint info >}}
  **Note:** We are using `/sample` as the new folder name.
  {{< /hint >}}
- Upload the files into the newly created folder.

Your Lokad account should look like this:

![Lokad Dashboard](/images/dashboard.png "Dashboard")

{{< hint info >}}
**Note:** We will be using the method mentioned in [3.1. section](/posts/abcxyz/#31-envision-playground) for the following steps.
{{< /hint >}}

## 4. Load the Dataset

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

This script joins the **Orders** and the **Items** tables on the Id column to obtain the product's sell price and quantity sold for each order.

![Joined Table](/images/joined-table.png "Joined")

## 5. Calculate Total Product Sales per Month

Calculate the total sales of each product per month using this script:

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

We filtered orders from 2017 only. You can change this according to the year of analysis you want to run.

![Items Sold per Month](/images/soldpermonth.png "MonthlySales")

## 6. Calculate Annual Revenue for Each Product

Calculate the total annual revenue for each product using this script:

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

This script adds all the total monthly sales of each product into the annual revenue.

![Items Sold per Year](/images/soldperyear.png "YearlySales")

## 7. Calculate Cumulative Revenue

Calculate the cumulative revenue percentages for each product using this script:

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

## 8. Calculate Coefficient of Variation

Calculate the Coefficient of Variation for each product using this script:

```js
  // Standard Deviation
  // Calculate the standard deviation of the demand per item (XYZ classification)
  Items.STDVariance = stdev(Orders.Quantity)

  show table "Products sold in 2017" with
  Items.STDVariance
```

![Standard Deviation](/images/stdev.png "Stdev")

## 9. Calculate ABC Classification

Classify products into A, B, or C categories based on their revenue contribution using this script:

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

## 10. Calculate XYZ Classification

Classify products into X, Y, or Z categories based on their demand variability using this script:

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

The final table displays the ABC-XYZ analysis for each product, with the ABC classification determined by the product’s revenue percentiles and the XYZ classification determined by the coefficient of variation of the product’s demand.

![Table Result](/images/abcxyz-analysis-table.png "Result")
![Table Result 2](/images/abcxyz-analysis-table-2.png "Result-2")
