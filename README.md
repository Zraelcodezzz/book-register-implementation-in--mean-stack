# book-register-implementation-in--mean-stack

We're going to simple bopk register webfrm in mean stack

A simple book register in the MEAN stack (MongoDB, Express, Angular, Node.js) is a web application that allows users to add, view, update, and delete books from a database.

MongoDB is used as the database to store book details such as title, author, and publication date.
Express.js handles the server-side logic, managing API endpoints to interact with the database.
Angular provides the front-end interface, allowing users to interact with the app via forms and views.
Node.js serves as the backend runtime to execute the Express.js server, handling requests and responses.

![image](https://github.com/user-attachments/assets/dd84b878-eb01-452b-be35-d3de7b673d22)

**STEP 0: REQUIREMENTS**

AWS EC2 Instance..Let's quick launch oone

![image](https://github.com/user-attachments/assets/72e094c6-c068-4780-96d2-4e35fc68a552)

We've got our instance set..now 

![image](https://github.com/user-attachments/assets/a0bed7d4-8019-4ddb-af95-876c0dc0dfdd)

Dash to the CLI and SSH into the instance.

![image](https://github.com/user-attachments/assets/7751f342-e07a-44fb-b3c9-3032391a9db7)


**STEP 1: NODEJS INSTALLATION**

i. Firstly,let's update Ubuntu

         sudo apt update

  ![image](https://github.com/user-attachments/assets/90aa685c-f007-4584-be5c-40063292efc1)
       
         
ii.  Then Upgrade Ubuntu

            
            sudo apt upgrade

   ![image](https://github.com/user-attachments/assets/bd50ade7-e529-4caa-b3e8-652109b3cfc5)


iii.    Next we add certificates.

            sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
            
            
            curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   
         
  These commands prepare our system for installing Node.js 18.x by installing necessary tools (like `curl` and `dirmngr`), adding the NodeSource repository, and setting up secure HTTPS connections for package management. After running them, we'll be ready to install Node.js from the NodeSource repo.

  ![image](https://github.com/user-attachments/assets/e50ae3ba-999e-4300-9ad9-1dd6aaab50f3)



iv.       Then we can now install Nodejs.

                   sudo apt install -y nodejs

  ![image](https://github.com/user-attachments/assets/e4ecc1b6-fbbf-4211-907f-0d2601b6eab9)


**STEP 2: MONGODEB INSTALLATION**

 MongoDB stores data in flexible, JSON-like documents. The fields in the database can differ between documents, and the data structure can adapt over time. In our example, we will store book records in MongoDB, including details like the book title, ISBN number, author, and number of pages.

 i.  Let's install necessary tools first.

                  sudo apt-get install -y gnupg curl

This command installs gnupg (GNU Privacy Guard, required for verifying packages) and curl (used to fetch files from URLs).

![image](https://github.com/user-attachments/assets/87e2a358-cfc1-47b8-9750-a88c076b190e)

                curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
                 sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
                 --dearmor

This fetches the MongoDB public GPG key and saves it in a secure format to /usr/share/keyrings/ for use during installation.

  ![image](https://github.com/user-attachments/assets/c2c572ea-1444-431b-8a22-6b34303e870c)

   We then add MongoDB repository to the list of sources:

     echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

This adds the MongoDB repository to the system's package manager, allowing MongoDB to be installed using apt.

![image](https://github.com/user-attachments/assets/08cbdab4-c570-40fb-a269-d20c3161538b)

Then we install

                    sudo apt-get install -y mongodb-org

![image](https://github.com/user-attachments/assets/51cdaaef-0431-4de1-8bc2-e3225b652293)


ii.  Now let's start our MongoDB server

                    sudo service mongodb start


 Verify it's running using 

     sudo systemctl status mongod

  ![image](https://github.com/user-attachments/assets/e85fabfa-3a0b-40ec-8160-f4bfebf86a23)

iii. Next we install Node package manager

                sudo apt install -y npm
                
 ![image](https://github.com/user-attachments/assets/a23879b3-bcd2-4b0c-bb8d-e5cd0c9d9645)


 iv. We then install 'body-parser' package.This helps us to process JSON files passed in requests to the server.

     sudo npm install body-parser

  ![image](https://github.com/user-attachments/assets/817024de-175f-4ed0-af31-d8482db83fb0)


  **Now that let's get our files structured and app initialized**


   i.  We create a folder for our books and get in there

                  mkdir Books && cd Books


 ii.  Next we initialize our app

                   npm init

  ![image](https://github.com/user-attachments/assets/c68052cf-4135-41ba-ab71-e25ff5e2cd06)


iii.  We create a server.js file in our Books directory that  sets up our Express.js server and connects to the MongoDB database.


                            const express = require('express');
                            const bodyParser = require('body-parser');
                            const mongoose = require('mongoose');
                            const path = require('path');
                            const app = express();
                             const PORT = process.env.PORT || 3300;

                             // MongoDB connection
                             mongoose.connect('mongodb://localhost:27017/test', {
                             useNewUrlParser: true,
                              useUnifiedTopology: true,
                              })
                            .then(() => console.log('MongoDB connected'))
                             .catch(err => console.error('MongoDB connection error:', err));

                            app.use(express.static(path.join(__dirname, 'public'))); 
                             app.use(bodyParser.json());

                             require('./apps/routes')(app);

                            app.listen(PORT, () => {
                            console.log(`Server up: http://localhost:${PORT}`);
                            });


![image](https://github.com/user-attachments/assets/caab1ba0-03f3-4784-9a8a-061f4ff69c47)


**STEP 3: EXPRESS INSTALL AND ROUTES SETUP**


i. We get express and mongoose onboard by:

          sudo npm install express mongoose
          
 ![image](https://github.com/user-attachments/assets/d2a78edd-6e08-4481-9454-6c4706759bd5)


ii.  In our books directory we created apps folder

             mkdir apps && cd apps
             
iii. While in apps we create routes.js we then defines routes for our book registry API using Express, allowing users to fetch all books, add a new book, and delete a book by ISBN. It also serves an HTML file for any other route requests.

             const Book = require('./models/book');
             const path = require('path');

       module.exports = function(app) {
    app.get('/book', async (req, res) => {
        try {
            const books = await Book.find();
            res.json(books);
        } catch (err) {
            res.status(500).json({ message: 'Error fetching books', error: err.message });
        }
    });

    app.post('/book', async (req, res) => {
        try {
            const book = new Book({
                name: req.body.name,
                isbn: req.body.isbn,
                author: req.body.author,
                pages: req.body.pages
            });
            const savedBook = await book.save();
            res.status(201).json({
                message: 'Successfully added book',
                book: savedBook
            });
        } catch (err) {
            res.status(400).json({ message: 'Error adding book', error: err.message });
        }
    });

    app.delete('/book/:isbn', async (req, res) => {
        try {
            const result = await Book.findOneAndDelete({ isbn: req.params.isbn });
            if (!result) {
                return res.status(404).json({ message: 'Book not found' });
            }
            res.json({ message: 'Successfully deleted the book', book: result });
        } catch (err) {
            res.status(500).json({ message: 'Error deleting book', error: err.message });
        }
    });

    app.get('*', (req, res) => {
        res.sendFile(path.join(__dirname, '../public', 'index.html'));
    });
    };


  ![image](https://github.com/user-attachments/assets/39776c50-929d-4ab1-8074-12ca4937e556)


  iv.  Next we create our models in the apps folder

             mkdir models && cd models

   while in models we create a book.js file


      const mongoose = require('mongoose');
     const bookSchema = new mongoose.Schema({
    name: { type: String, required: true },
    isbn: { type: String, required: true, unique: true, index: true },
    author: { type: String, required: true },
    pages: { type: Number, required: true, min: 1 }
    }, {
    timestamps: true
    });

    module.exports = mongoose.model('Book', bookSchema);

  ![image](https://github.com/user-attachments/assets/a154054a-7da5-47b3-826d-bbbf75815d08)

  The code defines a Mongoose schema for a "Book" model, specifying fields for name, ISBN, author, and pages, along with validation rules. It exports the model for use in the application to interact with the corresponding MongoDB collection.


  **STEP 4: ACCESS THE ROUTES WITH ANGULAR JS**

  i. Firstly,let's reverse back to our Books directory

    cd ../..


 ii.  Next we create a  folder named public   

           mkdir public && cd public
           
![image](https://github.com/user-attachments/assets/37213996-d72c-4c8f-b015-685ad37116e2)

 iii.   Then create a file named script.js and  define the AngularJS controller that fetches books from the server, enables users to add new books, and delete existing ones. It updates the displayed book list after each operation while handling errors for HTTP requests.

      angular.module('myApp', [])
     .controller('myCtrl', function($scope, $http) {
    function fetchBooks() {
        $http.get('/book')
        .then(response => {
            $scope.books = response.data;
        })
        .catch(error => {
            console.error('Error fetching books:', error);
        });
    }

    fetchBooks();

    $scope.del_book = function(book) {
        $http.delete(`/book/${book.isbn}`)
        .then(() => {
            fetchBooks();
        })
        .catch(error => {
            console.error('Error deleting book:', error);
        });
    };

    $scope.add_book = function() {
        const newBook = {
            name: $scope.Name,
            isbn: $scope.Isbn,
            author: $scope.Author,
            pages: $scope.Pages
        };

        $http.post('/book', newBook)
        .then(() => {
            fetchBooks();
            // Clear form fields
            $scope.Name = $scope.Isbn = $scope.Author = $scope.Pages = '';
        })
        .catch(error => {
            console.error('Error adding book:', error);
        });
    };
    });

  ![image](https://github.com/user-attachments/assets/d1122ff6-f045-4bc3-8b83-8df7fd61e453)


iv.    Next we create an index.html file that creates a simple AngularJS-based web application for managing books, allowing us to add new books and delete existing ones. It includes a form for book details and a table to display the list of books fetched from our backend API.


    <!DOCTYPE html>
    <html ng-app="myApp" ng-controller="myCtrl">
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Book Management</title>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.8.2/angular.min.js"></script>
    <script src="script.js"></script>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        table { border-collapse: collapse; width: 100%; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
        input[type="text"], input[type="number"] { width: 100%; padding: 5px; }
        button { margin-top: 10px; padding: 5px 10px; }
    </style>
    </head>
    <body>
    <h1>Book Management Application</h1>
    <form ng-submit="add_book()">
        <label>Name:</label>
        <input type="text" ng-model="Name" required>
        <label>ISBN:</label>
        <input type="text" ng-model="Isbn" required>
        <label>Author:</label>
        <input type="text" ng-model="Author" required>
        <label>Pages:</label>
        <input type="number" ng-model="Pages" required>
        <button type="submit">Add Book</button>
    </form>
    <h2>Books</h2>
    <table>
        <thead>
            <tr>
                <th>Name</th>
                <th>ISBN</th>
                <th>Author</th>
                <th>Pages</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody>
            <tr ng-repeat="book in books">
                <td>{{ book.name }}</td>
                <td>{{ book.isbn }}</td>
                <td>{{ book.author }}</td>
                <td>{{ book.pages }}</td>
                <td><button ng-click="del_book(book)">Delete</button></td>
            </tr>
        </tbody>
    </table>
    </body>
    </html>


v. We then return to our books directory and start our server

         node server.js

![image](https://github.com/user-attachments/assets/e35bb0c3-3828-4575-8867-2571435683fb)

vii. Our server is running on port 3300 we then go to edit our inbound rules in our ec2 instance to open TCP port 33oo so that our server can be accessible from our browser.


  ![image](https://github.com/user-attachments/assets/a7f88384-74ad-4941-8c7c-7f7094f93cd0)


  Now our books register should be accessble via our broswer using our publicIP address suffixed by port 3300

http://<public_ip>:3300
  

  ![image](https://github.com/user-attachments/assets/33776564-0f31-4fa0-94c1-35d45a8f8a4e)


  Cheers!




    

               


          


             




 

      
          


                  
           

         

       
    

     
               
  
                   








