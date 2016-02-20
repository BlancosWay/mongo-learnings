# Retrival:

### Index
1. Find and FindOne
2. $ Conditions
    + Comparision Operators.
    + $ne.
    + OR Queries.
    + NOT Operator.
    + $and and $nor Operators.
3. Type Specific Queries.
4. Regular expressions.
5. Querying Arrays.
    + scalar queries on arrays.
    + $all(does not follow order).
    + Query for exact order.
    + $size.
    + $slice.

## 1. Find and FindOne

Find is used along with query document or an empty document.

Find when used with no document or empty document will give all the results.

    db.sample.find() == db.sample.find({});

FindOne will give only one document.

##### Example:

    db.sample.insert({name:"A", ids:[1,2,3]});

    db.sample.find({});
    { "_id" : ObjectId("56c7fee1db13da086828b410"), "name" : "A", "ids" : [ 1, 2, 3 ] }

    db.sample.insert([{name:"B",ids:[1,2,3,4,5],fullName:"A B"},{name:"C",fullName:"A B",ids:[3,4,5]}]);

    db.sample.findOne();
    {
    "_id" : ObjectId("56c7ff7fdb13da086828b411"),
    "name" : "A",
    "ids" : [
        1,
        2,
        3
       ]
    }

+ Adding more than one keys results in AND behavior

    ##### Example
        db.sample.drop();
        db.sample.insert([{
            name: "h",
            ids: [1,2,3],
            department: "A"
        },{
            name: "n",
            ids: [3,4,5],
            department: "A"
        },{
            name: "m",
            ids: [4,5,6],
            department: "A"
        },{
            name:"h",
            ids: [1,3,4],
            department: "B"
        }]);

        db.sample.find({ids:3,department:"A"});

        { "_id" : ObjectId("56c804b9db13da086828b414"), "name" : "h", "ids" : [ 1, 2, 3 ], "department" : "A" }
        { "_id" : ObjectId("56c804b9db13da086828b415"), "name" : "n", "ids" : [ 3, 4, 5 ], "department" : "A" }

+ Specifying Which keys to Return

    Specify the required keys as a document in the find query.
    + set 1 to return the key.
    + set 0 explicitly to not to return a key.
    + _id key will be returned by default. Set 0 on _id to hard stop it.

    ##### Example:
        db.sample.find({ids:3},{department:1});
        { "_id" : ObjectId("56c804b9db13da086828b414"), "department" : "A" }
        { "_id" : ObjectId("56c804b9db13da086828b415"), "department" : "A" }
        { "_id" : ObjectId("56c804b9db13da086828b417"), "department" : "B" }

        db.sample.find({ids:3},{department:1,_id:0});
        { "department" : "A" }
        { "department" : "A" }
        { "department" : "B" }

### Limitations:

+ the value of the query document keys should be constants.It can be normal variable in the code.
+  It cannot refer another key of the same or other document.

        db.sample.find({"name":"this.fullname"}); //Does not work.

        Hint: Where clauses should be used for this.

## 2. $ Conditionals:

### Comparision Operators:
----------

+ $lt, $le, $ge, $gt are used for comparision operations <, <=, >=, > to respectively.
+ These operations can be combined to get the range or desired results.

##### Example
    db.sample.find({ids:{$lt:4}}); //Picks up all the documents which has one of the ids less than 4
    { "_id" : ObjectId("56c804b9db13da086828b414"), "name" : "h", "ids" : [ 1, 2, 3 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b415"), "name" : "n", "ids" : [ 3, 4, 5 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b417"), "name" : "h", "ids" : [ 1, 3, 4 ], "department" : "B" }.

    db.sample.find({ids:{$lt:4,$gt:3}}); //picks up all the documents which has one of the ids less than 4 and greater than 3.
    //Todo.


+ queries on date generally use comparision operators as the every document use millisecond precision while insertion.

### $ne
 ----------------
 + $ne is used for "not equal".

 ##### example
    db.sample.find({ids:{$ne:3}}); //all the documents with no 3 as one of the values of ids.
    { "_id" : ObjectId("56c804b9db13da086828b416"), "name" : "m", "ids" : [ 4, 5, 6 ], "department" : "A" }
    { "_id" : ObjectId("56c808d2db13da086828b418"), "name" : "t", "fullname" : "t", "ids" : [ 5, 6, 7 ] }

### OR Queries
--------------
There are 2 ways to do OR queries in mongo.
+ $or  [gives a 'or' condition on multiple keys]
+ $in($nin)  [gives a 'or' condition on single key.]

$or can be used along with $in($nin).
$or and $in($nin) both takes an array.

##### Examples:
    db.sample.find({department:{$in:["A","B"]}});
    { "_id" : ObjectId("56c804b9db13da086828b414"), "name" : "h", "ids" : [ 1, 2, 3 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b415"), "name" : "n", "ids" : [ 3, 4, 5 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b416"), "name" : "m", "ids" : [ 4, 5, 6 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b417"), "name" : "h", "ids" : [ 1, 3, 4 ], "department" : "B" }

    db.sample.find({department:{$nin:["A"]}}); //It retrives documents with out department key as well.
    { "_id" : ObjectId("56c804b9db13da086828b417"), "name" : "h", "ids" : [ 1, 3, 4 ], "department" : "B" }
    { "_id" : ObjectId("56c808d2db13da086828b418"), "name" : "t", "fullname" : "t", "ids" : [ 5, 6, 7 ] }

    Using Exists along with or operators to filter no key values.

    db.sample.find({department:{$nin:["A"],$exists:1}}); //returns only the documents with has department key and department value not equal to "A".
    { "_id" : ObjectId("56c804b9db13da086828b417"), "name" : "h", "ids" : [ 1, 3, 4 ], "department" : "B" }

    db.sample.find({$or:[{department:{$in:["A","B"]}},{ids:7}]});
    { "_id" : ObjectId("56c804b9db13da086828b414"), "name" : "h", "ids" : [ 1, 2, 3 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b415"), "name" : "n", "ids" : [ 3, 4, 5 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b416"), "name" : "m", "ids" : [ 4, 5, 6 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b417"), "name" : "h", "ids" : [ 1, 3, 4 ], "department" : "B" }
    { "_id" : ObjectId("56c808d2db13da086828b418"), "name" : "t", "fullname" : "t", "ids" : [ 5, 6, 7 ] }

### Not Operator:
-----------
+ Cannot be used as top level operator.
+ Can take only regex or a document.
+ Useful mostly in regex.

$mod takes 2 values in the array.
   + first value is the divider.
   + second value is the remainder.

##### Example:
    db.sample.insert([{id:1},{id:2}]);
    db.sample.find({id:{$not:{$mod:[5,1]},$exists:1}});
    { "_id" : ObjectId("56c818e2db13da086828b41b"), "id" : 2 }

### AND and NOR Operators:
-----
+ AND, OR and NOR are used as the top level operators.
+ All the above three operators are used in the similar way.

## Type Specific Queries:

### null
----------

+ null picks up null values for the keys as well the documents with no specified keys.

+ Use $exists along with $in to get the documents which has null valued keys.

##### Example:
     db.sample.insert([{id:null},{}]);
     db.sample.find();
    { "_id" : ObjectId("56c804b9db13da086828b414"), "name" : "h", "ids" : [ 1, 2, 3 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b415"), "name" : "n", "ids" : [ 3, 4, 5 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b416"), "name" : "m", "ids" : [ 4, 5, 6 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b417"), "name" : "h", "ids" : [ 1, 3, 4 ], "department" : "B" }
    { "_id" : ObjectId("56c808d2db13da086828b418"), "name" : "t", "fullname" : "t", "ids" : [ 5, 6, 7 ] }
    { "_id" : ObjectId("56c818e2db13da086828b41b"), "id" : 2 }
    { "_id" : ObjectId("56c8190cdb13da086828b41c"), "id" : 1 }
    { "_id" : ObjectId("56c897e1db13da086828b41d"), "id" : null }
    { "_id" : ObjectId("56c897e1db13da086828b41e") }

    db.sample.find({id:null});
    { "_id" : ObjectId("56c804b9db13da086828b414"), "name" : "h", "ids" : [ 1, 2, 3 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b415"), "name" : "n", "ids" : [ 3, 4, 5 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b416"), "name" : "m", "ids" : [ 4, 5, 6 ], "department" : "A" }
    { "_id" : ObjectId("56c804b9db13da086828b417"), "name" : "h", "ids" : [ 1, 3, 4 ], "department" : "B" }
    { "_id" : ObjectId("56c808d2db13da086828b418"), "name" : "t", "fullname" : "t", "ids" : [ 5, 6, 7 ] }
    { "_id" : ObjectId("56c897e1db13da086828b41d"), "id" : null }
    { "_id" : ObjectId("56c897e1db13da086828b41e") }

    db.sample.find({id:{$in:[null],$exists:true}});
    { "_id" : ObjectId("56c897e1db13da086828b41d"), "id" : null }

## Regular Expressions:

+ used for regex matches.
+ used for case insensitive matches.

##### Examples:
    db.sample.drop();
    db.sample.insert([{name:"ADBc"},{name:"Adbc"},{name:"AdB"}]);

    db.sample.find({name:/adbc/i});
    { "_id" : ObjectId("56c89aacdb13da086828b423"), "name" : "ADBc" }
    { "_id" : ObjectId("56c89aacdb13da086828b424"), "name" : "Adbc" }

    db.sample.find({name:/adbc?/i}); //Will match all the documents.
    { "_id" : ObjectId("56c89a5bdb13da086828b421"), "name" : "AdB" }
    { "_id" : ObjectId("56c89aacdb13da086828b423"), "name" : "ADBc" }
    { "_id" : ObjectId("56c89aacdb13da086828b424"), "name" : "Adbc" }

    db.sample.find({name:/ADBc/}); //should match only one document
    { "_id" : ObjectId("56c89aacdb13da086828b423"), "name" : "ADBc" }

+ used to match the regex stored directly in the DB(Though very often).

##### Example
    db.sample.insert({"name" : /baz/})
    db.foo.find({"name" : /baz/})
    {
        "_id" : ObjectId("4b23c3ca7525f35f94b60a2d"),
        "name" : /baz/
    }

## Querying Arrays:

### Scalar Queries on arrays:
--------

+ querying arrays can work similar to scalars in few cases.

##### Example
    db.sample.drop();
    db.sample.insert([{
        name:"a",
        ids:[1,2,3]
    },{
        name:"b",
        ids:[3,4,5]
    },{
        name: "c",
        ids: [2,4,6]
    }]);

    db.sample.find({ids:2});
    { "_id" : ObjectId("56c8a043db13da086828b425"), "name" : "a", "ids" : [ 1, 2, 3 ] }
    { "_id" : ObjectId("56c8a043db13da086828b427"), "name" : "c", "ids" : [ 2, 4, 6 ] }

+ However the above queries work in a different way if the query invloves more than 1 key. It will return documents where even one condition is satified.
#### GENERAL THUMB RULE FOR ARRAYS:
     If a field contains an array and query has multiple conditional operators, the field as a whole will match if either a single array element meets the conditions or a combination of array elements meet the conditions.
+ Some of the unexpected behaviours while using normal queries on array are mentioned below.

##### Examples:
    db.sample.find();
    { "_id" : ObjectId("56c8a043db13da086828b425"), "name" : "a", "ids" : [ 1, 2, 3 ] }
    { "_id" : ObjectId("56c8a043db13da086828b426"), "name" : "b", "ids" : [ 3, 4, 5 ] }
    { "_id" : ObjectId("56c8a043db13da086828b427"), "name" : "c", "ids" : [ 2, 4, 6 ] }

    db.sample.find({ids:{$lt:3,$gt:2}});
    GENERAL EXPECTATION: No documents should be returned.
    ACTUAL RESULT:
    { "_id" : ObjectId("56c8a043db13da086828b425"), "name" : "a", "ids" : [ 1, 2, 3 ] }
    { "_id" : ObjectId("56c8a043db13da086828b427"), "name" : "c", "ids" : [ 2, 4, 6 ] }
    SOLUTION: use $elemMatch for comparison operators for strict comparison on array of each document.
    db.

    db.sample.find({ids:{$elemMatch:{$lt:3,$gt:2}}});
    //returns no documents.

    db.sample.find({ids:1,ids:2});
    //general AND operator on ids.
    EXPECTED: returns only one documents where ids contains both 1 and 2 as the query is just normal AND operations of ids.
    ACTUAL: returns
    { "_id" : ObjectId("56c8a043db13da086828b425"), "name" : "a", "ids" : [ 1, 2, 3 ] }
    { "_id" : ObjectId("56c8a043db13da086828b427"), "name" : "c", "ids" : [ 2, 4, 6 ] }
    SOLUTION: use $all for these operations.(explained in detail in next chapters.)


### $all
-------
+ To match two or more keys in the same array of the documents use $all.
+ Order does not matter.

##### Example:
    db.sample.find();
    { "_id" : ObjectId("56c8a043db13da086828b425"), "name" : "a", "ids" : [ 1, 2, 3 ] }
    { "_id" : ObjectId("56c8a043db13da086828b426"), "name" : "b", "ids" : [ 3, 4, 5 ] }
    { "_id" : ObjectId("56c8a043db13da086828b427"), "name" : "c", "ids" : [ 2, 4, 6 ] }
    db.sample.find({ids:{$all:[2,3]}});
    { "_id" : ObjectId("56c8a043db13da086828b425"), "name" : "a", "ids" : [ 1, 2, 3 ] }

### Query for exact order:
--------

+ use the entire array for the exact match of the array in the documents.This follows order.
+ Use DOT Operator for querying on exact index of the array key.

##### Examples:
    db.sample.drop();
    db.sample.insert([{ids:["A","B","C"]},{ids:["A","C","B"]},{ids:["C","B","A"]}]);
    db.sample.find({ids:["A","B","C"]});
    { "_id" : ObjectId("56c8acb7db13da086828b428"), "ids" : [ "A", "B", "C" ] }

    db.sample.find({'ids.2':"B"});
    { "_id" : ObjectId("56c8acb7db13da086828b429"), "ids" : [ "A", "C", "B" ] }

### $size
---------

+ $size returns all the documents where the specified array size is matched.
+ $size operator cannot be used with any other conditional operator.

##### Example:
    db.sample.find({ids:{$size:3}});
    { "_id" : ObjectId("56c8acb7db13da086828b428"), "ids" : [ "A", "B", "C" ] }
    { "_id" : ObjectId("56c8acb7db13da086828b429"), "ids" : [ "A", "C", "B" ] }
    { "_id" : ObjectId("56c8acb7db13da086828b42a"), "ids" : [ "C", "B", "A" ] }
    db.sample.insert([{id:[1],ids:[422,2,1]}]);
    db.sample.find({ids:{$size:3},id:{$size:1}});
    { "_id" : ObjectId("56c8b9c4db13da086828b42e"), "id" : [ 1 ], "ids" : [ 422, 2, 1 ] }

### $slice
---------

+ $slice is used in the second document in the find query to get only the subset of elements from the actual list of elements in the array.
+ +n value returns the first n values of the array.
+ -n value returns the last n values of the array.
+ if n > existing number of values in the array. Only the existing number of values will be returned.


##### Examples:
    db.sample.find();
    { "_id" : ObjectId("56c8acb7db13da086828b428"), "ids" : [ "A", "B", "C" ] }
    { "_id" : ObjectId("56c8acb7db13da086828b429"), "ids" : [ "A", "C", "B" ] }
    { "_id" : ObjectId("56c8acb7db13da086828b42a"), "ids" : [ "C", "B", "A" ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42b"), "id" : [ 1, 2, 3 ], "ids" : [ 1, 2, 3, 4 ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42c"), "id" : [ 1, 2 ], "ids" : [ 1, 2, 3, 4, 5 ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42d"), "id" : [ 1 ], "ids" : [ 422, 2 ] }
    { "_id" : ObjectId("56c8b9c4db13da086828b42e"), "id" : [ 1 ], "ids" : [ 422, 2, 1 ] }

    db.sample.find({},{ids:{$slice:1}});
    { "_id" : ObjectId("56c8acb7db13da086828b428"), "ids" : [ "A" ] }
    { "_id" : ObjectId("56c8acb7db13da086828b429"), "ids" : [ "A" ] }
    { "_id" : ObjectId("56c8acb7db13da086828b42a"), "ids" : [ "C" ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42b"), "id" : [ 1,     2, 3 ], "ids" : [ 1 ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42c"), "id" : [ 1, 2 ], "ids" : [ 1 ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42d"), "id" : [ 1 ], "ids" : [ 422 ] }
    { "_id" : ObjectId("56c8b9c4db13da086828b42e"), "id" : [ 1 ], "ids" : [ 422 ] }

    db.sample.find({},{ids:{$slice:-4}});
    { "_id" : ObjectId("56c8acb7db13da086828b428"), "ids" : [ "A", "B", "C" ] }
    { "_id" : ObjectId("56c8acb7db13da086828b429"), "ids" : [ "A", "C", "B" ] }
    { "_id" : ObjectId("56c8acb7db13da086828b42a"), "ids" : [ "C", "B", "A" ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42b"), "id" : [ 1, 2, 3 ], "ids" : [ 1, 2, 3, 4 ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42c"), "id" : [ 1, 2 ], "ids" : [ 2, 3, 4, 5 ] }
    { "_id" : ObjectId("56c8b98ddb13da086828b42d"), "id" : [ 1 ], "ids" : [ 422, 2 ] }
    { "_id" : ObjectId("56c8b9c4db13da086828b42e"), "id" : [ 1 ], "ids" : [ 422, 2, 1 ]

+ $slice can also get subset of the values from the middle.
+ $slice optionally take array where the first element is the starting index and second element is the count of the elements to return from the starting index.
+ first element  == index of the array.
+ second element == limit of the array.
+ limit cannot be -ve.
+ -n represent the n index from the end.
+ +n represent the n index from the start.

##### Example:
    db.sample.find({"id" : [ 1, 2, 3 ]});
    { "_id" : ObjectId("56c8b98ddb13da086828b42b"), "id" : [ 1, 2, 3 ], "ids" : [ 1, 2, 3, 4 ] }
    db.sample.find({"id" : [ 1, 2, 3 ]},{ids:{$slice:[2,2]}});
    { "_id" : ObjectId("56c8b98ddb13da086828b42b"), "id" : [ 1, 2, 3 ], "ids" : [ 3, 4 ] }
