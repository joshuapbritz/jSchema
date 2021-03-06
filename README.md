# jSchema

![logo](https://user-images.githubusercontent.com/5210420/32085476-48b25564-ba9d-11e7-8a6e-9e2df9cd5ed5.png)

Data Modeling in JavaScript
jSchema is a framework for modeling data in JavaScript.  By using fundamental data modeling principles you are able to pull multiple datasets into in a common schema, define relationships, aggregate, join, and subset datasets to make data easier to work with in the browser.

![entity relationship diagram - new page 1](https://user-images.githubusercontent.com/5210420/32084304-50e6bdbc-ba96-11e7-92b8-cfab13866fe0.png)

jSchema is going to create an object assigned globally to the window called `window.jSchema`.  This object is a metadata representation of all your datasets containing the table names, column names, and keys that define sets.  The data itself is stored within a closure within the object and is retrieved via a getter function.  Joining data, aggregating data, and filtering data will create a new dataset in your WORK namespace that will persist on the page until either you delete the table or you run a cleanUp method.

## Demo

A complete working demo of how to load tables, join tables, and aggregate tables can be found in the repository in the demo folder.  


## How to Use

### NPM
If you use npm, `npm install jschema.js`.

### requirejs
jschema.js uses requirejs to modularly load the library and is included in the package.json.  require.js can be moved to the lib directory of your project and included as

```html
<script data-main="main" src="lib/require.js"></script></body>
```
where main is main.js.  You can then add jSchema to your application as such (in this case main.js):

```Javascript
requirejs(['lib/jschema'], function(jSchema){
  var s = new jSchema;

  s.add([{a:1, b:2}])
  s.add([{b:2, c:3}], {name:"named_table", primaryKey:"b"})
});
```

Or load data from external sources

```Javascript
fetch("education.json")
  .then(response => response.json())
  .then(json => s.add(json, {name:"education", primaryKey:"Age_Group"}))
  .then(fetch("gender.json")
    .then(response => response.json())
    .then(json => s.add(json, {name:"gender", primaryKey:"Age_Group"}))
);
```

Multiple datasets that have primary / foreign key relationships can be joined as such:

```Javascript
s.join("EDUCATION", "GENDER", {name: "joinTable"})
```

You can drop tables that are no longer needed in the schema with the drop method

```Javascript
s.drop("GENDER")
```

If you want to sort a dataset by an attribute use the orderBy method:

```Javascript
s.orderBy("GENDER", {
  clause: "Count",
  order: "asc",
  name: "sortBy"
})
```

To Group by you need to provide the dataset name, the name of the dimension to group by, and the metrics you wish to aggregate:

```Javascript
s.groupBy("GENDER", {
  dim: "Gender",
  metric: "Count",
  name: "groupBy",
  method: "sum" // supported methods are sum, count
})
```
Output
```JSON [
  {
    "dim": "Male",
    "val": 37575
  },
  {
    "dim": "Female",
    "val": 44074
  }
]
```

To filter a dataset you can call the filter method and pass three of more arguments.  First argument is always the table name.  The second and third are the field to filter by and the value to filter it on.  Additional pairs can be included as 4th, 5th parameters and so on.  A filtered dataset will be created in the WORK space.

```Javascript
s.filter('GENDER', 'Gender', 'Female')
```

If a new version of the dataset is made available you can update the existing table in the schema by running the update method

```Javascript
s.update("GENDER", data);
```

By default all temporary datasets (joins, group by, order by) added to the schema will be prefixed with the namespace WORK. (e.g. joining two tables NAMES and LOCATIONS will result in a table added to the schema called WORK.NAMES_LOCATIONS).  Calling the cleanUp method will remove all datasets added to the work namespace

```Javascript
s.cleanUp();
```

# Testing
To run mocha test scripts from the terminal type npm test.

# Contact
Questions, comments, feature requests, etc are always welcome.  I am [@ignoreintuition](https://twitter.com/IgnoreIntuition) on Twitter.
