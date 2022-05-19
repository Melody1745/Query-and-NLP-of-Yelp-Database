# Description
This dataset is a subset of Yelp's businesses, reviews, and user data. It contains 5 collections, including almost 7 million reviews, over 160 thousand businesses, the checkin record for almost 140 thousand businesses, and more than 900 thousand tips by nearly 2 million users. Each collection is stored in a json file. For this project, we use cloud service, like Amazon RDS and ArangoDB Oasis, for team work. Based on the database of MySql and Arango, we apply pymysql, mysql workbench and AQL to query, and apply Google data studio and python seaborn package for data visualization.

Source: https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset

# Part 1 Database Establishment
We initially choose to create a MySQL database in Amazon RDS, and connect to it through our local MySQL Workbench. In order to import the dataset into MySQL database, we create schema in MySQL, and set primary and foreign keys according to the structure of this dataset to link these tables together. Due to the fact that each of the json file is too large to be imported directly to mysql table, we decided to write functions to import the data by reading the records in json line by line. Importing nested json data is a little bit more complex, since dict or list type of data cannot be imported directly into mysql, so we changed the nested data to string at first, then convert it to tuple before inserting it to mysql table (see code file import_data_mysql). The EER diagram is shown below:

![1652976570(1)](https://user-images.githubusercontent.com/90291484/169347300-a5bcfe01-281b-4a65-a053-156417910ca4.png)

In order to look into the social network of the users, we also import the dataset into arangodb oasis, which is the cloud platform of arangodb. We extract the user dataset from mysql database through pandas. After creating a document collection, named 'Users', in Arangodb Oasis, we apply command to import user collection and set user_id as the unique key (see code file import_data_arangodb_oasis). For the edge document, we need to split the friends column into separate rows. Arango can create link automatically after importing edge file, but we should firstly add the vertex collection name in front of each id and change the column name to set 'from' and 'to' attributes to each node. Finally we can virtualize friendship of each user and process for social network analysis. Since there are nearly 500 thousand unique user_id but over 1.5 million unique friend id in total. Most of the users are isolated. Except for some review stars, people choose them to be friends to get more useful information about restaurants.

![1652983493(1)](https://user-images.githubusercontent.com/90291484/169369112-0ed7a108-3cd9-4170-b7e5-81d519d70e70.png)

# Part 2 Basic Query and EDA
For this dataset, we firstly look into each table and query for basic some information (see code file query), then we choose reviews, check_in, and star ratings to represent business reputation. We got those informations from 3 different tables and want to figure out the relationship among them. The scope of this study is all businesses in Massachusetts, so we filtered out businesses from other states. Also, in the reviews table, the reviews are in text format, we need to convert it into numerical format for the following analysis (see code file query_business). 

# Part 3 NLP
We used NLP technique to get the sentiment score for each review and average them by business, and applied the google data studio to produce an analytics tool to visualize the data. It allows us to visualize the data in real-time. If the data were updated, the charts will also be refreshed automatically. It can easily visualize the geographical information based on the longitude and latitude. We generated a interactive map to help people find the business they want. For example, you can specify the category and the rating, then the map will show the locations based on your condition (https://datastudio.google.com/reporting/7a8a9eb5-bdde-4c8b-b015-44ffb0d7b2d1). 

![1652989128(1)](https://user-images.githubusercontent.com/90291484/169388824-b74395e6-e594-49fd-a2d5-490c67cc911d.png)

Then, we made two bar charts to see the relationship between review, check in counts and stars. File 'get_review_score' shows the average review scores for each level of stars. The review score is increasing with stars.

![1652989284(1)](https://user-images.githubusercontent.com/90291484/169389246-471b9715-9e34-47bf-91b0-504dd9f97af2.png)

File 'get_checkin_count' shows the average number of check-ins for each level of stars. More check-ins happened on the average rated businesses.

![1652989387(1)](https://user-images.githubusercontent.com/90291484/169389493-9a610fec-7291-4e04-8e45-2965ccbfe704.png)

The restaurants with 3 stars appear more average number of check-ins. This may because of the average lower price, but we cannot verify this statement without price data. So more researched could be completed in the future, when the data of consumption per person is available.
