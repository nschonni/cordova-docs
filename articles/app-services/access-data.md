
<properties
   pageTitle="Access Data in your Apache Cordova app | Cordova"
   description="Access Data in your Apache Cordova app"
   services="na"
   documentationCenter=""
   authors="sureshja"
   tags=""/>
<tags
   ms.service="na"
   ms.devlang="javascript"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sureshja"/>

#Access Data in your Apache Cordova app

##Overview

This guide shows you how to perform common data access scenarios using Cordova plugin 
for Azure Mobile App, which not only includes API for data access but also the API for
authentication and sending push notifications. In this article we are going to cover
the scenarios that include querying for data, inserting, updating, and deleting 
data, and handling errors. 

##Prerequisites
To complete this tutorial, you need to create an Azure Mobile App back-end, setup
a database and create todoitem table by following the [quick start](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-cordova-get-started/
) guide. If you are using [Visual Studio Community 2015](http://www.visualstudio.com/)
 or newer, make sure you have installed [Visual Studio Tools for Apache Cordova](https://www.visualstudio.com/en-us/features/cordova-vs.aspx).

##How to: Create the Mobile App client

To start with you need to add [Mobile App plugin](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps) 
to your project. If you are using Cordova CLI, you can add the Azure Mobile App as follows

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```
 
In the editor, open or create a JavaScript file, and add the following code that defines the `MobileServiceClient` variable, and supply the application URL and application key from the mobile service in the `MobileServiceClient` constructor, in that order.

```javascript
	var client = new WindowsAzure.MobileServiceClient('AppUrl');
```
You must replace the placeholder `AppUrl` with the application URL of your 
mobile app, which you obtain from the [Azure portal](http://portal.azure.com/).

##How to: Query data from a mobile app

All of the code that accesses or modifies data in the SQL Database table calls 
functions on the `MobileServiceTable` object. You get a reference to the table by 
calling the `getTable()` function on an instance of the `MobileServiceClient`.

```javascript
    var todoItemTable = client.getTable('todoitem');
```

### How to: Filter returned data

The following code illustrates how to filter data by including a `where` clause in 
a query. It returns all items from `todoItemTable` whose complete field is equal 
to `false`. `todoItemTable` is the reference to the mobile app table that you would have
created by following the quick start guide. The where function applies a row 
filtering predicate to the query against the table. It accepts as its argument a 
JSON object or function that defines the row filter, and returns a query that can 
be further composed.

```javascript
	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});
```

By calling `where` on the Query object and passing an object as a parameter, we are
instructing Mobile App Client to return only the rows whose `complete` column 
contains the `false` value.

The object which is passed to the `where` method can have an arbitrary number of parameters, and they'll all be interpreted as AND clauses to the query. For example, the line below:

```javascript
	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});
```

The body of the function is translated into an Open Data Protocol (OData) boolean 
expression which is passed to a query string parameter. It is possible to pass in 
a function that takes no parameters, like so:

```javascript
    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });
```

It is possible to combine `where` with `orderBy`, `take`, and `skip`. See the next 
section for details.

### <a name="sorting"></a>How to: Sort returned data

The following code illustrates how to sort data by including an `orderBy` or `orderByDescending` function in the query. It returns items from `todoItemTable` sorted ascending by the `text` field. By default, the server returns only the first 50 elements.

```javascript
	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});
```

### <a name="paging"></a>How to: Return data in pages

By default, Mobile Services only returns 50 rows in a given request, unless the client explicitly asks for more data in the response. The following code shows how to implement paging in returned data by using the `take` and `skip` clauses in the query.  The following query, when executed, returns the top three items in the table.

```javascript
	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});
```

Notice that the `take(3)` method was translated into the query option `$top=3` in the query URI.

The following revised query skips the first three results and returns the next three after that. This is effectively the second "page" of data, where the page size is three items.

```javascript
	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});
```

Again, you can view the URI of the request sent to the mobile service. Notice that the `skip(3)` method was translated into the query option `$skip=3` in the query URI.

This is a simplified scenario of passing hard-coded paging values to the `take` and `skip` functions. In a real-world app, you can use queries similar to the above with a pager control or comparable UI to let users navigate to previous and next pages.

### <a name="selecting"></a>How to: Select specific columns

You can specify which set of properties to include in the results by adding a `select` clause to your query. For example, the following code returns the `id`, `complete`, and `text` properties from each row in the `todoItemTable`:

```javascript
	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})
```

Here the parameters to the select function are the names of the table's columns that you want to return.

All the functions described so far are additive, so we can just keep calling them and we'll each time affect more of the query. One more example:

```javascript
    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
```

### <a name="lookingup"></a>How to: Look up data by ID

The `lookup` function takes only the `id` value, and returns the object from the database with that ID. Database tables are created with either an integer or string `id` column. A string `id` column is the default.

```javascript
	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})
```

##How to: Insert data

The following code illustrates how to insert new rows into a table. The client requests that a row of data be inserted by sending a POST request to the mobile service. The request body contains the data to be inserted, as a JSON object.

```javascript
	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})
```

This inserts data from the supplied JSON object into the table. You can also specify a callback function to be invoked when the insertion is complete:

```javascript
	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});
```

##How to: Modify data

The following code illustrates how to update data in a table. The client requests that a row of data be updated by sending a PATCH request to the mobile service. The request body contains the specific fields to be updated, as a JSON object. It updates an existing item in the table `todoItemTable`.

```javascript
	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	})
```

The first parameter specifies the instance to update in the table, as specified by its ID.

You can also specify a callback function to be invoked when the update is complete:

```javascript
	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});
```

##</a>How to: Delete data

The following code illustrates how to delete data from a table. The client requests that a row of data be deleted by sending a DELETE request to the mobile service. It deletes an existing item in the table todoItemTable.

```javascript
	todoItemTable.del({
	   id: idToDelete
	})
```

The first parameter specifies the instance to delete in the table, as specified by its ID.

You can also specify a callback function to be invoked when the delete is complete:

```javascript
	todoItemTable.del({
	   id: idToDelete
	}).done(function () {
	   /* Do something */
	}, function (err) {
	   alert("Error: " + err);
	});
```
##How to: Call a custom API

A custom API enables you to define custom endpoints that expose server functionality that does not map to an insert, update, delete, or read operation. By using a custom API, you can have more control over messaging, including reading and setting HTTP message headers and defining a message body format other than JSON. For an example of how to create a custom API in your mobile service, see [How to: define a custom API endpoint](mobile-services-dotnet-backend-define-custom-api.md).

You call a custom API from the client by calling the [invokeApi](https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L337) method on **MobileServiceClient**. For example, the following line of code sends a POST request to the **completeAll** API on the mobile service:

```javascript
    client.invokeApi("completeall", {
        body: null,
        method: "post"
    }).done(function (results) {
        var message = results.result.count + " item(s) marked as complete.";
        alert(message);
        refreshTodoItems();
    }, function(error) {
        alert(error.message);
    });
```

For more realistic examples and a more a complete discussion of **invokeApi**, 
see [Custom API in Azure Mobile Services Client SDKs](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).