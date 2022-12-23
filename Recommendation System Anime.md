# Anime Recommendation System - Fajri yanti

## Project Domain
Anime Recommendation System is a system for managing anime that contains information on anime as well as the user's preferred and most-watched episodes. It allows the user to classify and rank the programs they've watched. This technique aids individuals in discovering anime of interest to them. This project may examine the contents of the dataset to get insight. Later, linked cooperative filtering algorithms such as KNN is utilized to generate a list of the user's preferred anime, along with its rating. Our goal is to make it easier for otaku*  to get recommendations for new anime,  so that they never get bored.



*otaku: a Japanese term for those who like watching anime and reading manga.

## Business Understanding

The majority of an otaku's life revolves on Japanese popular culture, which consists mostly of viewing anime and being current on new releases. The primary issue comes after they have seen all of the available episodes or when their favorite television program is no longer showing. By then, they will be forced to find anything else to watch in order to escape boredom.

Due to the primary issue that they are dealing with, there are a number of indirect difficulties that we have identified and come up with. The most significant ones are as follows:

* Otakus need to obtain reliable ideas for anime depending on their preferences (for example, the kind of show, the amount of episodes, etc.).
* Otakus need to look for recommendations for anime that they haven't seen before and need to do it immediately.
* The majority of the time, Otaku watch the majority of the popular anime that is now available on the market, thus it is important for them to find undervalued anime (sometimes overrated).
* Otakus have a desire to know the context of the anime that has been offered to them, such as the genre, the year it first aired, a summary, and so on.
* Otakus have a responsibility to stay current on the latest news on anime, particularly with regard to the most anticipated anime releases of the year.


Problem Statement: 
To reach the goal, we used two techniques: a collaborative filtering system based on the knn algorithm and content-based filtering.

## Goals 

It is clear that the level of satisfaction experienced by otaku in relation to the recommended anime is the single most significant aspect that should be taken into account. The more closely it conforms to their tastes, the more satisfying the experience will be for them as users. Due to the possibility that they have previously seen one of the proposed anime, the research result from the anime recommendations should not be considered excessive. It need to produce more than one anime title in order to lessen the likelihood of the user having seen the anime in question before. Otakus should be given a variety of alternatives to pick from, such as obtaining information on anime or watching ideas for anime list.

From this analysis, the following features should be included into the creating a list of recommended anime, the user should be presented with a list of anime generated based on their preferences or the algorithm itself.



## Data Understanding

The data utilized in this project to undertake anime recommendation system was obtained from [kaggle](https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database). This data collection provides information on user preferences for 12,294 anime from 73,516 individuals. Each user may add anime to their finished list and rate it, and this data set is a compilation of those ratings.

#### The Description of Variables

* Anime.csv

|Variables  | Description  |
|--|--|
| anime_id   | myanimelist.net's unique id identifying an anime  |
| name|full name of anime |
| genre |  comma separated list of genres for this anime |
|type  |movie, special, music,  TV, OVA, ONA |
| episodes | how many episodes in this show. (1 if movie) |
|rating  |  average rating out of 10 for this anime |
| members  | number of community members that are in this anime's  "group" |
<figcaption align="center">Table 1.a - Dataset Anime </figcaption>  

* Rating.csv


|Variables| Description  |
|--|--|
|user_id   | non identifiable randomly generated user id |
|  anime_id   | the anime that this user has rated |
|rating  |rating out of 10 this user has assigned (-1 if the user watched it but didn't assign a rating)  |
<figcaption align="center">Table 1.b -Dataset Rating</figcaption>  


## Data Preparation
> Cleaning the data
* checking  null values using `.isnull().sum()` method and remove the missing values using `.drop`
>merge dataset
* This dataset contains two csv-formatted data collections, anime.csv and rating.csv. The dataset has  similar column named 'rating'  but different values. so, we need change the name column. We change the `rating` column in the anime.csv file with `avg_rating` and the second one with `user_rating`. We modify the column's name using the `.rename()` function.

* Merge the dataset using `.merge` and stored it to `df` .
`df = pd.merge(df_anime, df_rate, on='anime_id')` 

* Remove `user_rating == -1`
 `user_rating == -1` means  the user watched it but didn't assign a rating.  Since  `user_rating ==  -1`   not required in this analysis project because they have no bearing on the issue to be solved. We can eliminate them. We used `df_rate = df_rate.drop(df_rate[df_rate.rating == -1].index)`
 

> Exploratory Data Analysis (EDA)

* Statistical view of the dataset

|index|count|mean|std|min|25%|50%|75%|max|
|---|---|---|---|---|---|---|---|---|
|anime\_id|6337146\.0|8902\.5483888173|8881\.6739041292|1\.0|1239\.0|6213\.0|14075\.0|34475\.0|
|avg\_rating|6337146\.0|7\.675013424023994|0\.6699057620889904|2\.0|7\.29|7\.7|8\.15|9\.37|
|members|6337146\.0|184576\.84681432304|190953\.41223216086|33\.0|46803\.0|117091\.0|256325\.0|1013917\.0|
|user\_id|6337146\.0|36747\.95345444148|21013\.370972111774|1\.0|18985\.0|36815\.0|54873\.0|73516\.0|
|user\_rating|6337146\.0|7\.808542678360259|1\.5724395013636019|1\.0|7\.0|8\.0|9\.0|10\.0|
<figcaption align="center">Table 2 - Statistical View from The Dataset</figcaption>  

* Distribution of rating per user 
The majority of users have rated less than 200 anime.
<figure>  
<img  src="https://drive.google.com/uc?export=view&id=1RSrjZckVfLqd2xWaSL8Nxc5KJus63ca9">
<figcaption align="center">Fig.1 - Distribution of rating per user  </figcaption>  
</figure>

*  Distribution of User Rating 
 Most of the Anime ratings are spread between 6.0 - 9.0
 The mode of the users ratings distribution is around 7.0 - 8.0
<figure>  
<img  src="https://drive.google.com/uc?export=view&id=10Yd884IVILqdEYNV6IM4hGsu9hQYXdVL">
<figcaption align="center">Fig.2 - Distribution of User Rating </figcaption>  
</figure>

* Anime with the highest member community
'Death Note' has the most community members, followed by 'Shingeki no Kyojin' and 'Sword Art Online.'
<figure>  
<img  src="https://drive.google.com/uc?export=view&id=168J6nAnKkXTMkkbo7VmhBt2awZRZ8qiN">
<figcaption align="center">Fig.3 - Highest Community Member </figcaption>  
</figure>

* Anime with Highest User Rated Count
'Death Note,' 'Sword Art Online,' and 'Shingeki no Kyojin' are the three anime series that have the greatest ratings. 'Death Note' also has the biggest member community.
<figure>  
<img  src="https://drive.google.com/uc?export=view&id=1wVJXKuUpFRSiIIpsFFb9bKcqlVyUUV1I">
<figcaption align="center">Fig.4 - Anime User Rating</figcaption>  
</figure>

* Most viewed anime types based on count of reviews
TV has the most anime shows, and more than 4 million people watch them. The least number of users view at the music type.
<figure>  
<img  src="https://drive.google.com/uc?export=view&id=
1snrqOfmEIMrkArI0Tyy6oFQNhHDEOHlc">
<figcaption align="center">Fig.5 - Most Viewed Anime</figcaption>  
</figure>



## Modeling 

### Collaborative Filtering 
>In this project, the recommendation system aims to get a rating for an anime. It uses collaborative filtering system algorithm such as KNN to make an accurate recommendation to the users.

There are likely to be numerous people who rate an item in the same way that the user intended. Collaborative Filtering is guided by this comparable trend of their evaluations with the user[1]. Collaborative Filtering is based on the principle of recommending a product based on the preferences of others who share similar interests.

Collaborative Filtering Based on past behavior, this approach builds a model of the user. Users may have watched videos or given ratings on items in the past. Using this model, the model can predict the item or a rating for the item that a user might be interested in based on an association between the user and the item. KNN is used as a collaborative filtering approach in recommender systems.

KNN Collaborative filtering systems use the actions of users to recommend other most liked anime to the users. User-based approaches and item-based approaches can both be used. However, item-based approaches are usually preferred. Users are often more difficult to scale due to their dynamic nature, whereas items usually don't change too much and can be computed offline and served without constantly retraining. KNN can be used to develop a collaborative filtering system based on items in order to make inferences about new samples; it uses a database in which data points are grouped into several clusters.

Working of KNN Based on item feature similarity, KNN makes no assumptions about the distribution of the underlying data. As shown in table 3, it calculates the "distance" between the target most rated anime and every other anime in the database, then ranks their distances and returns the most liked anime's as recommendations.




|Recommendations for Hunter x Hunter (2011):   |  Distance  |
|--|--|
|1: Fullmetal Alchemist: Brotherhood  |  0.582916050148331 |
|2: Steins;Gate  | 0.6247773379089085  |
|3: Code Geass: Hangyaku no Lelouch R2 |  0.6543877255375563 |
|4: Gintama  |0.7415196494555457  |
|5: Gintama&#039  |0.7433903262344277  |
<figcaption align="center">Table 3 - Output Recommendation System using Collaborative Filtering </figcaption>  

### Content Based Filtering
Content-Based Filtering A user-provided data is used by a content-based recommender. When a user likes something, they give it a higher rating, whereas lower ratings indicate disinterest. In Content Based Filtering, these graded items serve as the 'content.' The user is then recommended future things based on this content that they might enjoy[2].

The content-based filtering uses the features of the items and a user’s profile created from the data of previous activities to generate recommendations. This technique is most appropriate when known information about the items (name, area, portrayal). In content-based recommender system, recommendations are treated as a user-specific classification problem where the classifier is trained based on the likes and dislikes of the user and item features. Items are described using keywords, and a client profile is created to store the kind of items the client likes. These algorithms use this user profile to recommend items similar to those the client is currently examining or enjoyed in the past.


**Term Frequency (TF) and Inverse Document Frequency (IDF)**

In content-based filtering mechanisms and information retrieval systems, the concepts of Term Frequency (TF) and Inverse Document Frequency (IDF) are used[3]. A word’s frequency in a document is referred to as TF. The inverse of document frequency throughout the entire corpus of documents is the IDF. They are used, among other things, to figure out the relative worth of any document, article, news item, or film.  The Vector Space Model is used to assess which things are more closely related to one another, and thus more closely related to the user profile. It calculates the distance between the vectors based on the angle between them[4].
<figure>  
<img  src="https://drive.google.com/uc?export=view&id=1XfbtRCkCbBEy3enL0y8azmWF_ufDd3iI">
<figcaption align="center">Fig.5 - TF-IDF</figcaption>  
</figure>


**Cosine similarity**
>
Cosine similarity  measures the similarity between two vectors of an inner product space. It is measured by the cosine of the angle between two vectors and determines whether two vectors are pointing in roughly the same direction. It is often used to measure document similarity in text analysis. A document can be represented by thousands of attributes, each recording the frequency of a particular word (such as a keyword) or phrase in the document. Thus, each document is an object represented by what is called a  _term-frequency vector_. Cosine similarity is a metric that measures the cosine of the angle between two vectors projected in a multi-dimensional space.

> The smaller the angle between the two vectors, the more similar they are to each other.

<figure>  
<img  src="https://drive.google.com/uc?export=view&id=1PUVlgFzwvMrvPvaDRa8qLq2gZCpFoaJI">
<figcaption align="center">Fig.6 - Cosine Similarity</figcaption>  
</figure>


**Cosine Distance:**

Usually, people use the cosine similarity as a similarity metric between vectors. Now, the cosine distance can be defined as follows:

> **Cosine Distance = 1 — Cosine Similarity**

The intuition behind this is that if 2 vectors are perfectly the same then the  **similarity**  is  **1**  (angle=0 hence 𝑐𝑜𝑠(𝜃)=1) and thus,  **distance**  is  **0**  (1–1=0).


|index|name|genre|
|---|---|---|
|0|Hunter x Hunter Movie: Phantom Rouge|Action, Adventure, Shounen, Super Power|
|1|Hunter x Hunter Movie: The Last Mission|Action, Adventure, Shounen, Super Power|
|2|Hunter x Hunter: Greed Island|Action, Adventure, Shounen, Super Power|
|3|Hunter x Hunter|Action, Adventure, Shounen, Super Power|
|4|Hunter x Hunter OVA|Action, Adventure, Shounen, Super Power|
<figcaption align="center">Table 4 - Output Recommendation System using Content Based Filtering </figcaption>  


## Evaluation

Precision and recall  determined using just the subset of  recommendations from ranks 1 to k. The recommended order is decided by the predicted value. For example, the anime with the greatest predicted value is rated number one, whereas the anime with the k-th highest predicted value is ranked number k.

<figure>  
<img  src="https://drive.google.com/uc?export=view&id=1bTcZTpEWS83wHz2sYSTJ1z3cgxJLKmJ8">
<figcaption align="center">Fig.5 - Evaluation</figcaption>  
</figure>


## Conclusion

Using datasets and algorithms, recommender systems provide new opportunities for personalized recommendations. It also helps alleviate the problem of information overload, which is a persistent concern with information retrieval systems, and gives users access to anime that are not immediately available to them on the system. This project examined the two recommendation approaches and emphasized their advantages and disadvantages. 

Anime recommendation sample result: 

> Recommendation System using Collaborative Filtering   

|Recommendations for Hunter x Hunter (2011):   |  Distance  |
|--|--|
|1: Fullmetal Alchemist: Brotherhood  |  0.582916050148331 |
|2: Steins;Gate  | 0.6247773379089085  |
|3: Code Geass: Hangyaku no Lelouch R2 |  0.6543877255375563 |
|4: Gintama  |0.7415196494555457  |
|5: Gintama&#039  |0.7433903262344277  |

>Recommendation System using Content-Based Filtering

    anime_recommendations('Hunter x Hunter (2011)')

|index|name|genre|
|---|---|---|
|0|Hunter x Hunter Movie: Phantom Rouge|Action, Adventure, Shounen, Super Power|
|1|Hunter x Hunter Movie: The Last Mission|Action, Adventure, Shounen, Super Power|
|2|Hunter x Hunter: Greed Island|Action, Adventure, Shounen, Super Power|
|3|Hunter x Hunter|Action, Adventure, Shounen, Super Power|
|4|Hunter x Hunter OVA|Action, Adventure, Shounen, Super Power|

In the content-based model, we utilized content data over time to recommend anime of a similar genre and style;   We also used the K-Nearest Neighbor algorithm, which displays five anime for each anime recommendation.

# 
### References

[1] Murali, M. Viswa, T. G. Vishnu, and Nancy Victor. "A Collaborative Filtering based Recommender System for Suggesting New Trends in Any Domain of Research." In 2019 5th International Conference on Advanced Computing & Communication Systems (ICACCS), pp. 550-553. IEEE, 2019.

[2] Shinde, Urmila, and Rajashree Shedge. "Comparative analysis of collaborative filtering technique." IOSR Journal of Computer Engineering 10 (2013): 77-82.

[3] Qaiser, S., & Ali, R. (2018). Text mining: use of TF-IDF to examine the relevance of words to documents. International Journal of Computer Applications, 181(1), 25-29.

[4] Bafna, P., Pramod, D., & Vaidya, A. (2016, March). Document clustering: TF-IDF approach. In 2016 International Conference on Electrical, Electronics, and Optimization Techniques (ICEEOT) (pp. 61-66). IEEE.

[4] Shinde, Urmila, and Rajashree Shedge. "Comparative analysis of collaborative filtering technique." IOSR Journal of Computer Engineering 10 (2013): 77-82.


> Written with [StackEdit](https://stackedit.io/).
