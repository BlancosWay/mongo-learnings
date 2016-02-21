# Security:

+ some of the security issues are caused by server side Javascript if done wrongly.
+ Javascript can be turn off entirely by starting the mongo instance with mongod --noscripting

+ User specific js code should not passed to the mongo instances on the server.
+ This could cause serious security issues.

##### Example:
    User passed name to the mongo.And we want to print "Hello,name!".
    The function might look like this.

    function(){
        print('Hello,' + name + '!');
    };

    If the user passed name as "') db.dropDatabase(); print('"
    It will drop a database.
