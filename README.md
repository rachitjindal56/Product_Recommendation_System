
# Product Recommendation Engine
Recommendation engines are now a one of the most common Machine Learning project that can be seen now-a-days.
In fact, some biggest brands are build around one, like  Netflix, Amazon, Google, etc. Thirty-five percent of purchases on Amazon come from product recommendations.

### What Is a Recommendation Engine?

A recommendation engine is a data filtering tool which uses machine learning algorithms to recommend the most relevant items to a user.

It operates on the principle of finding patterns in consumer behavior data, which can be collected implicitly or explicitly.
Netflix uses a recommendation engine to present viewers with movie and show suggestions. Amazon,uses a product recommendation engine to present customers with product recommendations.

In the past, recommendations would come from a salesperson or friends and family.
But today,this is done with the help of algorithms.

### Types of Recommendation Engines

There are three main types of recommendation engines: 
#### 1) Collaborative filtering: 

It focuses analyzing data of user behavior, activities, and preferences, to predict what a person will like, based on their similarity to other users.

#### 2) Content-Based filtering 

It works on the principle that if you like a particular item, you will also like this other item. To make recommendations, algorithms use a profile of the description of an item (genre, product type, color, word length) to work out the similarity of items using cosine and Euclidean distances.  

##### a) Vector based approach
##### b) Classification based approach


#### 3) Hybrid above of the two

A hybrid recommendation engine includes both the collaborative and the content-based data. Because of this, it outperforms both.

#### Will we be discussing content-based filtering vector approach on which this project is based on.

### How does Recommendation Engine (project) works?

#### Step 1) Preprocessing and converting the text to vectors:

##### 1) The primary step is to pre-process dataset to remove stopwords, punctuations, and perform lemmatization to avoid redundancy in data.

##### 2) Now, the available in string format and needs to converted to vectors for generating similarities. For that, pre-trained embeddings of tensorflow are used with suitable dimensions.

#### Step 2) Mathematical measures to determine how similar is a vector to a given vector.
Now, the correlation or similarities between the vector are to be calculated using mathematical algorithms.

Mathematical Similarity metrics used:
1) Cosine Similarity: The Cosine angle between the vectors.
2) Dot Product: The cosine angle and magnitude of the vectors also matters.
3) Euclidian Distance: The elementwise squared distance between two vectors

Using above algorithms, the indices of the vectors sorted on the bases of highest similarity are returned.
These indices are stored in a seperate file .pkl (pickle).

#### Step 3) Uploading the data to database for API creation:

Now this modified dataset containing the products, description, vectors, indices is stored on a MongoDB server to make predictions dynamically using API.

#### Step 4) API Creation (using Flask):

#### 1) Importing the vector indices file for performing the predictions and connecting using MongoDB.

The vector incides file should be present in the static folder so API can access it.

    data_path = 'static/[name_of_your_file].pkl'

    with open(data_path, 'rb') as f:
        data = pickle.load(f)

    CONNECTION_STRING = "[paste_mongodb_connectionstring]"
    connect = pymongo.MongoClient(CONNECTION_STRING,ssl=True,ssl_cert_reqs=ssl.CERT_NONE)


#### 2) Querying and Prediction generation:

Check out the file app.py for API:

    class Engine(Resource):
        
    def get(self,_id):

        # Creating ObjectId for querying database
        _id = ObjectId(str(_id))
        
        # Finding product for similar products arguments
        dist_args_description = products.find_one({"_id": _id})['dist_arg']
        
        # dist_args_category = products.find_one({"_id": _id})['dist_arg_category']
        # dist_args_type = products.find_one({"_id": _id})['dist_arg_type']
        # dist_args_features = products.find_one({"_id": _id})['dist_arg_features']
        
        
        similar_products_desc = [data[i]['product_name'] for i in dist_args_description]
        
        # similar_products_category = [data[i]['product_name'] for i in dist_args_category]
        # similar_products_type = [data[i]['product_name'] for i in dist_args_type]
        # similar_products_features = [data[i]['product_name'] for i in dist_args_features]
        
        id_products_desc = []
        
        # id_products_category = []
        # id_products_type = []
        # id_products_features = []
        
        for pd in similar_products_desc:
            id_products_desc.append(str(products.find_one({"proudct_name": pd})['_id']))
            
            # id_products_category.append(str(products.find_one({"proudct_name": pd})['_id']))
            # id_products_type.append(str(products.find_one({"proudct_name": pd})['_id']))
            # id_products_features.append(str(products.find_one({"proudct_name": pd})['_id']))

        output = {
            "_id":id_products_desc,
            "products":similar_products_desc
        }

        return output

    api.add_resource(Engine, '/<string:_id>')

It takes the product ObjectID as input and returns the ObjectID and product name to the server as a json file.

#### Step 5) Deployment:

The API can be deployed on cloud server like AWS, Heroku, etc. For deployment on Heroku refer to the following link:

https://devcenter.heroku.com/articles/getting-started-with-python#deploy-the-app

## Conclusion:

In this post, we have taken a look at the types of filtering mechanisms and their features. 
Also, a complete talk about deep learning-based content-based vector approach of filtering applications with a complete implementation of 
model from scratch to final API developement and deployment using Heroku.



## Authors

- [Rachit R Jindal](https://github.com/rachitjindal56)


## Tech Stack

**Server:** Python, Pandas, Tensorflow, API, Flask, MongoDB

