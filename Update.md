• Updating  Documents:
  ○ Direct Updates:

    § Notation:db.collectionName.update(query,fullDocument);
      Need to give full document to update.
  ○ Modifiers:

  § $inc(used only on numbers):

        Notation: db.collection.update(query,{ $inc: { <field1>: <amount1>, <field2>: <amount2>, ... } },{multi:<true|false>});
        To specify the $inc on embedded fields or array fields use DOT operator.
      Behavior:
        By default it is a atomic operation on only one document.
        Use multi field to update multiple documents.
        Takes negative or positive numbers.
        Creates the field if the field is not present and set the value to the amount specified in the update query.In case of embedded fields the path with also be created as specified.

      Example :
      db.sample.insert([
        {
          name:"harsha",
          fullname:"sriharsha nadella",
          age:24,
          address:{
            streetname:"highschool",
            flatno:341
          }
        },
        {
          name:"harsha",
          fullname:"sriharsha nadella",
          age:25,
          address:{
            streetname:"highschool",
            flatno:341
          }
        },
        {
          name:"harsha",
          fullname:"sriharsha nadella",
          age:26,
          address:{
            streetname:"highschool",
            flatno:341
          }
        }
      ]);

      db.sample.update({name:"harsha"},{$inc:{age:1}});

      db.sample.find();
      { "_id" : ObjectId("56b6d793d82cb34c7c49824a"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 25, "address" : { "streetname" : "highschool", "flatno" : 341 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824b"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 25, "address" : { "streetname" : "highschool", "flatno" : 341 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824c"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 26, "address" : { "streetname" : "highschool", "flatno" : 341 } }

      db.sample.update({name:"harsha"},{$inc:{age:1}},{multi:true});

      db.sample.find();
      { "_id" : ObjectId("56b6d793d82cb34c7c49824a"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 26, "address" : { "streetname" : "highschool", "flatno" : 341 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824b"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 26, "address" : { "streetname" : "highschool", "flatno" : 341 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824c"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 27, "address" : { "streetname" : "highschool", "flatno" : 341 } }

      db.sample.update({name:"harsha"},{$inc:{age:1,"address.flatno":1}});

      db.sample.find();
      { "_id" : ObjectId("56b6d793d82cb34c7c49824a"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 27, "address" : { "streetname" : "highschool", "flatno" : 342 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824b"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 26, "address" : { "streetname" : "highschool", "flatno" : 341 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824c"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 27, "address" : { "streetname" : "highschool", "flatno" : 341 } }

      db.sample.update({name:"harsha"},{$inc:{age:1,"address.flatno":1}},{multi:true});

      db.sample.find();
      { "_id" : ObjectId("56b6d793d82cb34c7c49824a"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 343 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824b"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 27, "address" : { "streetname" : "highschool", "flatno" : 342 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824c"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 342 } }

      db.sample.update({name:'harsha'},{$set:{ids:[1,2,3]}},{multi:true,upsert:true});

      db.sample.find();
      { "_id" : ObjectId("56b6d793d82cb34c7c49824a"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 343 }, "ids" : [ 1, 2, 3 ] }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824b"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 27, "address" : { "streetname" : "highschool", "flatno" : 342 }, "ids" : [ 1, 2, 3 ] }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824c"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 342 }, "ids" : [ 1, 2, 3 ] }

      db.sample.update({name:"harsha"},{$inc:{'ids.0':1}});
      db.sample.find();
      { "_id" : ObjectId("56b6d793d82cb34c7c49824a"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 343 }, "ids" : [ 2, 2, 3 ] }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824b"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 27, "address" : { "streetname" : "highschool", "flatno" : 342 }, "ids" : [ 1, 2, 3 ] }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824c"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 342 }, "ids" : [ 1, 2, 3 ] }

      db.sample.update({name:"harsha"},{$inc:{'ids.0':1}},{multi:true});
      db.sample.find();
      { "_id" : ObjectId("56b6d793d82cb34c7c49824a"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 343 }, "ids" : [ 3, 2, 3 ] }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824b"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 27, "address" : { "streetname" : "highschool", "flatno" : 342 }, "ids" : [ 2, 2, 3 ] }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824c"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 342 }, "ids" : [ 2, 2, 3 ] }

      db.sample.update({name:"harsha"},{$inc:{'dep.id':1}});

      db.sample.find();
      { "_id" : ObjectId("56b6d793d82cb34c7c49824a"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 343 }, "ids" : [ 3, 2, 3 ], "dep" : { "id" : 1 } }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824b"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 27, "address" : { "streetname" : "highschool", "flatno" : 342 }, "ids" : [ 2, 2, 3 ] }
      { "_id" : ObjectId("56b6d793d82cb34c7c49824c"), "name" : "harsha", "fullname" : "sriharsha nadella", "age" : 28, "address" : { "streetname" : "highschool", "flatno" : 342 }, "ids" : [ 2, 2, 3 ] }


  § $set(used for any update on key)

      Notation: db.collection.update(query,{ $set: { <field1>: <value1>, ... } },{multi:<true|false>}}
        To specify the $set on embedded fields or array fields use DOT operator.

    Speed: Can be used for numbers as well but $inc is fast for numbers.

    Behaviour:
        By default it is a atomic operation on only one document.
        Use multi field to update multiple documents.
        Creates the field if the field is not present and set the value to the amount specified in the update query.In case of embedded fields the path with also be created as specified.
        If you specify multiple field-value pairs, $set will update or create each field.

    Example:
        db.sample.insert({
          _id: 100,
          sku: "abc123",
          quantity: 250,
          instock: true,
          reorder: false,
          details: { model: "14Q2", make: "xyz" },
          tags: [ "apparel", "clothing" ],
          ratings: [ { by: "ijk", rating: 4 } ]
        });

        db.sample.find();
        { "_id" : 100, "sku" : "abc123", "quantity" : 250, "instock" : true, "reorder" : false,
           "details" : { "model" : "14Q2", "make" : "xyz" }, "tags" : [ "apparel", "clothing" ], "ratings" : [ { "by" : "ijk", "rating" : 4 } ] }

        db.sample.update({_id:100},{$set:{sku:"abc1234",quantity: 251, 'details.model':"14Q34",'details.make':"Axyz",'ratings.0.rating':5}});

        db.sample.find();
        { "_id" : 100, "sku" : "abc1234", "quantity" : 251, "instock" : true, "reorder" : false,
           "details" : { "model" : "14Q34", "make" : "Axyz" }, "tags" : [ "apparel", "clothing" ], "ratings" : [ { "by" : "ijk", "rating" : 5 } ] }

    § $unset
      Notation:  db.collection.update(query,{ $unset: { <field1>: "", ... } });
        To specify the array of embedded field use DOT operator.
      Behavior:
        If the field is not present, no operation will be performed.
        When used with $ to match an array element, $unset replaces the matching element with null rather than removing the matching element from the array. This behavior keeps consistent the array size and element positions.
      Example:
        db.sample.insert({
          _id: 100,
          sku: "abc123",
          quantity: 250,
          instock: true,
          reorder: false,
          details: { model: "14Q2", make: "xyz" },
          tags: [ "apparel", "clothing" ],
          ratings: [ { by: "ijk", rating: 4 },{ by: "harsha", rating: 5 } ]
        });

        db.sample.find();
        { "_id" : 100, "sku" : "abc123", "quantity" : 250, "instock" : true, "reorder" : false,
          "details" : { "model" : "14Q2", "make" : "xyz" }, "tags" : [ "apparel", "clothing" ],
           "ratings" : [ { "by" : "ijk", "rating" : 4 }, { "by" : "harsha", "rating" : 5 } ] }

        db.sample.find();
        { "_id" : 100, "instock" : true, "reorder" : false, "details" : { "model" : "14Q2" }, "tags" : [ "apparel", "clothing" ],
         "ratings" : [ { "by" : "ijk", "rating" : 4 }, { "by" : "harsha" } ] }

        db.sample.update({_id:100,'ratings.by':"ijk"},{$unset:{sku:"",quantity: "",'details.make':"",'ratings.1.rating':"",'ratings.$.by':""}});

        db.sample.find();
        { "_id" : 100, "instock" : true, "reorder" : false, "details" : { "model" : "14Q2" }, "tags" : [ "apparel", "clothing" ], "ratings" : [ { "rating" : 4 }, { "by" : "harsha" } ] }


  ○ Array Modifiers:

    § $push

      □ $push Single Value :

        Adds elements to the end of the array.

        Notation: db.collection.update(query,{ $push: { <field1>: <value1>, ... } });
          To specify the fields in embedded or array use DOT Operation.

        Behavior:
          If the field is not present, it will create the field along with path variables needed.
          If the field is present and not an array, the push operation will fail.

        Example:
          db.sample.insert([{name:"harsha"}]);

          db.sample.find()
          { "_id" : ObjectId("56b8cf394a67dad36a46d15a"), "name" : "harsha" }

          db.sample.update({name:"harsha"},{$push:{ids:24}});

          db.sample.find()
          { "_id" : ObjectId("56b8cf394a67dad36a46d15a"), "name" : "harsha", "ids" : [ 24 ] }

      □ $push Multi values:

        Use $each along with $push.
        Adds Elements to the end of the array.

        Notation: { $push: { <field1>: { $each: <value1>, ... }, ... } }
          To specify the fields in embedded or array use DOT operation.

        Behavior:
          If the field is not present, it will create the field along with path variables needed.
          If the field is present and not an array, the push operation will fail.

        Example:
          db.sample.drop();
          db.sample.insert([{name:"harsha"}]);

          db.sample.find()
          { "_id" : ObjectId("56b8cf394a67dad36a46d15a"), "name" : "harsha" }

          db.sample.update({name:"harsha"},{$push:{ids:{$each:[24,25,26]}}});

          db.sample.find()
          { "_id" : ObjectId("56b8cf394a67dad36a46d15a"), "name" : "harsha", "ids" : [ 24, 25, 26 ] }

      □ $Push with other modifiers:
        Modifier	Description
        $each	Appends multiple values to the array field.When used in conjunction with the other modifiers, the $each modifier no longer needs to be first.
        $slice	Limits the number of array elements. Requires the use of the $each modifier. -n for last n values in the array. +n for the first n values in the array.
        $sort	Orders elements of the array. Requires the use of the $each modifier. -1 for descending order. +1 for the ascending order.
        $position	Specifies the location in the array at which to insert the new elements. Requires the use of the $each modifier. Without the $position modifier, the $push appends the elements to the end of the array.

        Examples:
          db.sample.insert({name:"harsha",ids:[1,2,3,4,5]});

          db.sample.find();
          { "_id" : ObjectId("56b8d5044a67dad36a46d15d"), "name" : "harsha", "ids" : [ 1, 2, 3, 4, 5 ] }

          db.sample.update({name:"harsha"},{$push: {ids:{ $each: [6,7,8,9], $slice: -5, $sort: -1}} });

          db.sample.find();
          { "_id" : ObjectId("56b8d5044a67dad36a46d15d"), "name" : "harsha", "ids" : [ 5, 4, 3, 2, 1 ] }

          db.sample.update({name:"harsha"},{$push: {ids:{ $each: [6,7,8,9], $slice: 5, $sort: -1}} });

          db.sample.find();
          { "_id" : ObjectId("56b8d5044a67dad36a46d15d"), "name" : "harsha", "ids" : [ 9, 8, 7, 6, 5 ] }

          db.sample.update({name:"harsha"},{$push: {ids:{ $each: [6,7,8,9], $slice: 5, $sort: 1}} });

          db.sample.find();
          { "_id" : ObjectId("56b8d5044a67dad36a46d15d"), "name" : "harsha", "ids" : [ 5, 6, 6, 7, 7 ] }
