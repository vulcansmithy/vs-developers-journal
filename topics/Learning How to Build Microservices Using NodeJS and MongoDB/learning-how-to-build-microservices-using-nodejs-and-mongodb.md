### Steps

- create the directory for new Node.js app

  - ```
        > mkdir microservice
    ```

- Install the dependecy package Express and Request. Express is the backend framework for Node.js and Request is the package that will enable you to communicate with external API

  - ```
    > npm i express request --save
    ```

  - or

  - ```
    > npm install express request --save
    ```

  -  `i` is an abbreviation for install

  - `-save` attribute enables the packages to be added as dependencies in the `package.json` file. Although the later versions of npm packages are added automatically, it's still safe to include the `--save`.

- create a new file called `server.js`.The server.js should be in the same directory as the `package.json`.

  - ```
    > touch server.js
    ```

- in the `server.js` add the following code

  - ```javascript
    const express = require('express');
    const app = express();
    
    const routes = require('./api_routes/routes');
    routes(app);
    const port = process.env.PORT || 3000;
    
    app.listen(port, () => {
        console.log(`Listening to port http://localhost:${port}`);
    });
    ```



- start the node.js app

  - ```bash
    > node start
    ```




- using `body-parser`

  - ```javascript
    ...
    const bodyParser = require("body-parser");
    
    app.use(bodyParser.json());
    ...
    
    ```

    

- Note: Inside `nodemon`, to restart the server, type `rs`

- How to save the submitted json data into a microservices app 

  - Load Book model

    - ```javascript
      ...
      // Load Book model
      require("./Book");
      const Book = mongoose.model("Book");
      ...
      ```

  - Setup or have a POST endpoint that will receive submitted JSON data submitted on POST

    - ```javascript
      ...
      app.post("/book", (req, res) => {
      	...
      })
      ...
      ```

  - Inside the POST "/book" endpoint, create a local variable that will hold a collection of data that will be sourced out from the submitted JSON data

    - ```javascript
      app.post("/book", (req, res) => {
      		var newBook = {
              title: req.body.title,
              author: req.body.author,
              numberOfPages: req.body.numberOfPages,
              publisher: req.body.publisher
          }
      		...
      })
      ```

  - Instantiate a new Book instance and pass it the local collection variable when instantiating the Book model

    - ```javascript
      app.post("/book", (req, res) => {
      		var newBook = {
              title: req.body.title,
              author: req.body.author,
              numberOfPages: req.body.numberOfPages,
              publisher: req.body.publisher
          }
      
          // Create a new book
          var book = new Book(newBook)
      		...
      })
      ```

  - Call the save method in the `book` instanciated model

    - ```javascript
      app.post("/book", (req, res) => {
      		var newBook = {
              title: req.body.title,
              author: req.body.author,
              numberOfPages: req.body.numberOfPages,
              publisher: req.body.publisher
          }
      
          // Create a new book
          var book = new Book(newBook)
          
          book.save().then(() => {
          		console.log("New book created!")
          }).catch((err) => {
              if (err) {
                  throw err;
              }
          })
      		...
      })
      ```

    - Add a catch hook in case there is an exception or error. The if the save action is successful just print (thru console.log) the message "New book created!"

  - And finally, send a respond back to the API endpoint caller with a simple message

    - ```javascript
      app.post("/book", (req, res) => {
          var newBook = {
              title: req.body.title,
              author: req.body.author,
              numberOfPages: req.body.numberOfPages,
              publisher: req.body.publisher
          }
      
          // Create a new book
          var book = new Book(newBook)
      
          book.save().then(() => {
              console.log("New book created!")
          }).catch((err) => {
              if (err) {
                  throw err;
              }
          })
          res.send("A new book created with success!")
      })
      ```

- The way the routes are defined in Node.JS is somewhat similar to to how are things are done in Rails. This is from a perspective of a Rails developer. Although it much simpler, in my opinion, in terms of where the routes are declared. For example, to define a route to get all the books in the database

  - ```
    ...
    app.get("/books", (req, res) => {
    		...
    })
    ```

    - Observation: where the routes are declared is where the set of codes that would process the task the routes will be handling. As comparison to Rails where, the routes is a specified location, e.g., routes.rb and it will just "point" to the location where the controller/action method where the actual code that will process the specified route/endpoint.

- Another observation is that way parameters are defined in the definition of the "endpoint signature", e.g.,

  - "/books"
  - "/book/id"

  are somewhat similar to how things are done in Rails

- The only difference is how the parameters are retrieve. Given there is a routed defined as `/book/:id`, to retrieve it the actual code to do it are as follows

  ```
  ...
  req.params.id
  ...
  ```

  While, in Rails it would look something like this

  ```
  ...
  params[:id]
  ```

- TODO: further investigate this method

  - ```
    ...
    Book.findById(req.params.id).then((book) => {
    	...
    }
    ...
    ```

  - Need to know the answer to the following questions:

    - Is it something similar to ActiveRecords `find` statements?
    - If search by other attribute say `publisher` is there a `findByPublisher`, or
    - What method can be used for searching according to a specific attribute?
    - How to execute a `sql` related statement

- TODO: is there a same mechanism in Rails for Mongoose to do model attribute validation?



## Challenge: Figure out how to implement a code that would load ENV variable from the Node.JS app

- install `dot-env`

  - ```
    > npm install --save dotenv
    ```

- Once installed, create a `.env` file 

  - ```
    > touch .env
    ```

- Next, add a ENV variable that you want to accessible from the Node.JS app

  - ```bash
    > nano .env
    ```

  - ```
    PORT=4545
    ```

- Next, from the the Node.JS app, require the `dotenv` package, preferablly on the very top of. the source code

  - ```javascript
    require("dotenv").config;
    ...
    ...
    ```

- Then, where you need to load the value of the save ENV variable, the code should look like something like this

  - ```javascript
    ...
    ...
    const PORT = process.env.PORT || 4545;
    ...
    ...
    ```

    



- `reques` package is something similar to HTTParty gem on the ruby side. Note: According to the package page, this is already deprecated.

  

- ```javascript
  mongoose.model("Order"), {
    CustomerID: {
      type: mongoose.SchemaType.ObjectID,
      required: true    
    },
  	....
  })
  ```

  - `Mongoose.SchemaType.ObjectID` is a special type that identify the field as an ID. This ID is actually an object. 

  - Notable Links

    - https://masteringjs.io/tutorials/mongoose/objectid

      

- To convert a string ID to `mongoose.schemaType.ObjectID`, you need to cast the string into an ObjectID

  - ```javascript
    ...
    BookID: mongoose.Types.ObjectID(req.body.BookID)
    ...
    ```

    

- [Youtube: Using Clean Architecture for Microservice APIs in Node.js with MongoDB and Express](https://www.youtube.com/watch?v=CnailTcJV_U)

  - [Source repo:  github.com/dev-mastery/comments-api](https://github.com/dev-mastery/comments-ap)
  - 
