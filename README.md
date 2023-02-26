
Predicting Poverty Level using Geolocated Wikipedia Articles
==============

## Motivation
Current efforts to measure the progress of the United Nations Sustainable Development Goals (SDGs) are hampered by a dearth of data. 
For example, poverty — one of seventeen SDGs — is not measured often because it is both spatially sparse and infrequently collected in Sub-Saharan Africa due to the high cost of surveys.
Same for Syria and Iraq, due to the war. One possible way to measure these indicators is making use of the open texts available in Web.
In this project, we are trying to use the Wikipedia articles attached to specific locations to predict the povert level. 
We demonstrate that modern NLP techniques can be used to predict community-level asset Wikipedia articles.  

## United Nations Sustainable Development Goals (SDGs)
The 2030 Agenda for Sustainable Development, adopted by all United Nations Member States in 2015, provides a shared blueprint for peace and prosperity for people and the planet. At its heart are the 17 Sustainable Development Goals (SDGs), which are an urgent call by all countries - developed and developing - to work together toward ending poverty, improving health care and education, reducing inequality and protecting our oceans, forests and other natural resources (The 17 Goals | Sustainable Development. [United Nations](https://sdgs.un.org/goals)).

## Wikipedia as Source for Open Knowledge
Wikipedia is a large scale knowledge-repository where human from all the world collaborate to continuously add and update knowledge in the articles. The English version of Wikipedia (AKA The English Wikipedia) contains currently 6,622,837 articles with total of 1.14 e<sup>9</sup> edites, with average of 19.72 edits per page (Statistics. [In Wikipedia](https://en.wikipedia.org/wiki/Special:Statistics) accessed on 25/02/2023)

## Methods
The task can be defined as predicting the poverty level of a country location (represented by a latitude and longitude pair) using geolocated Wikipedia articles from a suitable neighborhood.
To accomplish that, we retrieve Wikipedia articles linked to each contry using [Wikimedia API](https://www.mediawiki.org/wiki/API:Main_page), then use NLP methods to build a regresson model to predict the continuos poverty precentage. Wikimedia API sensitivity to location is one major challenge, which makes it difficult to retrieve a sufficient number of articles linked to a country given the country geolocation. To solve that, we first get the all the cities in the country, then for each city, the city location is retrieved which is used then to retrieve the Wikipedia articles throught the Wikimedia API.

An additional challenge is posed by the by the type and the length of the articles. Some articles are short (less than 200 words), were generated automatically from structued knwoledge bases or don't really have usefull information related to the DSGs goals. These articles affected the model performance performance. To filter these articles out, we build a semantic similarity model to find the similarity between each Wikipedia article and set of base articles for each SDG. Then, filter those articles with similarity score less than a threshoold. After that, we built a regression model from the filrted articles.

The last challenge is the lack of baseline data. To build a regression model you need to have labeled data, which are the poverty level per country in our case. These data is not officially available for all countries in the last couple of years in the Unitied Nations reosouces. To overcome this issue, we relied on other sources to get the povery level data, such as [Statistica](https://www.statista.com/statistics/1237041/poverty-headcount-ratio-in-egypt) and [Unicef](https://www.unicef.org).