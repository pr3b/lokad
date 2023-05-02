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

1. Data Collection: The first step is to collect data on inventory, such as quantity on hand, cost of goods, and number of times an item has been sold in a specific period. For this guide, we will use the packaged dataset available at https://docs.lokad.com/gallery/dataset-one-echelon-2017/.

2. Define ABC and XYZ Classification Rules: Before conducting an ABC XYZ analysis, define the ABC and XYZ classification rules.

   - ABC Classification: Classify products into three categories: A, B, and C, based on their revenue contribution:

     1. A products generate a cumulative revenue of less than 50%.
     2. B products generate cumulative revenue between 50-80%.
     3. C products generate more than 80% cumulative revenue.

   - XYZ Classification: Classify products into three categories: X, Y, and Z, based on their demand variability:
     1. X products have stable demand (<50%).
     2. Y products have moderate demand variability (50-100%).
     3. Z products have high demand variability (>100%).

**Upload the Lokad Dataset:** Load data from the Lokad dataset; this can be done in two ways:
