# nosql-project

In this project a UK Food Standards Agency evaluates establihsments across the UK and gives them hygiene ratings. I evaluated the data in order to help the Agency's journalists decide where to focus their articles. 

The first part of this project consisted of SetUp. I started by importing the data from the JSON file and created and named the database in Terminal. In Jupyter Notebook I made sure to import the PyMongo and Pretty Print libraries. Then the database was updated with a new establihsment in Greenwich, after inserting the data I confirmed that it had been inserted using find_one(). Then I began to perform some queries within the database. I updated a few parts of the restaruants information using update_one(). The journalists were not interested in any establishments in Dover specifically so I used the delete_many() command to delete all documents where the Local Authority Name is Dover. I was then asked to convert the latitude and longitude to decimal numbers which I completed using the update_many() command. 

In the second part of this project I did some exploratory analysis. I explored these three specific questions, and included some snippets of my code for each. 

**1. Which establishments have a hygiene score equal to 20?**

hygiene_query = {'scores.Hygiene': 20}
count = establishments.count_documents(hygiene_query)
print("Number of establishments with hygiene score of 20:", count)
first_document = establishments.find_one(hygiene_query)
pprint(first_document)

**2. Which establishments in London have a RatingValue greater than or equal to 4?**

rating_value_query = {'AddressLine3': "London", 'RatingValue': {'$gte': 4}}
count = establishments.count_documents(rating_value_query)
print("Number of establishments in London with a Rating Value of greater than or equal to 4:", count)
first_document = establishments.find_one(rating_value_query)
pprint(first_document)
  
**3. What are the top 5 establishments with a `RatingValue` rating value of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"?**
   
degree_search = 0.01
latitude = 51.49014200
longitude = 0.08384000
query = {'geocode.latitude': {'$gte': latitude-degree_search, '$lte': latitude+degree_search},
         'geocode.longitude': {'$gte': longitude-degree_search, '$lte': longitude+degree_search},
         'RatingValue': 5}
sort = [('score.Hygiene', 1)]
limit = 5
pprint(list(establishments.find(query).sort(sort).limit(limit)))

**4. How many establishments in each Local Authority area have a hygiene score of 0?**
   
match_query = {'$match': {'scores.Hygiene': 0}}
group_query = {'$group': {'_id': "$LocalAuthorityName", 'count': {'$sum': 1}}}
sort_values = {'$sort': {'count': -1}}
pipeline = [match_query, group_query, sort_values]
results = list(establishments.aggregate(pipeline))






