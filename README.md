
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

The work method can be summarized in the following steps:
1. Retrieve the list of countries from a text file
2. Retrieve the cities in each country using the [CountriesNow API](https://countriesnow.space) API
3. Retrieve the geolocation for each city using the [PositionStack](https://positionstack.com) API
4. Use the locations retrieved in step 3 to get all the linked Wikipedia articles titles using the [Wikimedia Geosearch](https://www.mediawiki.org/wiki/API:Geosearch) API method
5. Retrieve the article parsed content (raw text) using the article title retrieved in step 4 through the [Wikimedia action](https://www.mediawiki.org/wiki/API:Main_page#Uses_for_the_MediaWiki_Action_API) API method
6. Retrieve the SDGs base documentns listed in the sdgs_titles.json file
7. Create BERT-based Sentence Embeddings for the SDGS documents and the Wikiepdia-Geolocated documents
8. Compute the cosin similarity between each SDGS document and the Wikiepdia-Geolocated documents
9. For each SDG, get those  Wikiepdia-Geolocated documents with cosine similarity score score greater than the threshold
10. Feed the filtered documents into the BERT-based regression model

## Experiment
We devided the data into training and test sets with (0.7, 0.3) ratio, respectivaly and trained our Wikipedia Embedding (WE) model using a Root Mean Squared Error loss function, 10 epochs, 0.1 dropout, and batch size of 16. The country list contains the following 21 MENA countries: Egypt, Morocco, Tunisia, Libya, Sudan, Mauritania, Jordan, Lebanon, Turkey, Syria, Egypt, Iraq, Saudi Arabia, Yemen, Cyprus, Qatar, Oman, Iran, United Arab Emirates, Kuwait and Bahrain. The model was trained on two V100 GPU each with 24 GB memory.

## Results
We ran the expirement with different similarity lower score threshold and recordeed the rms value for each. Figure 1 shows the number of articles that are kept after using different similarity scores.
![# of Articles Filtered-in Per Similarity Threshold ](https://user-images.githubusercontent.com/1752446/221652907-36220860-852d-4cee-bcbf-c85835718c45.png)

And figure 2 shows the recorded observed Rmse per epoch for each test.
![RMSE@different Similarity Scores vs EPOCH](https://user-images.githubusercontent.com/1752446/221683714-0a980358-7d60-41f9-812a-480a4de90c9c.png)

From figure 2, the lowest Rmse value is obtained at similarity threshold =0.2 and the 8th epoch.
## Conclusion
In this project we verified the use of geolocated Wikipedia articles for socioeconomic applications. We did this by obtaining vector representations of articles on creating sentence embeds for SDG-like geolocated Wikipedia articles. We then combined these latent embeds with survey data and evaluated models to predict poverty outcomes: a Wikipedia embed model. Using this framework, we found that Wikipedia articles are informative about socioeconomic indicators. We have tested this model against the first SDG indicator, ie end poverty in all its forms everywhere, and we have achieved reasonable results as measured by the RMSE. The geolocated Wikipedia article dataset finds application not only in poverty analysis, but also in more general socio-economic forecasts, such as B. Educational and health-related outcomes. We hope that this approach will accelerate progress towards the UN SDGs by improving the way we estimate missing socio-economic indicators, particularly in developing countries, with the aim of improving responses from regional governments and international aid organizations.

## Run the code
To run the code:
1. Update the countries.txt file with the countries list
2. Run wikipedia_crawler.ipynb to retrieve the Wikipedia articles geographically linked to the countries. A new Article directory will be created in the same workspace directory along with country_articles.json file that contains the retrieved articles and their paths
3. Run sdgs_classifie.ipynb to build the model
