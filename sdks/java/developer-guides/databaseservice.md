# DatabaseService

Hive SDK uploads unstructured data to the corresponding Vault through DatabaseService class. Unstructured database type data is one of the data types supported by Hive SDK. DatabaseService class is one of the derived sub-services in Vault Service, which is used to support operations on unstructured data types, such as insert/update/query of the CRUD functions. Once the unstructured data is saved in Hive Node, it will be persisted in the corresponding MongoDB service.

## Insert a Document

An example of inserted data is as follows:

```java
Vault vault = new Vault(context, getVaultProviderAddress());
DatabaseServic dbService = vault.getDatabaseServic();

ObjectNode doc = JsonNodeFactory.instance.objectNode();
doc.put("author", "john doe1");
doc.put("title", "Eve for Dummies1");
dbService.insertOne(YOUR_COLLECT_NAME, doc,
        new InsertOptions().bypassDocumentValidation(false))
.thenAcceptAsync(insertResult -> {
    System.out.println("Inserted a document to database in success.");
}).exceptionally(ex -> {
    System.out.println("Failed to insert a document.");
    ex.printStackTrace();
    return null;
});
```

To insert data, you need to construct Json data first and then insert it into the specified data table (COLLECTION\_NAME). At the same time, you can specify the InsertOptions, which are derived from the corresponding functions of MongoDB database.

## Create Collection

In MongoDB, collection represents a data table where one with a specified name is created. After creation, user data can be inserted.

```java
databaseService.createCollection("your_collection")
.thenAcceptAsync(result -> {
    System.out.println("Created a collection successfully");
}).exceptionally(ex -> {
    System.out.println("Failed to create the collection");
    ex.printStackTrace();
    return null;
});
```

## Delete Collection

Delete the MongoDB data table and all the data within will be deleted.

```java
databaseService.deleteCollection("your_collection")
.thenAcceptAsync(result -> {
    System.out.println("Deleted the collection successfully");
}).exceptionally(ex -> {
    System.out.println("Failed to delete the collection");
    ex.printStackTrace();
    return null;
});
```

## Insert

Insert data into the data table, which can be a single or multiple pieces of data. As shown in the example above, the insert option InsertOptions originates from MongoDB’s option to insert document. Please refer to the definition of InsertOptions for specific options. Here is an example of inserting multiple pieces of data:

```java
List<JsonNode> nodes = new ArrayList<>();

ObjectNode doc = JsonNodeFactory.instance.objectNode();
doc.put("author", "john doe2");
doc.put("title", "Eve for Dummies2");
nodes.add(doc);

doc.removeAll();

doc = JsonNodeFactory.instance.objectNode();
doc.put("author", "john doe3");
doc.put("title", "Eve for Dummies3");
nodes.add(doc);
dbService.insertMany("your_collection", nodes,
		new InsertOptions().bypassDocumentValidation(false).ordered(true))
.thenAcceptAsync(insertResult -> {
    System.out.println("Inserted two documents successfully.");
}).exceptionally(ex -> {
    System.out.println("Failed to insert documents.");
    ex.printStackTrace();
    return null;
});
```

## Update

Just like inserting data, updating data also provides two versions of interfaces: updating the first or multiple pieces of data that meet the conditions, and updating data also supports providing update options. Update a single piece of data as follows:

```java
ObjectNode filter = JsonNodeFactory.instance.objectNode();
filter.put("author", "john doe1");
ObjectNode doc = JsonNodeFactory.instance.objectNode();
doc.put("author", "john doe1");
doc.put("title", "Eve for Dummies1_1");
ObjectNode update = JsonNodeFactory.instance.objectNode();
update.put("$set", doc);
dbService.updateOne("your_collection", filter, update,
        new UpdateOptions().setBypassDocumentValidation(false).setUpsert(true))
.thenAcceptAsync(updateResult -> {
    System.out.println("updated the document successfully.");
}).exceptionally(ex -> {
    System.out.println("Failed to update the document.");
    ex.printStackTrace();
    return null;
});
```

Example of updating multiple pieces of data:

```java
ObjectNode filter = JsonNodeFactory.instance.objectNode();
filter.put("author", "john doe1");
ObjectNode doc = JsonNodeFactory.instance.objectNode();
doc.put("author", "john doe1");
doc.put("title", "Eve for Dummies1_1");
ObjectNode update = JsonNodeFactory.instance.objectNode();
update.put("$set", doc);
databaseService.updateMany("your_collection", filter, update,
		new UpdateOptions().setBypassDocumentValidation(false).setUpsert(true))
.thenAcceptAsync(updateResult -> {
    System.out.println("Updated the documents successfully.");
}).exceptionally(ex -> {
    System.out.println("Failed to update the documents.");
    ex.printStackTrace();
    return null;
});
```

## Delete

The method of deleting data is the same - two versions are provided. Only by providing indication and deletion conditions, the data that meets the conditions will be deleted. The following is an example of deleting the first piece of data that meets the conditions:

```java
ObjectNode filter = JsonNodeFactory.instance.objectNode();
filter.put("author", "john doe2");
databaseService.deleteOne("your_collection", filter)
.thenAcceptAsync(deleteResult -> {
    System.out.println("delete the document successfully.");
}).exceptionally(ex -> {
    System.out.println("failed to delete the document.");
    ex.printStackTrace();
    return null;
});
```

Example of deleting multiple pieces of data that meet the conditions are as follows:

```java
ObjectNode filter = JsonNodeFactory.instance.objectNode();
filter.put("author", "john doe2");
databaseService.deleteMany("your_collection", filter)
.thenAcceptAsync(deleteResult -> {
    System.out.println("delete the documents successfully.");
}).exceptionally(ex -> {
    System.out.println("failed to delete the documents.");
    ex.printStackTrace();
    return null;
});
```

## Count

The following methods can be used to calculate the amount of data in the table. You need to specify the table name, query conditions, and parameters.

```java
ObjectNode filter = JsonNodeFactory.instance.objectNode();
filter.put("author", "john doe1");
databaseService.countDocuments("your_collection", filter,
        new CountOptions().setLimit(1L).setSkip(0L).setMaxTimeMS(1000000000L))
.thenAcceptAsync(count -> {
    System.out.println("Count documents successfully.");
    System.out.println("count => " + count);
}).exceptionally(ex -> {
    System.out.println("Failed to count the documents.");
    ex.printStackTrace();
    return null;
});
```

## Find and Query

There are three ways to search data: search the first piece of data that meets the conditions, search multiple pieces of data that meet the conditions, and return version of multiple pieces of data with multiple query conditions. All three methods are required: table name, query conditions and query options.

```java
ObjectNode query = JsonNodeFactory.instance.objectNode();
query.put("author", "john doe1");
databaseService.findOne("test_collection", query, new FindOptions().setSkip(0).setLimit(0))
.thenAcceptAsync(document -> {
    System.out.println("find the document successfully.");
    System.out.println("author => " + document.get("author").textValue());
    System.out.println("title => " + document.get("title").textValue());
}).exceptionally(ex -> {
    System.out.println("failed to find the document.");
    ex.printStackTrace();
    return null;
});
```

An example of multiple search results is as follows:

```java
ObjectNode query = JsonNodeFactory.instance.objectNode();
query.put("author", "john doe1");
databaseService.findMany("test_collection", query, new FindOptions().setSkip(0).setLimit(0))
.thenAcceptAsync(list -> {
    System.out.println("find the documents successfully.");
    System.out.println("author => " + document.get("author").textValue());
    System.out.println("title => " + document.get("title").textValue());
}).exceptionally(ex -> {
    System.out.println("failed to find the documents.");
    ex.printStackTrace();
    return null;
});
```

Below is an example of more query options that can be used when finding multiple results is as follows:

```java
ObjectNode query = JsonNodeFactory.instance.objectNode();
query.put("author", "john doe1");
Assertions.assertNotNull(databaseService.query(COLLECTION_NAME, query, null)
.thenAcceptAsync(list -> {
    System.out.println("find the documents successfully.");
    System.out.println("author => " + document.get("author").textValue());
    System.out.println("title => " + document.get("title").textValue());
}).exceptionally(ex -> {
    System.out.println("failed to find the documents.");
    ex.printStackTrace();
    return null;
});
```
