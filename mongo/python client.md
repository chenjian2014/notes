# PyMongo

```python
from pymongo import MongoClient, ASCENDING
from datetime import datetime

client = MongoClient(host="127.0.0.1", port=27017)

db = client.test

collection = db.posts

post = {
    "author": "Mike",
    "text": "My first blog post!",
    "tags": ["mongodb", "python", "pymongo"],
    "date": datetime.now()
}

post_id = collection.insert_one(post).inserted_id

mike_post = collection.find_one({"author": "Mike"})


new_posts = [
    {
        "author": "Mike",
        "text": "Another post!",
        "tags": ["bulk", "insert"],
        "date": datetime(2009, 11, 12, 11, 14)
    },
    {
        "author": "Eliot",
        "title": "MongoDB is fun",
        "text": "and pretty easy too!",
        "date": datetime(2009, 11, 10, 10, 45)
    }
]

post_ids = collection.insert_many(new_posts).inserted_ids

for i in collection.find({"author": "Mike"}):
    print(i)

collection.count_documents({"author": "Mike"})

collection.create_index([('author', ASCENDING)], unique=False)

collection.index_information()
```