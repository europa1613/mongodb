### Connecting to a MongoDB Database

Unit 5 Lesson 2 Connecting to a MongoDB Atlas Cluster with the Shell

**Node REPL**
```javascript
const grettings = ["Hello", "world", "welcome"];
const loopArray = arr => arr.forEach(i => console.log(i));
loopArray(greetings);

```

#### Lab: 

```sh
$ MY_ATLAS_CONNECTION_STRING=$(atlas clusters connectionStrings describe myAtlasClusterEDU | sed "1 d")
$ mongosh -u myAtlasDBUser -p myatlas-001 $MY_ATLAS_CONNECTION_STRING
```

### Drivers

-   https://www.mongodb.com/docs/drivers/

```java
package com.mdbu.app;

import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import org.bson.Document;

import java.util.ArrayList;
import java.util.List;

public class Connection {

    public static void main(String[] args) {
        String connectionString = "mongodb+srv://myAtlasDBUser:myatlas-001@myatlasclusteredu.4284tya.mongodb.net";
        try (MongoClient mongoClient = MongoClients.create(connectionString)) {
            List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());
            databases.forEach(db -> System.out.println(db.toJson()));
        }
    }
}

```

```sh
mvn --quiet compile
mvn --quiet exec:java -Dexec.mainClass=com.mdbu.app.Connection
```

### MongoDB CRUD Operations: Insert and Find Documents

#### Inserting Documents in a MongoDB Collection

Review the following code, which demonstrates how to insert a single document and multiple documents into a collection.

##### Insert a Single Document

Use `insertOne()` to insert a document into a collection. Within the parentheses of `insertOne()`, include an object that contains the document data. Here's an example:

```javascript
db.grades.insertOne({
    student_id: 654321,
    products: [
        {
            type: "exam",
            score: 90,
        },
        {
            type: "homework",
            score: 59,
        },
        {
            type: "quiz",
            score: 75,
        },
        {
            type: "homework",
            score: 88,
        },
    ],
    class_id: 550,
});
```

##### Insert Multiple Documents

Use insertMany() to insert multiple documents at once. Within insertMany(), include the documents within an array. Each document should be separated by a comma. Here's an example:

```javascript
db.grades.insertMany([
    {
        student_id: 546789,
        products: [
            {
                type: "quiz",
                score: 50,
            },
            {
                type: "homework",
                score: 70,
            },
            {
                type: "quiz",
                score: 66,
            },
            {
                type: "exam",
                score: 70,
            },
        ],
        class_id: 551,
    },
    {
        student_id: 777777,
        products: [
            {
                type: "exam",
                score: 83,
            },
            {
                type: "quiz",
                score: 59,
            },
            {
                type: "quiz",
                score: 72,
            },
            {
                type: "quiz",
                score: 67,
            },
        ],
        class_id: 550,
    },
    {
        student_id: 223344,
        products: [
            {
                type: "exam",
                score: 45,
            },
            {
                type: "homework",
                score: 39,
            },
            {
                type: "quiz",
                score: 40,
            },
            {
                type: "homework",
                score: 88,
            },
        ],
        class_id: 551,
    },
]);
```

##### Finding Documents in a MongoDB Collection

```javascript
db.zips.find({ _id: ObjectId("5c8eccc1caa187d17ca6ed16") });
db.zips.find({ city: { $in: ["PHOENIX", "CHICAGO"] } });
db.sales.find({ storeLocation: { $in: ["London", "New York"] } });
```

##### Finding Documents by Using Comparison Operators

Review the following comparison operators: $gt, $lt, $lte, and $gte.

##### `$gt`

Use the $gt operator to match documents with a field greater than the given value. For example:

```javascript
db.sales.find({ "items.price": { $gt: 50 } });
```

##### `$lt`

Use the $lt operator to match documents with a field less than the given value. For example:

```javascript
db.sales.find({ "items.price": { $lt: 50 } });
```

##### `$lte`

Use the $lte operator to match documents with a field less than or equal to the given value. For example:

```javascript
db.sales.find({ "customer.age": { $lte: 65 } });
```

##### `$gte`

Use the $gte operator to match documents with a field greater than or equal to the given value. For example:

```javascript
db.sales.find({ "customer.age": { $gte: 65 } });
```

##### Querying on Array Elements in MongoDB

##### Queries for scalar products also

```javascript
db.accounts.find({ products: "InvestmentFund" });
```

**Only if part of an array:**

```javascript
db.accounts.find({
    products: {
        $elemMatch: { $eq: "InvestmentStock" },
    },
});
```

```javascript
db.sales.find({
    items: {
        $elemMatch: {
            name: "laptop",
            price: { $gt: 800 },
            quantity: { $gte: 1 },
        },
    },
});
```

##### Finding Documents by Using Logical Operators

Review the following logical operators: implicit `$and`, `$or`, and `$and`.

Find a Document by Using Implicit `$and`
Use implicit `$and` to select documents that match multiple expressions.

**For example:**

```javascript
db.routes.find({ "airline.name": "Southwest Airlines", stops: { $gte: 1 } });
```

Find a Document by Using the `$or` Operator
Use the `$or` operator to select documents that match at least one of the included expressions.

**For example:**

```javascript
db.routes.find({
    $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }],
});
```

Find a Document by Using the `$and` Operator
Use the `$and` operator to use multiple `$or` expressions in your query.

```javascript
db.routes.find({
    $and: [
        { $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }] },
        { $or: [{ "airline.name": "American Airlines" }, { airplane: 320 }] },
    ],
});
```

Find every document in the sales collection that meets the following criteria:

-   Purchased online
-   Used a coupon
-   Purchased by a customer 25 years old or younger

```javascript
db.sales.find({
    $and: [
        { purchaseMethod: "Online" },
        { couponUsed: true },
        { "customer.age": { $lte: 25 } },
    ],
});
```

Return every document in the sales collection that meets one of the following criteria:

-   Item with the name of pens
-   Item with a writing tag

```java
{
  _id: ObjectId("5bd761dcae323e45a93ccfe9"),
  saleDate: ISODate("2015-08-25T10:01:02.918Z"),
  items: [
    {
      name: 'envelopes',
      tags: [ 'stationary', 'office', 'general' ],
      price: Decimal128("8.05"),
      quantity: 10
    },
    {
      name: 'binder',
      tags: [ 'school', 'general', 'organization' ],
      price: Decimal128("28.31"),
      quantity: 9
    },
    {
      name: 'notepad',
      tags: [ 'office', 'writing', 'school' ],
      price: Decimal128("20.95"),
      quantity: 3
    },
    {
      name: 'laptop',
      tags: [ 'electronics', 'school', 'office' ],
      price: Decimal128("866.5"),
      quantity: 4
    },
    {
      name: 'notepad',
      tags: [ 'office', 'writing', 'school' ],
      price: Decimal128("33.09"),
      quantity: 4
    },
    {
      name: 'printer paper',
      tags: [ 'office', 'stationary' ],
      price: Decimal128("37.55"),
      quantity: 1
    },
    {
      name: 'backpack',
      tags: [ 'school', 'travel', 'kids' ],
      price: Decimal128("83.28"),
      quantity: 2
    },
    {
      name: 'pens',
      tags: [ 'writing', 'office', 'school', 'stationary' ],
      price: Decimal128("42.9"),
      quantity: 4
    },
    {
      name: 'envelopes',
      tags: [ 'stationary', 'office', 'general' ],
      price: Decimal128("16.68"),
      quantity: 2
    }
  ],
  storeLocation: 'Seattle',
  customer: { gender: 'M', age: 50, email: 'keecade@hem.uy', satisfaction: 5 },
  couponUsed: false,
  purchaseMethod: 'Phone'
}

```

**Answer:**

```javascript
db.sales.find({
    $or: [{ "items.name": "pens" }, { "items.tags": "writing" }],
});
```

### Replacing a Document in MongoDB

To replace documents in MongoDB, we use the `replaceOne()` method. The `replaceOne()` method takes the following parameters:

`filter:` A query that matches the document to replace.
`replacement:` The new document to replace the old one with.
`options:` An object that specifies options for the update.
In the previous video, we use the \_id field to filter the document. In our replacement document, we provide the entire document that should be inserted in its place. Here's the example code from the video:

```javascript
db.books.replaceOne(
    {
        _id: ObjectId("6282afeb441a74a98dbbec4e"),
    },
    {
        title: "Data Science Fundamentals for Python and MongoDB",
        isbn: "1484235967",
        publishedDate: new Date("2018-5-10"),
        thumbnailUrl:
            "https://m.media-amazon.com/images/I/71opmUBc2wL._AC_UY218_.jpg",
        authors: ["David Paper"],
        categories: ["Data Science"],
    }
);
```

### Updating MongoDB Documents by Using `updateOne()`

Updating MongoDB Documents by Using `updateOne()`
The `updateOne()` method accepts a filter document, an update document, and an optional options object. MongoDB provides update operators and options to help you update documents. In this section, we'll cover three of them: `$set`, `upsert`, and `$push`.

#### $set

The `$set` operator replaces the value of a field with the specified value, as shown in the following code:

```javascript
db.podcasts.updateOne(
    {
        _id: ObjectId("5e8f8f8f8f8f8f8f8f8f8f8"),
    },

    {
        $set: {
            subscribers: 98562,
        },
    }
);
```

#### upsert

The `upsert` option creates a new document if no documents match the filtered criteria. Here's an example:

```javascript
db.podcasts.updateOne(
    { title: "The Developer Hub" },
    { $set: { topics: ["databases", "MongoDB"] } },
    { upsert: true }
);
```

#### $push

The `$push` operator adds a new value to the hosts array field. Here's an example:

```javascript
db.podcasts.updateOne(
    { _id: ObjectId("5e8f8f8f8f8f8f8f8f8f8f8") },
    { $push: { hosts: "Nic Raboy" } }
);
```

#### Lab

```javascript
Atlas atlas-xbzsi3-shard-0 [primary] bird_data> db.birds.findOne({common_name: "Canada Goose"})
{
  _id: ObjectId("6268413c613e55b82d7065d2"),
  common_name: 'Canada Goose',
  scientific_name: 'Branta canadensis',
  wingspan_cm: 152.4,
  habitat: 'wetlands',
  diet: [ 'grass', 'algae' ],
  last_seen: ISODate("2022-05-19T20:20:44.083Z")
}

db.birds.updateOne( {_id: ObjectId("6268413c613e55b82d7065d2")} , {$set: {tags: ["geese", "herbivore", "migration"]} });


```

##### `$push` and `$each`

```javascript
Atlas atlas-xbzsi3-shard-0 [primary] bird_data> db.birds.findOne({_id: ObjectId("6268471e613e55b82d7065d7")})
{
  _id: ObjectId("6268471e613e55b82d7065d7"),
  common_name: 'Great Horned Owl',
  scientific_name: 'Bubo virginianus',
  wingspan_cm: 111.76,
  habitat: [ 'grasslands', 'farmland', 'tall forest' ],
  diet: [
    'mice',
    'small mammals',
    'rabbits',
    'shrews'
  ],
  last_seen: ISODate("2022-05-19T20:20:44.083Z")
}


```

Write a query that will update a document with an \_id of ObjectId("6268471e613e55b82d7065d7") and add the following to the diet array without removing any existing values:

`diet: ["newts", "opossum", "skunks", "squirrels"]`

```javascript
db.birds.updateOne(
    { _id: ObjectId("6268471e613e55b82d7065d7") },
    {
        $push: {
            diet: { $each: ["newts", "opossum", "skunks", "squirrels"] },
        },
    }
);
```

#### Return, Update, and Add a Document

Write a query to update a document in the birds collection.

-   In the filter document, use the common_name field with a value of Robin Redbreast to search for the document.
-   In the update document, use the $inc operator to increment the sightings field by 1. Additionally, your query should set a new field called last_updated to the current date and time, using new Date().
-   Add an option to the updateOne() method to create a new document if no documents match your query.

```javascript
db.birds.updateOne(
    {
        common_name: "Robin Redbreast",
    },
    {
        $inc: {
            sightings: 1,
        },
        $set: {
            last_updated: new Date(),
        },
    },
    {
        upsert: true,
    }
);
```

#### Updating MongoDB Documents by Using findAndModify()

The `findAndModify()` method is used to find and replace a single document in MongoDB. It accepts a filter document, a replacement document, and an optional options object. The following code shows an example:

```javascript
db.podcasts.findAndModify({
    query: { _id: ObjectId("6261a92dfee1ff300dc80bf1") },
    update: { $inc: { subscribers: 1 } },
    new: true,
});
```

##### Lab

```javascript
db.birds.findAndModify({
    query: { common_name: "Blue Jay" },
    update: { $inc: { sightings_count: 1 } },
    new: true,
});
```

#### Deleting Documents in MongoDB

To delete documents, use the deleteOne() or deleteMany() methods. Both methods accept a filter document and an options object.

##### Delete One Document

The following code shows an example of the deleteOne() method:

```javascript
db.podcasts.deleteOne({ _id: Objectid("6282c9862acb966e76bbf20a") });
```

##### Delete Many Documents

The following code shows an example of the deleteMany() method:

```javascript
db.podcasts.deleteMany({category: “crime”})
```

#### Sorting and Limiting Query Results in MongoDB

Review the following code, which demonstrates how to sort and limit query results.

##### Sorting Results

Use `cursor.sort()` to return query results in a specified order. Within the parentheses of `sort()`, include an object that specifies the field(s) to sort by and the order of the sort. Use 1 for ascending order, and -1 for descending order.

**Syntax:**

```javascript
db.collection.find(<query>).sort(<sort>)
```

**Example:**

```javascript
// Return data on all music companies, sorted alphabetically from A to Z.
db.companies.find({ category_code: "music" }).sort({ name: 1 });
```

To ensure documents are returned in a consistent order, include a field that contains unique values in the sort. An easy way to do this is to include the \_id field in the sort. Here's an example:

```javascript
// Return data on all music companies, sorted alphabetically from A to Z. Ensure consistent sort order
db.companies.find({ category_code: "music" }).sort({ name: 1, _id: 1 });
```

##### Limiting Results

Use `cursor.limit()` to return query results in a specified order. Within the parentheses of limit(), specify the maximum number of documents to return.

**Syntax:**

```javascript
db.companies.find(<query>).limit(<number>)
```

**Example:**

```javascript
// Return the three music companies with the highest number of employees. Ensure consistent sort order.
db.companies
    .find({ category_code: "music" })
    .sort({ number_of_employees: -1, _id: 1 })
    .limit(3);
```

##### Lab

```javascript
db.sales.find({}).sort({ saleDate: 1 });

db.sales
    .find({ purchaseMethod: "Online", couponUsed: true })
    .sort({ saleDate: -1 });

//Return a Limited Number of Sorted Results
db.sales
    .find({
        "items.name": { $in: ["laptop", "backpack", "printer paper"] },
        storeLocation: "London",
    })
    .sort({ saleDate: -1 })
    .limit(3);
```

#### Returning Specific Data from a Query in MongoDB

Review the following code, which demonstrates how to return selected fields from a query.

##### Add a Projection Document

To specify fields to include or exclude in the result set, add a projection document as the second parameter in the call to db.collection.find().

##### Syntax:

```javascript
db.collection.find( <query>, <projection> )
```

**Include a Field**
To include a field, set its value to 1 in the projection document.

**Syntax:**

```javascript
db.collection.find( <query>, { <field> : 1 })
```

**Example:**

```javascript
// Return all restaurant inspections - business name, result, and _id fields only
db.inspections.find(
    { sector: "Restaurant - 818" },
    { business_name: 1, result: 1 }
);
```

**Exclude a Field**
To exclude a field, set its value to 0 in the projection document.

**Syntax:**

```javascript
db.collection.find(query, { <field> : 0, <field>: 0 })
```

**Example:**

```javascript
// Return all inspections with result of "Pass" or "Warning" - exclude date and zip code
db.inspections.find(
    { result: { $in: ["Pass", "Warning"] } },
    { date: 0, "address.zip": 0 }
);
```

While the `_id` field is included by default, it can be suppressed by setting its value to 0 in any projection.

```javascript
// Return all restaurant inspections - business name and result fields only
db.inspections.find(
    { sector: "Restaurant - 818" },
    { business_name: 1, result: 1, _id: 0 }
);
```

#### Counting Documents in a MongoDB Collection

Review the following code, which demonstrates how to count the number of documents that match a query.

##### Count Documents

Use db.collection.countDocuments() to count the number of documents that match a query. countDocuments() takes two parameters: a query document and an options document.

##### Syntax:

```javascript
db.collection.countDocuments( <query>, <options> )
```

The query selects the documents to be counted.

###### Examples:

```javascript
// Count number of docs in trip collection
db.trips.countDocuments({});
// Count number of trips over 120 minutes by subscribers
db.trips.countDocuments({ tripduration: { $gt: 120 }, usertype: "Subscriber" });
```

## MongoDB CRUD Operations in Java

#### Inserting a document in Java apps

```java
Document doc1 = new Document().append("account_holder","Mary Jane")
                .append("account_id","MDB123123123")
                .append("balance",2000)
                .append("account_type","savings");

InsertOneResult result  = collection.insertOne(doc1);
```

#### Inserting many document in Java apps - Lab

```
mvn --quiet compile
mvn --quiet exec:java -Dexec.mainClass=com.mdbu.app.DemoApp
```

```java
public void insertManyDocuments(List<Document> documents) {
  InsertManyResult result = collection.insertMany(documents);
  System.out.println("\tTotal # of documents: " + result.getInsertedIds().size());
}
```

#### Querying a MongoDB Collection in Java Applications

Review the following code, which demonstrates how to query documents in MongoDB with Java.

##### Using `find()`

In the following example, we find all accounts that have a balance greater than or equal to 1000 and are checking accounts. We process each document that’s returned from the `find()` method by iterating the MongoCursor. The `find()` method accepts a query filter and returns documents that match the filter in the collection.

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
try(MongoCursor<Document> cursor = collection.find(and(gte("balance", 1000),eq("account_type","checking")))
        .iterator())
{
    while(cursor.hasNext()) {
        System.out.println(cursor.next().toJson());
    }
}
```

##### Using `find().first()`

Chaining together the `find()` and `first()` methods finds the first matching document for the query filter that's passed into the `find()` method. In the following example, we return a single document from the same query:

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Document doc = collection.find(Filters.and(gte("balance", 1000), Filters.eq("account_type", "checking"))).first();
System.out.println(doc.toJson());
```

##### Lab

```java

public void findOneDocument(Bson query) {
  Document doc = collection.find(query).first();
  System.out.println(doc != null ? doc.toJson() : null);
}

public void findDocuments(Bson query) {
  try (MongoCursor<Document> cursor = collection.find(query).iterator()) {
  while (cursor.hasNext()) {
      System.out.println(cursor.next().toJson());
    }
  }
}


```

### Updating Documents in Java Applications

Review the following code, which demonstrates how to update documents in MongoDB with Java.

##### Using updateOne()

To update a single document, we use the `updateOne()` method on a MongoCollection object. The method accepts a filter that matches the document that we want to update, and an update statement that instructs the driver how to change the matching document. The `updateOne()` method updates only the first document that matches the filter.

In the following example, we update one document by increasing the balance of a specific account by 100 and setting the account status to active:

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Bson query  = Filters.eq("account_id","MDB12234728");
Bson updates  = Updates.combine(Updates.set("account_status","active"),Updates.inc("balance",100));
UpdateResult upResult = collection.updateOne(query, updates);
```

##### Using updateMany()

To update multiple documents, we use the `updateMany()` method on a MongoCollection object. The method accepts a filter that matches the document that we want to update, and an update statement that instructs the driver how to change the matching document. The `updateMany()` method updates all the documents in the collection that match the filter.

In the following example, we update many documents by adding a minimum balance of 100 to all savings accounts:

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Bson query  = Filters.eq("account_type","savings");
Bson updates  = Updates.combine(Updates.set("minimum_balance",100));
UpdateResult upResult = collection.updateMany(query, updates);
```

#### Lab

##### `updateOne()`

```java

package com.mdbu.app;

import ch.qos.logback.classic.Level;
import ch.qos.logback.classic.Logger;
import com.mdbu.aggregations.Aggregation;
import com.mdbu.crud.Crud;
import com.mdbu.transactions.Transaction;
import com.mongodb.client.*;
import com.mongodb.client.model.Aggregates;
import com.mongodb.client.model.Filters;
import com.mongodb.client.model.Updates;
import org.bson.Document;
import org.bson.conversions.Bson;
import org.bson.types.ObjectId;
import org.slf4j.LoggerFactory;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import static com.mongodb.client.model.Aggregates.match;
import static com.mongodb.client.model.Filters.*;
import static com.mongodb.client.model.Projections.*;
import static com.mongodb.client.model.Sorts.descending;
import static com.mongodb.client.model.Sorts.orderBy;
import static java.util.Arrays.asList;


public class DemoApp {
    public static void main(final String[] args) {
        Logger root = (Logger) LoggerFactory.getLogger("org.mongodb.driver");
        // Available levels are: OFF, ERROR, WARN, INFO, DEBUG, TRACE, ALL
        root.setLevel(Level.WARN);

        String connectionString = System.getenv("MONGODB_URI");
        try (MongoClient client = MongoClients.create(connectionString)) {
            //CRUD
            Crud crud = new Crud(client);
            //UPDATE ONE
            Bson query = Filters.eq("account_id", "MDB333829449"); //TODO: define the query variable
            Bson update = Updates.combine(Updates.set("account_status", "active"), Updates.inc("balance", 100)); //TODO: define the update variable
            crud.updateOneDocument(query, update);
        }
    }
}
```

```java

package com.mdbu.crud;

import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.result.DeleteResult;
import com.mongodb.client.result.UpdateResult;
import com.mongodb.client.result.InsertOneResult;
import org.bson.Document;
import org.bson.BsonValue;
import org.bson.conversions.Bson;

import java.util.ArrayList;
import java.util.List;

public class Crud {
    private final MongoCollection<Document> collection;

    public Crud(MongoClient client) {
        this.collection = client.getDatabase("bank").getCollection("accounts");
    }

    public void updateOneDocument(Bson query, Bson update) {
        //TODO: Implement the updateOne operation
        UpdateResult upResult = collection.updateOne(query, update);
        System.out.println(upResult);
    }
}

```

##### `updateMany()`

```java
package com.mdbu.app;

import ch.qos.logback.classic.Level;
import ch.qos.logback.classic.Logger;
import com.mdbu.aggregations.Aggregation;
import com.mdbu.crud.Crud;
import com.mdbu.transactions.Transaction;
import com.mongodb.client.*;
import com.mongodb.client.model.Aggregates;
import com.mongodb.client.model.Filters;
import com.mongodb.client.model.Updates;
import org.bson.Document;
import org.bson.conversions.Bson;
import org.bson.types.ObjectId;
import org.slf4j.LoggerFactory;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import static com.mongodb.client.model.Aggregates.match;
import static com.mongodb.client.model.Filters.*;
import static com.mongodb.client.model.Projections.*;
import static com.mongodb.client.model.Sorts.descending;
import static com.mongodb.client.model.Sorts.orderBy;
import static java.util.Arrays.asList;


public class DemoApp {
    public static void main(final String[] args) {
        Logger root = (Logger) LoggerFactory.getLogger("org.mongodb.driver");
        // Available levels are: OFF, ERROR, WARN, INFO, DEBUG, TRACE, ALL
        root.setLevel(Level.WARN);

        String connectionString = System.getenv("MONGODB_URI");
        try (MongoClient client = MongoClients.create(connectionString)) {
            //CRUD
            Crud crud = new Crud(client);
            //UPDATE MANY
            Document updateManyFilter = new Document().append("account_type", "savings");
            Bson updatesForMany = Updates.combine(Updates.set("minimum_balance", 100));
            crud.updateManyDocuments(updateManyFilter, updatesForMany);
        }
    }
}

```

```java
package com.mdbu.crud;

import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.result.DeleteResult;
import com.mongodb.client.result.UpdateResult;
import com.mongodb.client.result.InsertOneResult;
import org.bson.Document;
import org.bson.BsonValue;
import org.bson.conversions.Bson;

import java.util.ArrayList;
import java.util.List;

public class Crud {
    private final MongoCollection<Document> collection;

    public Crud(MongoClient client) {
        this.collection = client.getDatabase("bank").getCollection("accounts");
    }

    public void updateManyDocuments(Bson query, Bson update) {
        //TODO: Implement the updateMany operation
        UpdateResult updates = collection.updateMany(query, update);
        System.out.println(updates);
    }
}


```

#### Deleting Documents in Java Applications

Review the following code, which demonstrates how to delete documents in MongoDB with Java.

##### Using `deleteOne()`

To delete a single document from a collection, we use the `deleteOne()` method on a MongoCollection object. The method accepts a query filter that matches the document that we want to delete. If we don't specify a filter, MongoDB matches the first document in the collection. The `deleteOne()` method deletes only the first document that matches.

In the following example, we delete a single document that pertains to John Doe's account:

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Bson query = Filters.eq("account_holder", "john doe");
DeleteResult delResult = collection.deleteOne(query);
System.out.println("Deleted a document:");
System.out.println("\t" + delResult.getDeletedCount());
```

##### Using `deleteMany()` with a Query Object

To delete multiple documents from a collection in a single operation, we call the `deleteMany()` method on a MongoCollection object. To specify which documents to delete, we pass a query filter that matches the documents that we want to delete. If we provide an empty document, MongoDB matches all documents in the collection and deletes them.

In the following example, we delete all dormant accounts by using a query object. Then, we print the total number of deleted documents.

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Bson query = eq("account_status", "dormant");
DeleteResult delResult = collection.deleteMany(query);
System.out.println(delResult.getDeletedCount());
```

##### Using `deleteMany()` with a Query Filter

To delete multiple documents from a collection in a single operation, we call the `deleteMany()` method on a MongoCollection object. To specify which documents to delete, we pass a query filter that matches the documents that we want to delete. If we provide an empty document, MongoDB matches all documents in the collection and deletes them.

In the following example, we delete all dormant accounts by using a query filter. Then, we print the total number of deleted documents.

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
DeleteResult delResult = collection.deleteMany(Filters.eq("account_status", "dormant"));
System.out.println(delResult.getDeletedCount());
```

### Creating MongoDB Transactions in Java Applications

Review the following code, which demonstrates how to create multi-document transactions in MongoDB with Java.

#### Creating a Transaction

To start the transaction, we use the session’s `WithTransaction()` method. We then define the sequence of operations to perform inside the transaction. Here are the steps to complete a multi-document transaction, followed by the code:

1. Start a new session.
2. Begin a transaction with the `WithTransaction()` method on the session.
3. Create variables that will be used in the transaction.
4. Obtain the user accounts information that will be used in the transaction.
5. Create the tranfers document.
6. Update the user accounts.
7. Insert the transfer document.
8. Commit the transaction.

**The following code demonstrates these steps:**

```java
final MongoClient client = MongoClients.create(connectionString);
final ClientSession clientSession = client.startSession();

TransactionBody txnBody = new TransactionBody<String>(){
    public String execute() {
        MongoCollection<Document> bankingCollection = client.getDatabase("bank").getCollection("accounts");

        Bson fromAccount = eq("account_id", "MDB310054629");
        Bson withdrawal = Updates.inc("balance", -200);

        Bson toAccount = eq("account_id", "MDB643731035");
        Bson deposit = Updates.inc("balance", 200);

        System.out.println("This is from Account " + fromAccount.toBsonDocument().toJson() + " withdrawn " + withdrawal.toBsonDocument().toJson());
        System.out.println("This is to Account " + toAccount.toBsonDocument().toJson() + " deposited " + deposit.toBsonDocument().toJson());
        bankingCollection.updateOne(clientSession, fromAccount, withdrawal);
        bankingCollection.updateOne(clientSession, toAccount, deposit);

        return "Transferred funds from John Doe to Mary Doe";
    }
};

try {
    clientSession.withTransaction(txnBody);
} catch (RuntimeException e){
    System.out.println(e);
}finally{
    clientSession.close();
}
```

#### Lab

```java

package com.mdbu.app;

import ch.qos.logback.classic.Level;
import ch.qos.logback.classic.Logger;
import com.mdbu.aggregations.Aggregation;
import com.mdbu.crud.Crud;
import com.mdbu.transactions.Transaction;
import com.mongodb.client.*;
import com.mongodb.client.model.Aggregates;
import com.mongodb.client.model.Filters;
import com.mongodb.client.model.Updates;
import org.bson.Document;
import org.bson.conversions.Bson;
import org.bson.types.ObjectId;
import org.slf4j.LoggerFactory;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import static com.mongodb.client.model.Aggregates.match;
import static com.mongodb.client.model.Filters.*;
import static com.mongodb.client.model.Projections.*;
import static com.mongodb.client.model.Sorts.descending;
import static com.mongodb.client.model.Sorts.orderBy;
import static java.util.Arrays.asList;


public class DemoApp {
    public static void main(final String[] args) {
        Logger root = (Logger) LoggerFactory.getLogger("org.mongodb.driver");
        // Available levels are: OFF, ERROR, WARN, INFO, DEBUG, TRACE, ALL
        root.setLevel(Level.WARN);

        String connectionString = System.getenv("MONGODB_URI");
        try (MongoClient client = MongoClients.create(connectionString)) {
            //Transaction
            Transaction txn = new Transaction(client);
            var senderAccountFilter = "MDB310054629";
            var receiverAccountFilter = "MDB643731035";
            double transferAmount = 200;
            txn.transferMoney(senderAccountFilter, transferAmount, receiverAccountFilter);
        }
    }
}

```

```java
package com.mdbu.transactions;

import com.mongodb.client.ClientSession;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.TransactionBody;
import com.mongodb.client.model.Updates;
import org.bson.Document;
import org.bson.conversions.Bson;
import org.bson.types.ObjectId;

import java.util.UUID;
import java.util.Date;

import static com.mongodb.client.model.Filters.eq;
import static com.mongodb.client.model.Updates.inc;
import static com.mongodb.client.model.Updates.push;

public class Transaction {
    private final MongoClient client;

    public Transaction(MongoClient client) {
        this.client = client;
    }

    public void transferMoney(String accountIdOfSender, double transactionAmount, String accountIdOfReceiver) {
        try (ClientSession session = client.startSession()) {
            UUID transfer = UUID.randomUUID();
            String transferId = transfer.toString();
            try {
                session.withTransaction(() -> {
                    MongoCollection<Document> accountsCollection = client.getDatabase("bank").getCollection("accounts");
                    MongoCollection<Document> transfersCollection = client.getDatabase("bank").getCollection("transfers");


                    Bson senderAccountFilter = eq("account_id", accountIdOfSender);
                    Bson debitUpdate = Updates.combine(inc("balance", -1 * transactionAmount),push("transfers_complete", transferId));

                    Bson receiverAccountId = eq("account_id", accountIdOfReceiver);
                    Bson credit = Updates.combine(inc("balance", transactionAmount), push("transfers_complete", transferId));

                    transfersCollection.insertOne(session, new Document("_id", new ObjectId()).append("transfer_id", transferId).append("to_account", accountIdOfReceiver).append("from_account", accountIdOfSender).append("amount", transactionAmount).append("last_updated", new Date()));
                    accountsCollection.updateOne(session, senderAccountFilter, debitUpdate);
                    accountsCollection.updateOne(session, receiverAccountId, credit);
                    return null;
                });
            } catch (RuntimeException e) {
                throw e;
            }
        }
    }
}

```

### Introduction to MongoDB Aggregation

This section contains key definitions for this lesson, as well as the code for an aggregation pipeline.

#### Definitions

**Aggregation:** Collection and summary of data
**Stage:** One of the built-in methods that can be completed on the data, but does not permanently alter it
**Aggregation pipeline:** A series of stages completed on the data in order

#### Structure of an Aggregation Pipeline

```javascript
db.collection.aggregate([
    {
        $stage1: {
            { expression1 },
            { expression2 }...
        },
        $stage2: {
            { expression1 }...
        }
    }
])
```

#### Using `$match` and `$group` Stages in a MongoDB Aggregation Pipeline

Review the following sections, which show the code for the $match and $group aggregation stages.

#### `$match`

The `$match` stage filters for documents that match specified conditions. Here's the code for `$match`:

```javascript
{
  $match: {
     "field_name": "value"
  }
}
```

#### $group

The `$group` stage groups documents by a group key.

```javascript
{
  $group:
    {
      _id: <expression>, // Group key
      <field>: { <accumulator> : <expression> }
    }
 }
```

#### `$match` and `$group` in an Aggregation Pipeline

The following aggregation pipeline finds the documents with a field named "state" that matches a value "CA" and then groups those documents by the group key "$city" and shows the total number of zip codes in the state of California.

```javascript
db.zips.aggregate([
    {
        $match: {
            state: "CA",
        },
    },
    {
        $group: {
            _id: "$city",
            totalZips: { $count: {} },
        },
    },
]);
```

##### Lab

```javascript
db.sightings.aggregate([
    {
        $match: {
            species_common: "Eastern Bluebird",
        },
    },
    {
        $group: {
            _id: "$location.coordinates",
            number_of_sightings: {
                $count: {},
            },
        },
    },
])[
    //output
    ({ _id: [41, -74], number_of_sightings: 1 },
    { _id: [40, -74], number_of_sightings: 3 },
    { _id: [40, -73], number_of_sightings: 1 })
];
```

---

#### Using `$sort` and `$limit` Stages in a MongoDB Aggregation Pipeline

Review the following sections, which show the code for the `$sort` and `$limit` aggregation stages.

##### `$sort`

The `$sort` stage sorts all input documents and returns them to the pipeline in sorted order. We use 1 to represent ascending order, and -1 to represent descending order.

```javascript
{
    $sort: {
        "field_name": 1
    }
}
```

##### $limit

The `$limit` stage returns only a specified number of records.

```javascript
{
    $limit: 5;
}
```

##### `$sort` and `$limit` in an Aggregation Pipeline

The following aggregation pipeline sorts the documents in descending order, so the documents with the greatest pop value appear first, and limits the output to only the first five documents after sorting.

```javascript
db.zips.aggregate([
    {
        $sort: {
            pop: -1,
        },
    },
    {
        $limit: 5,
    },
]);
```

#### Lab

```javascript
db.sightings.aggregate([
    {
        $sort: {
            "location.coordinates.1": -1, // <==== Array .<index> reference
        },
    },
    {
        $limit: 4,
    },
]);
```

### Using `$project`, `$count`, and $`set` Stages in a MongoDB Aggregation Pipeline

Review the following sections, which show the code for the `$project`, `$set`, and `$count`aggregation stages.

#### `$project`

The `$project` stage specifies the fields of the output documents. 1 means that the field should be included, and 0 means that the field should be supressed. The field can also be assigned a new value.

```javascript
{
    $project: {
        state:1,
        zip:1,
        population:"$pop",
        _id:0
    }
}
```

#### `$set`

The `$set` stage creates new fields or changes the value of existing fields, and then outputs the documents with the new fields.

```javascript
{
    $set: {
        place: {
            $concat:["$city",",","$state"]
        },
        pop:10000
     }
  }
```

#### `$count`

The `$count` stage creates a new document, with the number of documents at that stage in the aggregation pipeline assigned to the specified field name.

```javascript
{
    $count: "total_zips";
}
```

##### Lab `$project`, `$set` & `$count`

```javascript
//$project
db.sightings.aggregate([
    {
        $project: {
            _id: 0,
            species_common: 1,
            date: 1,
        },
    },
]);

//$set
db.birds.aggregate([
    {
        $set: {
            class: "bird",
        },
    },
]);

//$count
db.sightings.aggregate([
    {
        $match: {
            date: {
                $gt: ISODate("2022-01-01T00:00:00.000Z"),
                $lt: ISODate("2023-01-01T00:00:00.000Z"),
            },
            species_common: "Eastern Bluebird",
        },
    },
    {
        $count: "bluebird_sightings_2022",
    },
]);
```

##### Lab `$out`

```javascript
db.sightings.aggregate([
    {
        $match: {
            date: {
                $gte: ISODate("2022-01-01T00:00:00.0Z"),
                $lt: ISODate("2023-01-01T00:00:00.0Z"),
            },
        },
    },
    {
        $out: "sightings_2022",
    },
]);

//findOne from the $out collection
db.sightings_2022.findOne();
```

#### Summary MongoDB Aggregation

In this unit, you learned how to use aggregation in MongoDB and create an aggregation pipeline. You also learned how to use several of the most common aggregation stages, including:

-   $match
-   $group
-   $sort
-   $limit
-   $project
-   $count
-   $set
-   $out

### MongoDB Aggregation with Java

#### Using `$match` and `$group`

Review the following code, which demonstrates how to build the `$match` and `$group` stages of an aggregation pipeline in MongoDB with Java.

##### `$match` and `$group`

In both of the following examples, we use the `Aggregates` builder class to build the `$match` and `$group` stages of an aggregation pipeline.

First, we use `$match` to find an account that belongs to a customer. We start by creating a new method called `matchStage` that we call in the main method. In `matchStage`, we use the `Filters` builder class to specify which account we're searching for. The `match()` method creates a $match pipeline stage that matches incoming documents against the specified query filter and filters out documents that don't match.

```java
public static void main(String[] args) {
    String connectionString = System.getProperty("mongodb.uri");
    try (MongoClient mongoClient = MongoClients.create(connectionString)) {
        MongoDatabase db = mongoClient.getDatabase("bank");
        MongoCollection<Document> accounts = db.getCollection("accounts");
        matchStage(accounts);
    }
}

private static void matchStage(MongoCollection<Document> accounts){
    Bson matchStage = Aggregates.match(Filters.eq("account_id", "MDB310054629"));
    System.out.println("Display aggregation results");
    accounts.aggregate(Arrays.asList(matchStage)).forEach(document->System.out.print(document.toJson()));
}
```

In the next example, we use `$match` and `$group` to find the average and total balances of the customer's accounts. We start by creating a new method called `matchAndGroupStages` that we call in the main method. The `match()` method creates a `$match` pipeline stage that matches incoming documents against the specified query filter and filters out documents that don't match. The `group()` method then creates a `$group` pipeline stage to group documents by a specified expression and output a document for each distinct grouping.

```java
public static void main(String[] args) {
    String connectionString = System.getProperty("mongodb.uri");
    try (MongoClient mongoClient = MongoClients.create(connectionString)) {
        MongoDatabase db = mongoClient.getDatabase("bank");
        MongoCollection<Document> accounts = db.getCollection("accounts");
        matchAndGroupStages(accounts);
    }
}

private static void matchAndGroupStages(MongoCollection<Document> accounts){
    Bson matchStage = Aggregates.match(Filters.eq("account_id", "MDB310054629"));
    Bson groupStage = Aggregates.group("$account_type", sum("total_balance", "$balance"), avg("average_balance", "$balance"));
    System.out.println("Display aggregation results");
    accounts.aggregate(Arrays.asList(matchStage, groupStage)).forEach(document->System.out.print(document.toJson()));
}

```

##### Lab `$match` & `group`

**DemoApp.java**

```java
package com.mdbu.app;

import ch.qos.logback.classic.Level;
import ch.qos.logback.classic.Logger;
import com.mdbu.aggregations.Aggregation;
import com.mdbu.crud.Crud;
import com.mdbu.transactions.Transaction;
import com.mongodb.client.*;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.model.Aggregates;
import com.mongodb.client.model.Filters;
import com.mongodb.client.model.Updates;
import org.bson.Document;
import org.bson.conversions.Bson;
import org.bson.types.ObjectId;
import org.slf4j.LoggerFactory;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import static com.mongodb.client.model.Aggregates.match;
import static com.mongodb.client.model.Filters.*;
import static com.mongodb.client.model.Projections.*;
import static com.mongodb.client.model.Sorts.descending;
import static com.mongodb.client.model.Sorts.orderBy;
import static java.util.Arrays.asList;


public class DemoApp {
    public static void main(final String[] args) {
        Logger root = (Logger) LoggerFactory.getLogger("org.mongodb.driver");
        // Available levels are: OFF, ERROR, WARN, INFO, DEBUG, TRACE, ALL
        root.setLevel(Level.WARN);

        String connectionString = System.getenv("MONGODB_URI");
        try (MongoClient client = MongoClients.create(connectionString)) {
            MongoDatabase db = client.getDatabase("bank");
            MongoCollection<Document> accounts = db.getCollection("accounts");
            //Aggregation
            Aggregation agg = new Aggregation(client);

            agg.showAccountTypeSummary(accounts);
        }
    }
}
```

**Aggregation.java**

```java
package com.mdbu.aggregations;

import com.mongodb.client.AggregateIterable;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.model.Aggregates;

import org.bson.Document;
import org.bson.conversions.Bson;
import com.mdbu.aggregations.Aggregation;

import static com.mongodb.client.model.Sorts.orderBy;
import static java.util.Arrays.asList;
import static com.mongodb.client.model.Aggregates.*;
import static com.mongodb.client.model.Accumulators.*;
import static com.mongodb.client.model.Filters.*;
import static com.mongodb.client.model.Sorts.descending;

import java.util.List;
import java.util.Arrays;

public class Aggregation {
    private final MongoCollection<Document> collection;

    public Aggregation(MongoClient client) {
        this.collection = client.getDatabase("bank").getCollection("accounts");
    }

   public void showAccountTypeSummary(MongoCollection<Document> accounts) {
        Bson matchStage = Aggregates.match(lt("balance",1000));
        Bson groupStage = Aggregates.group("$account_type",sum("total_balance", "$balance"),avg("average_balance", "$balance"));
        System.out.println("Display aggregation results");
        accounts.aggregate(Arrays.asList(matchStage, groupStage)).forEach(document->System.out.print(document.toJson()));
    }

}
```

#### Using `$sort` and `$project`

Review the following code, which demonstrates how to build the `$sort` and `$project` stages of an aggregation pipeline in MongoDB with Java.

#### `$sort` and `$project`

In the following example, we use the `Aggregates` builder class to build the `$sort` and `$project` stages of an aggregation pipeline. This pipeline finds all checking accounts with a balance greater than 1500 and sorts the results in descending order. The query will return only four fields, including a newly computed Euro balance.

The `match()` method creates a `$match` pipeline stage that matches incoming documents against the specified query filter and filters out documents that don't match. Then, the `sort()` method creates a `$sort` pipeline stage to sort by the specified criteria. Finally, the `project()` method creates a `$project` pipeline stage that projects specified document fields. Field projection in aggregation follows the same rules as field projection in queries.

```java
public static void main(String[] args) {
    String connectionString = System.getProperty("mongodb.uri");
    try (MongoClient mongoClient = MongoClients.create(connectionString)) {
        MongoDatabase db = mongoClient.getDatabase("bank");
        MongoCollection<Document> accounts = db.getCollection("accounts");
        matchSortAndProjectStages(accounts);
    }
}

private static void matchSortAndProjectStages(MongoCollection<Document> accounts){
    Bson matchStage =
            Aggregates.match(Filters.and(Filters.gt("balance", 1500), Filters.eq("account_type", "checking")));
    Bson sortStage = Aggregates.sort(Sorts.orderBy(descending("balance")));
    Bson projectStage = Aggregates.project(Projections.fields(Projections.include("account_id", "account_type", "balance"), Projections.computed("euro_balance", new Document("$divide", asList("$balance", 1.20F))), Projections.excludeId()));
    System.out.println("Display aggregation results");
    accounts.aggregate(asList(matchStage,sortStage, projectStage)).forEach(document -> System.out.print(document.toJson()));
}

```

#### Lab Aggregation with the MongoDB Driver for Java (match,sort, project)

**DemoApp.java**

```java
package com.mdbu.app;

import ch.qos.logback.classic.Level;
import ch.qos.logback.classic.Logger;
import com.mdbu.aggregations.Aggregation;
import com.mdbu.crud.Crud;
import com.mdbu.transactions.Transaction;
import com.mongodb.client.*;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.model.Aggregates;
import com.mongodb.client.model.Filters;
import com.mongodb.client.model.Updates;
import org.bson.Document;
import org.bson.conversions.Bson;
import org.bson.types.ObjectId;
import org.slf4j.LoggerFactory;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import static com.mongodb.client.model.Aggregates.match;
import static com.mongodb.client.model.Filters.*;
import static com.mongodb.client.model.Projections.*;
import static com.mongodb.client.model.Sorts.descending;
import static com.mongodb.client.model.Sorts.orderBy;
import static java.util.Arrays.asList;


public class DemoApp {
    public static void main(final String[] args) {
        Logger root = (Logger) LoggerFactory.getLogger("org.mongodb.driver");
        // Available levels are: OFF, ERROR, WARN, INFO, DEBUG, TRACE, ALL
        root.setLevel(Level.WARN);

        String connectionString = System.getenv("MONGODB_URI");
        try (MongoClient client = MongoClients.create(connectionString)) {
            MongoDatabase db = client.getDatabase("bank");
            MongoCollection<Document> accounts = db.getCollection("accounts");
            //Aggregation
            Aggregation agg = new Aggregation(client);

            agg.showGBPBalancesForCheckingAccounts(accounts);
        }
    }
}

```

**Aggregation.java**

```java
package com.mdbu.aggregations;

import com.mongodb.client.AggregateIterable;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.model.Aggregates;

import org.bson.Document;
import org.bson.conversions.Bson;
import com.mdbu.aggregations.Aggregation;

import static com.mongodb.client.model.Sorts.orderBy;
import static java.util.Arrays.asList;
import static com.mongodb.client.model.Aggregates.*;
import static com.mongodb.client.model.Filters.*;
import static com.mongodb.client.model.Projections.*;
import static com.mongodb.client.model.Sorts.descending;

import java.util.List;

public class Aggregation {
    private final MongoCollection<Document> collection;

    public Aggregation(MongoClient client) {
        this.collection = client.getDatabase("bank").getCollection("accounts");
    }

    public void showGBPBalancesForCheckingAccounts(MongoCollection<Document> accounts) {
        Bson matchStage = Aggregates.match(and(eq("account_type", "checking"), gt("balance", 1500)));
        Bson sortStage = Aggregates.sort(orderBy(descending("balance")));
        Bson projectStage = Aggregates.project(fields(include("account_id", "account_type", "balance"), excludeId()));
        System.out.println("Display aggregation results");
        accounts.aggregate(asList(matchStage,sortStage, projectStage)).forEach(document -> System.out.print(document.toJson()));
    }

}
```

### MongoDB Indexes

#### Creating a Single Field Index

Review the code below, which demonstrates how to create a single field index in a collection.

#### Create a Single Field Index

Use `createIndex()` to create a new index in a collection. Within the parentheses of `createIndex()`, include an object that contains the field and sort order.

```javascript
db.customers.createIndex({
    birthdate: 1,
});
```

#### Create a Unique Single Field Index

Add `{unique:true}` as a second, optional, parameter in `createIndex()` to force uniqueness in the index field values. Once the unique index is created, any inserts or updates including duplicated values in the collection for the index field/s will fail.

```javascript
db.customers.createIndex(
    {
        email: 1,
    },
    {
        unique: true,
    }
);
```

MongoDB only creates the unique index if there is no duplication in the field values for the index field/s.

#### View the Indexes used in a Collection

Use `getIndexes()` to see all the indexes created in a collection.

```javascript
db.customers.getIndexes();
```

#### Check if an index is being used on a query

Use `explain()` in a collection when running a query to see the Execution plan. This plan provides the details of the execution stages (IXSCAN , COLLSCAN, FETCH, SORT, etc.).

-   The IXSCAN stage indicates the query is using an index and what index is being selected.
-   The COLLSCAN stage indicates a collection scan is perform, not using any indexes.
-   The FETCH stage indicates documents are being read from the collection.
-   The SORT stage indicates documents are being sorted in memory.

```javascript
db.customers.explain().find({
    birthdate: {
        $gt: ISODate("1995-08-01"),
    },
});
//-----------------------------
db.customers
    .explain()
    .find({
        birthdate: {
            $gt: ISODate("1995-08-01"),
        },
    })
    .sort({
        email: 1,
    });
```

### Understanding Multikey Indexes

Review the code below, which demonstrates how multikey indexes work. If a single field or compound index includes an array field, then the index is a multikey index.

#### Create a Single field Multikey Index

Use `createIndex()` to create a new index in a collection. Include an object as parameter that contains the array field and sort order. In this example `accounts` is an array field.

```javascript
db.customers.createIndex({
    accounts: 1,
});
```

#### View the Indexes used in a Collection

Use `getIndexes()` to see all the indexes created in a collection.

```javascript
db.customers.getIndexes();
```

#### Check if an index is being used on a query

Use `explain()` in a collection when running a query to see the Execution plan. This plan provides the details of the execution stages (IXSCAN , COLLSCAN, FETCH, SORT, etc.).

-   The `IXSCAN` stage indicates the query is using an index and what index is being selected.
-   The `COLLSCAN` stage indicates a collection scan is perform, not using any indexes.
-   The `FETCH` stage indicates documents are being read from the collection.
-   The `SORT` stage indicates documents are being sorted in memory.

```javascript
db.customers.explain().find({
    accounts: 627788,
});
```

---

### Working with Compound Indexes

Review the code below, which demonstrates how to create a compound index in a collection.

#### Create a Compound Index

Use `createIndex()` to create a new index in a collection. Within the parentheses of `createIndex()`, include an object that contains two or more fields and their sort order.

```javascript
db.customers.createIndex({
    active: 1,
    birthdate: -1,
    name: 1,
});
```

#### Order of Fields in a Compound Index

The order of the fields matters when creating the index and the sort order. It is recommended to list the fields in the following order: **Equality**, **Sort**, and **Range**.

-   Equality: field/s that matches on a single field value in a query
-   Sort: field/s that orders the results by in a query
-   Range: field/s that the query filter in a range of valid values

The following query includes an equality match on the active field, a sort on birthday (descending) and name (ascending), and a range query on birthday too.

```javascript
db.customers
    .find({
        birthdate: {
            $gte: ISODate("1977-01-01"),
        },
        active: true,
    })
    .sort({
        birthdate: -1,
        name: 1,
    });
```

Here's an example of an efficient index for this query:

```javascript
db.customers.createIndex({
    active: 1,
    birthdate: -1,
    name: 1,
});
```

#### View the Indexes used in a Collection

Use `getIndexes()` to see all the indexes created in a collection.

```javascript
db.customers.getIndexes();
```

#### Check if an index is being used on a query

Use `explain()` in a collection when running a query to see the Execution plan. This plan provides the details of the execution stages (IXSCAN , COLLSCAN, FETCH, SORT, etc.). Some of these are:

-   The `IXSCAN` stage indicates the query is using an index and what index is being selected.
-   The `COLLSCAN` stage indicates a collection scan is perform, not using any indexes.
-   The `FETCH` stage indicates documents are being read from the collection.
-   The `SORT` stage indicates documents are being sorted in memory.

```javascript
db.customers
    .explain()
    .find({
        birthdate: {
            $gte: ISODate("1977-01-01"),
        },
        active: true,
    })
    .sort({
        birthdate: -1,
        name: 1,
    });
```

#### Cover a query by the Index

An Index covers a query when MongoDB does not need to fetch the data from memory since all the required data is already returned by the index.

In most cases, we can use projections to return only the required fields and cover the query. Make sure those fields in the projection are in the index.

By adding the projection `{name:1,birthdate:1,_id:0}` in the previous query, we can limit the returned fields to only `name` and `birthdate`. These fields are part of the index and when we run the `explain()` command, the execution plan shows only two stages:

-   `IXSCAN` - Index scan using the compound index
-   `PROJECTION_COVERED` - All the information needed is returned by the index, no need to fetch from memory

```javascript
db.customers
    .explain()
    .find(
        {
            birthdate: {
                $gte: ISODate("1977-01-01"),
            },
            active: true,
        },
        { name: 1, birthdate: 1, _id: 0 }
    )
    .sort({
        birthdate: -1,
        name: 1,
    });
```

##### Lab

```javascript
db.accounts
    .explain()
    .find(
        { account_holder: "Andrea", balance: { $gt: 5 } },
        { account_holder: 1, balance: 1, account_type: 1, _id: 0 }
    )
    .sort({ balance: 1 });
```
#### Deleting an Index
Review the code below, which demonstrates how to delete indexes in a collection.


#### View the Indexes used in a Collection
Use `getIndexes()` to see all the indexes created in a collection. There is always a default index in every collection on `_id` field. This index is used by MongoDB internally and cannot be deleted.
```javascript
db.customers.getIndexes()
```
#### Delete an Index
Use `dropIndex()` to delete an existing index from a collection. Within the parentheses of `dropIndex()`, include an object representing the index key or provide the index name as a string.

#### Delete index by name:
```javascript
db.customers.dropIndex(
  'active_1_birthdate_-1_name_1'
)
```
#### Delete index by key:
```javascript
db.customers.dropIndex({
  active:1,
  birthdate:-1, 
  name:1
})
```
#### Delete Indexes
Use `dropIndexes()` to delete all the indexes from a collection, with the exception of the default index on _id.
```javascript
db.customers.dropIndexes()
```
The `dropIndexes()` command also can accept an array of index names as a parameter to delete a specific list of indexes.
```javascript
db.collection.dropIndexes([
  'index1name', 'index2name', 'index3name'
  ])
```