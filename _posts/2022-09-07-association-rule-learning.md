---
layout: post
title: Understanding Grocery Products Relationships Using Association Rule Learning
image: "/posts/association-rules-title-img.jpg"
tags: [Association Rule Learning, Python]
---

In this project we use Association Rule Learning to analyse the transactional relationships & dependencies between products in a grocery store.

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
    - [Results](#overview-results)
    - [Growth/Next Steps](#overview-growth)
- [01. Data Overview](#data-overview)
- [02. Apriori Overview](#apriori-overview)
- [03. Data Preparation](#apriori-data-prep)
- [04. Applying The Apriori Algorithm](#apriori-fit)
- [05. Interpreting The Results](#apriori-results)
- [06. Growth & Next Steps](#growth-next-steps)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

Our client is looking to re-jig their store.  Customers are often complaining that they can't find the products they want, and are also wanting recommendations about which other products to try.  On top of this, their marketing team would like to start running "bundled" promotions as this has worked well in other areas of the store - but need guidance with selecting which products to put together.

They have provided us a sample of 9,800 transactions - our task is fairly open - to see if we can find solutions or insights that might help the business address the aforementioned problems!

<br>
<br>
### Actions <a name="overview-actions"></a>

Based upon the tasks at hand - we apply Association Rule Learning, specifically *Apriori* to examine & analyse the strength of relationship between different products within the transactional data.

We firstly installed the apyori package, which contains all of the required functionality for this task.

We then needed to bring in the sample data, and get it into the right format for the Apriori algorithm to deal with.

From there we apply the Apriori algorithm to provide us with several different relationship metrics, namely:

* Support
* Confidence
* Expected Confidence
* Lift

These metrics examine product relationships in different ways, so we utilise each to put forward ideas that address each of the tasks at hand.  You can read more about these metrics, and the Apriori algorithm in the relevant section below.

<br>
<br>

### Results <a name="overview-results"></a>

Interestingly, the strongest relationship existed between two easy to cook food products - this is useful information for the category managers as they may want to ensure that these products are available in one section of the store, rather than existing in their respective product types.

We also saw some strong relationships between baking products - which again is extremely useful for category managers who are thinking about the best way to lay out the products - having sections by product category rather than necessarily by type might make it easier for customers to find what they are after.

We propose to also build a "search engine" for category managers where they can look-up products by keyword in the product association table.

As an example - we search for any products that associate strongly with "juice" products.

<br>
<br>
### Growth/Next Steps <a name="overview-growth"></a>

As this is first & foremost an exploratory project, we will take back the results to the client Category Managers & discuss the results, our views on how these insights can be actioned best, and any considerations that need to be taken into account when interpreting.

From there we will recommend applying this same logic to all other categories, as well as potentially across the full-product range.

We will also propose the build of the "Keyword Search Engine" which will help Category Managers extract and utilise the insights held within the data.

<br>
<br>

___

# Data Overview  <a name="data-overview"></a>

Our initial dataset contains 9,800 transactions, each of which shows the grocery products that were present in that transaction.  

In the code below, we import Pandas, as well as the apriori algorithm from the apyori library, and we bring the raw data into Python.
<br>
```python

# import required Python packages
import pandas as pd
from apyori import apriori

# import the sample data
grocery_transactions = pd.read_csv("data/groceries - groceries.csv")

```
<br>

A sample of this data (the first 10 transactions) can be seen below:
<br>
<br>

| **transaction_id** | **Item 1** | **Item 2** | **Item 3** | **Item 4** | **Item 5** | **…** |
|---|---|---|---|---|---|---|
| 1 | citrus fruit | semi-finished bread| margarine | ready soups | … | ... |
| 2 | tropical fruit | yogurt | coffee | … | ... |
| 3 | whole milk | … | ... |
| 4 | pip fruit | yogurt | cream cheese | meat spreads | … | ... |
| 5 | other vegetables | whole milk | condensed milk | long life bakery product | … | ... |
| 6 | whole milk | butter | yogurt | rice | abrasive cleaner | ... |
| 7 | rolls/buns | … | ... |
| 8 | other vegetables | UHT-milk | rolls/buns | tomato juice| white vinegar | ... |
| 9 | potted plants | …  | ... |
| 10 | whole milk | cereals | … | ... |
| … | … | … | … | … | … | … |

<br>
To explain this data, *Transaction 1* (the first row) contained four products, citrus fruit, semi-finished bread, margarine and ready soups.  As there were only four products in this transaction, the remaining columns are blank.

Transaction 2 (the second row) contained three products. The first three columns for this row are therefore populated, followed by blank values.

For our sample data, the maximum number of unique items was 32, meaning the table of data had a total of 33 columns (32 for items + transaction_id).

The *apyori* library that we are using does not want the data in this format, it instead wants it passed in as a *list of lists* so we will need to modify it.  The code and logic for this can be found in the Data Preparation section below.

___
<br>
# Apriori Overview  <a name="apriori-overview"></a>

Association Rule Learning is an approach that discovers the strength of relationships between different data-points.  It is commonly utilised to understand which products are frequently (or infrequently) purchased together.

In a business sense this can provide some really interesting, and useful information that can help optimise:

* Product Arrangement/Placement (making the customer journey more efficient)
* Product Recommendations (customers who purchased product A also purchased product B)
* Bundled Discounts (which products should/should not be put together)

One powerful, intuitive, and commonly used algorithm for Association Rule Learning is **Apriori**.

In Apriori there are four key metrics, namely:

* Support
* Confidence
* Expected Confidence
* Lift

Each of these metrics help us understand items, and their relationship with other items in their own way.

<br>
##### Support

Support is extremely intuitive, it simply tells us the percentage of all transactions that contain *both* Item A and Item B.  To calculate this we’d just count up the transactions that include both items, and divide this by the total number of transactions.

You can think of Support as a baseline metric that helps us understand how common or popular this particular *pair* of items is.

<br>
##### Confidence

Confidence takes us a little bit further than Support, and looks more explcitly at the *relationship* between the two items.

It asks "of all transactions that *included item A*, what proportion also included item B?"  

In other words, here we are counting up the number of transactions that contained *both items A and B* and then rather than dividing by *all transactions* like we did for Support, we instead divide this by the *total number of transactions that contained item A*.

A high score for Confidence can mean a strong product relationship - but not always!  When one of the items is very popular we can get an inflated score.  To help us regulate this, we can look at two further metrics, Expected Confidence and Lift!

<br>
##### Expected Confidence

Expected Confidence is quite simple, it is the percentage of *all transactions* that *contained item B*.

This is important as it provides indication of what the Confidence *would be* if there were no relationship between the items.  We can use Expected Confidence, along with Confidence to calculate our final (and most powerful) metric - Lift!

<br>
##### Lift

Lift is the factor by which the Confidence, exceeds the Expected Confidence.  In other words, Lift tells us how likely item B is purchased *when item A is purchased*, while *controlling* for how popular item B is.

We calculate Lift by dividing Confidence by Expected Confidence.

A Lift score *greater than 1* indicates that items A & B appear together *more often* than expected, and conversely a Lift score *less then 1* indicates that items A & B appear together *less often* than expected.

<br>
##### In Practice

While above we're just discussing two products (Item A & Item B) - in reality this score would be calculated between *all* pairs of products, and we could then sort these by Lift score (for example) and see exactly what the strongest or weakest relationships were - and this information would guide our decisions regarding product layout, recommendations for customers, or promotions.

<br>
##### An Important Consideration

Something to consider when assessing the results of Apriori is that, Item/Product relationships that have a *high Lift score* but also have a *low Support score* should be interpreted with caution!

In other words, if we sorted all Item relationships by descending Lift score, the one that comes out on top might initially seem very impressive and it may appear that there is a very strong relationship between the two items.  Always take into account the Support metric - it could be that this relationship is only taking place by chance due to the rarity of the item set.

___
<br>
# Data Preparation  <a name="apriori-data-prep"></a>

As mentioned in the Data Overview section above, the *apyori* library that we are using does not want the data in table format, it instead wants it passed in as a *list of lists* so we will need to modify it here.  

In the code below, we:

* Remove the ID column as it is not required
* Iterate over the DataFrame, appending each transaction to a list, and appending those to a master list
* Print out the first 10 lists from the master list

<br>
```python

# drop ID column
grocery_transactions.drop("transaction_id", axis = 1, inplace = True)

# modify data for apriori algorithm
transactions_list = []
for index, row in grocery_transactions.iterrows():
    transaction = list(row.dropna())
    transactions_list.append(transaction)
    
# print out first 10 lists from master list
print(transactions_list[:10])

[['citrus fruit', 'semi-finished bread', 'margarine', 'ready soups'], ['tropical fruit', 'yogurt', 'coffee'], ['whole milk'], ['pip fruit', 'yogurt', 'cream cheese', 'meat spreads'], ['other vegetables', 'whole milk', 'condensed milk', 'long life bakery product'], ['whole milk', 'butter', 'yogurt', 'rice', 'abrasive cleaner'], ['rolls/buns'], ['other vegetables', 'UHT-milk', 'rolls/buns', 'tomato juice', 'white vinegar'], ['potted plants'], ['whole milk', 'cereals']]

```
<br>

As you can see from the print statement, each transaction (row) from the initial DataFrame is now contained within a list, all making up the master list.

___
<br>
# Applying The Apriori Algorithm <a name="apriori-fit"></a>

In the code below we apply the apriori algorithm from the apyori library.

This algorithm allows us to specify the association rules that we want.  We set:

* A minimum *Support* of 0.003 to eliminate very rare product sets
* A minimum *Confidence* of 0.2
* A minimum *Lift* of 3 to ensure we're only focusing on product sets with strong relationships
* A minimum & maximum length of 2 meaning we're only focusing on product *pairs* rather than larger sets

```python

# apply the apriori algorthm and specify required parameters
apriori_rules = apriori(transactions_list,
                        min_support = 0.003,
                        min_confidence = 0.2,
                        min_lift = 3,
                        min_length = 2,
                        max_length = 2)

# convert the output to a list
apriori_rules = list(apriori_rules)

# print out the first element
apriori_rules[0]

RelationRecord(items=frozenset({'hamburger meat', 'Instant food products'}), support=0.003050330452465684, ordered_statistics=[OrderedStatistic(items_base=frozenset({'Instant food products'}), items_add=frozenset({'hamburger meat'}), confidence=0.379746835443038, lift=11.42143769597027)])

```
<br>
The output from the algorithm is in the form of a generator. We covert this to a list as this is easier to manipulate & analyse.  

Based upon the parameters we set when applying the algorithm, we get 132 product pairs.  We print out the first element from the list to see what the output looks like, and while this contains all the key information we need - to make it easier to analyse (and more accessible & useable for stakeholders) - in the next code snippet, we extract the key elements and use list comprehension to re-work this data to exist as a Pandas DataFrame.

```python

# extract each piece of information
product1 = [list(rule[2][0][0])[0] for rule in apriori_rules]
product2 = [list(rule[2][0][1])[0] for rule in apriori_rules]
support = [rule[1] for rule in apriori_rules]
confidence = [rule[2][0][2] for rule in apriori_rules]
lift = [rule[2][0][3] for rule in apriori_rules]

# compile into a single dataframe
apriori_rules_df = pd.DataFrame({"product1" : product1,
                                 "product2" : product2,
                                 "support" : support,
                                 "confidence": confidence,
                                 "lift" : lift})

```
<br>
A sample of this data (the first 5 product pairs - not in any order) can be seen below:
<br>
<br>

| **product1** | **product2** | **support** | **confidence** | **lift** |
|---|---|---|---|---|
| Instant food products | hamburger meat | 0.003 | 0.380 | 11.421 |
| apple juice | tomato juice | 0.005 | 0.254 | 3.154 |
| baking powder | whipped/sour cream | 0.005 | 0.259 | 3.608 |
| beef | root vegetables | 0.0174 | 0.331 | 3.040 |
| berries | whipped/sour cream | 0.009 | 0.272 | 3.797 |
| … | … | … | … | … |

<br>
In the DataFrame we have the two products in the pair, and then the three key metrics; Support, Confidence, and Lift. 

___
<br>
# Interpreting The Results <a name="apriori-results"></a>

<br>
#### Associated Products

Now we have our data in a useable format - let's look at the product pairs with the *strongest* relationships - we can do this by sorting our Lift column, in descending order.

```python

# sort pairs by descending Lift
apriori_rules_df.sort_values(by = "lift", ascending = False, inplace = True)

```

<br>
In the table below, we can see the ten highest product relationships, based upon Lift
<br>
<br>

| **product1** | **product2** | **support** | **confidence** | **lift** |
|---|---|---|---|---|
| Instant food products | hamburger meat | 0.003 | 0.38 | 11.421 | 
| flour | sugar | 0.005 | 0.287 | 8.463 | 
| processed cheese | white bread | 0.004 | 0.252 | 5.975 | 
| mix fruit juice | tomato juice | 0.005 | 0.422 | 5.241 | 
| herbs | root vegetables | 0.007 | 0.431 | 3.956 | 
| berries | whipped/sour cream | 0.009 | 0.272 | 3.797 | 
| rice | root vegetables | 0.003 | 0.413 | 3.792 | 
| flour | margarine | 0.004 | 0.216 | 3.695 | 
| baking powder | whipped/sour cream | 0.005 | 0.259 | 3.608 | 
| flour | whipped/sour cream | 0.004 | 0.234 | 3.263 | 

<br>
Interestingly, the strongest relationship existed between two easy to cook food products - this is useful information for the category managers as they may want to ensure that these products are available in one section of the store, rather than existing in their respective product types.

We also saw some strong relationships between baking products - which again is extremely useful for category managers who are thinking about the best way to lay out the products - having sections by product category rather than necessarily by type might make it easier for customers to find what they are after.

<br>
#### Search Tool For Category Managers

With the data now stored as a DataFrame, we will also go back to the client with a proposal to build a simple "search" tool for Category Managers to use.

An example of how this might work would be to test a hypothesis around juice products.

The code below uses a string function to pull back all rows in the DataFrame where *product1* contains the words "juice"

```python

# search based upon text
apriori_rules_df[apriori_rules_df["product1"].str.contains("juice")]

```
<br>
The results of this search, in order of descending Lift are as follows:
<br>
<br>

| **product1** | **product2** | **support** | **confidence** | **lift** |
|---|---|---|---|---|
| mix fruit juice | tomato juice | 0.005 | 0.422 | 5.241 | 
| apple juice | tomato juice | 0.005 | 0.254 | 3.154 | 


<br>
There appears to be *some* relationship between juice products being bought together, but this should be investigated furthur

___
<br>
# Growth & Next Steps <a name="growth-next-steps"></a>

As this was first & foremost an exploratory project, we will take back the results to the client Category Managers & discuss the results, our views on how these insights can be actioned best, and any considerations that need to be taken into account when interpreting.

From there we will recommend applying this same logic to all other categories, as well as potentially across the full-product range.

We will also propose the build of the "Keyword Search Engine" which will help Category Managers extract and utilise the insights held within the data.
