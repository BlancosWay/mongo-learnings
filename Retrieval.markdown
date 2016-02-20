# Retrival:

### Index
1. Find and FindOne
2. $ Conditions



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
