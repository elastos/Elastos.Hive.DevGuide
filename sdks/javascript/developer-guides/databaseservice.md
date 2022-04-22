# DatabaseService

Hive SDK uploads unstructured data to the corresponding Vault through DatabaseService class. Unstructured database type data is one of the data types supported by Hive SDK. DatabaseService class is one of the derived sub-services in Vault Service, which is used to support operations on unstructured data types, such as insert/update/query of the CRUD functions. Once the unstructured data is saved in Hive Node, it will be persisted in the corresponding MongoDB service.

## Insert a Document

An example of inserted data is as follows:

```javascript
const vaultServices = new VaultServices(context, getProviderAddress());
const databaseService = vaultServices.getDatabaseService();

const docNode = {"author": "john doe1", "title": "Eve for Dummies1"};
const result = await databaseService.insertOne(COLLECTION_NAME, docNode, new InsertOptions(false, false, true));
```

To insert data, you need to construct Json data first and then insert it into the specified data table (COLLECTION\_NAME). At the same time, you can specify the InsertOptions, which are derived from the corresponding functions of MongoDB database.

## Create Collection

In MongoDB, collection represents a data table where one with a specified name is created. After creation, user data can be inserted.

```javascript
databaseService.createCollection('your_collection');
```

## Delete Collection

Delete the MongoDB data table and all the data within will be deleted.

```javascript
databaseService.deleteCollection('your_collection');
```

## Insert

Insert data into the data table, which can be a single or multiple pieces of data. As shown in the example above, the insert option InsertOptions originates from MongoDB’s option to insert document. Please refer to the definition of InsertOptions for specific options. Here is an example of inserting multiple pieces of data:

```javascript
const nodes = [{"author": "john doe2", "title": "Eve for Dummies2"},
               {"author": "john doe3", "title": "Eve for Dummies3"}];
const result = await databaseService.insertMany('your_collection', nodes, new InsertOptions(false, true));
```

## Update

Just like inserting data, updating data also provides two versions of interfaces: updating the first or multiple pieces of data that meet the conditions, and updating data also supports providing update options. Update a single piece of data as follows:

```javascript
const filter = { "author": "john doe1" };
const docNode = { "author": "john doe1", "title": "Eve for Dummies1_1" };
const updateNode = { "$set": docNode };
const result = await databaseService.updateOne('your_collection', filter, updateNode, new UpdateOptions(false, true));
```

Example of updating multiple pieces of data:

```javascript
const filter = { "author": "john doe1" };
const updateNode = { "$set": { "author": "john doe1", "title": "Eve for Dummies1_2" } };
const result = databaseService.updateMany('your_collection', filter, updateNode, new UpdateOptions(false, true));
```

## Delete

The method of deleting data is the same - two versions are provided. Only by providing indication and deletion conditions, the data that meets the conditions will be deleted. The following is an example of deleting the first piece of data that meets the conditions:

```javascript
databaseService.deleteOne('your_collection', { "author": "john doe2" });
```

Example of deleting multiple pieces of data that meet the conditions are as follows:

```javascript
databaseService.deleteMany('your_collection', { "author": "john doe2" });
```

## Count

The following methods can be used to calculate the amount of data in the table. You need to specify the table name, query conditions, and parameters.

```javascript
const filter = { "author": "john doe1" };
const count = databaseService.countDocuments('your_collection', filter, new CountOptions());
```

## Find and Query

There are three ways to search data: search the first piece of data that meets the conditions, search multiple pieces of data that meet the conditions, and return version of multiple pieces of data with multiple query conditions. All three methods are required: table name, query conditions and query options.

```javascript
const query = {"author": "john doe1"};
const result = await databaseService.findOne('your_collection', query, new FindOptions());
```

An example of multiple search results is as follows:

```javascript
const query = {"author": "john doe1"};
const result = await databaseService.findMany('your_collection', query);
```

Below is an example of more query options that can be used when finding multiple results is as follows:

```javascript
const query = {"author": "john doe1"};
const result = await databaseService.query('your_collection', query, null);
```
