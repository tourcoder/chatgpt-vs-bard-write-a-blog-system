
1. Create a MongoDB database to store your blog posts.

```
// Create a MongoDB client.
const MongoClient = require("mongodb").MongoClient;

// Connect to the MongoDB database.
const client = new MongoClient("mongodb://localhost:27017");

// Get the database.
const db = client.db("blog");

// Create a collection for blog posts.
const posts = db.collection("posts");
```

2. Create a React application to display your blog posts.

```
// Create a React application.
npx create-react-app my-blog

// Change into the application directory.
cd my-blog

// Install the Mongoose library.
npm install mongoose
```
    
3. Connect your React application to your MongoDB database.

```
// Create a file called `database.js`.
touch database.js

// Add the following code to `database.js`.
const MongoClient = require("mongodb").MongoClient;

const client = new MongoClient("mongodb://localhost:27017");

const db = client.db("blog");

module.exports = {
  db
};
```

4. Create a blog post model in MongoDB.

```
// Create a file called `blog-post.js`.
touch blog-post.js

// Add the following code to `blog-post.js`.
const mongoose = require("mongoose");

const Schema = mongoose.Schema;

const BlogPostSchema = new Schema({
  title: {
    type: String,
    required: true
  },
  body: {
    type: String,
    required: true
  },
  date: {
    type: Date,
    required: true
  }
});

const BlogPost = mongoose.model("BlogPost", BlogPostSchema);

module.exports = BlogPost;
```

5. Create a blog post component in React.

```
// Create a file called `BlogPost.js`.
touch BlogPost.js

// Add the following code to `BlogPost.js`.
import React from "react";

const BlogPost = ({ title, body, date }) => {
  return (
    <div>
      <h1>{title}</h1>
      <p>{body}</p>
      <p>Published on {date}</p>
    </div>
  );
};

export default BlogPost;
```

6. Create a route for your blog post component.

```
// Create a file called `App.js`.
touch App.js

// Add the following code to `App.js`.
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router, Route } from "react-router-dom";

import BlogPost from "./components/BlogPost";

const App = () => (
  <Router>
    <Route path="/" component={BlogPost} />
  </Router>
);

ReactDOM.render(<App />, document.getElementById("root"));
```

7. Add styling to your blog post component.

```
// Create a file called `style.css`.
touch style.css

// Add the following code to `style.css`.
.blog-post {
  margin: 20px 0;
}

.blog-post h1 {
  font-size: 24px;
}

.blog-post p {
  font-size: 16px;
}
```