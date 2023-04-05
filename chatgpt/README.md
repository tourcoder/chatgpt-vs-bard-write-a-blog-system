Step 1: Set up the MongoDB database

First, you'll need to set up a MongoDB database to store your blog posts. You can do this either locally or using a cloud service like MongoDB Atlas.

Once you've set up your database, create a new collection called blog-posts to store your blog posts:

```
use my-blog-database // Replace with your own database name

db.createCollection("blog-posts", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["title", "content", "author", "date"],
      properties: {
        title: {
          bsonType: "string",
          description: "Title of the blog post",
        },
        content: {
          bsonType: "string",
          description: "Content of the blog post",
        },
        author: {
          bsonType: "string",
          description: "Author of the blog post",
        },
        date: {
          bsonType: "date",
          description: "Date of the blog post",
        },
      },
    },
  },
})
```

This code creates a new collection called blog-posts and specifies the required fields for each blog post document: title, content, author, and date.

Step 2: Set up the React app

Next, you'll create a new React app using create-react-app and install any necessary dependencies:

```
npx create-react-app my-blog-app
cd my-blog-app
npm install axios react-router-dom
```

This creates a new React app called my-blog-app and installs axios for making HTTP requests and react-router-dom for handling routing.

Step 3: Create a form for creating blog posts

Next, you'll create a form component that allows users to create new blog posts:

```
import React, { useState } from "react"
import axios from "axios"

function BlogPostForm() {
  const [title, setTitle] = useState("")
  const [content, setContent] = useState("")
  const [author, setAuthor] = useState("")

  const handleSubmit = async (event) => {
    event.preventDefault()

    const date = new Date()

    const newBlogPost = {
      title,
      content,
      author,
      date,
    }

    await axios.post("/api/blog-posts", newBlogPost)

    setTitle("")
    setContent("")
    setAuthor("")
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="title">Title</label>
        <input
          type="text"
          id="title"
          value={title}
          onChange={(event) => setTitle(event.target.value)}
        />
      </div>
      <div>
        <label htmlFor="content">Content</label>
        <textarea
          id="content"
          value={content}
          onChange={(event) => setContent(event.target.value)}
        ></textarea>
      </div>
      <div>
        <label htmlFor="author">Author</label>
        <input
          type="text"
          id="author"
          value={author}
          onChange={(event) => setAuthor(event.target.value)}
        />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}

export default BlogPostForm
```

This code defines a new component called BlogPostForm that includes fields for the blog post data (title, content, author). When the user submits the form, a new blog post document is created in the MongoDB database using an HTTP POST request to /api/blog-posts.

Step 4: Display a list of blog posts

Next, you'll create a component to display a list of all the blog posts in the database:

```
import React, { useEffect, useState } from "react"
import axios from "axios"

function BlogPostList() {
  const [blogPosts, setBlogPosts] = useState([])

  useEffect(() => {
    const fetchBlogPosts = async () => {
      const response = await axios.get("/api/blog-posts")
      setBlogPosts(response.data)
    }

    fetchBlogPosts()
  }, [])

  return (
    <div>
      {blogPosts.map((blogPost) => (
        <div key={blogPost._id}>
          <h2>{blogPost.title}</h2>
          <p>{blogPost.content}</p>
          <p>By {blogPost.author}</p>
          <p>Posted on {new Date(blogPost.date).toLocaleDateString()}</p>
        </div>
      ))}
    </div>
  )
}

export default BlogPostList
```

This code defines a new component called BlogPostList that retrieves all the blog posts from the database using an HTTP GET request to /api/blog-posts. The retrieved blog post documents are then mapped to individual JSX elements, which are displayed in the UI.

Step 5: Set up routing

Next, you'll use react-router-dom to set up routing for the different views of the blog system:

```
import React from "react"
import { BrowserRouter as Router, Route, Switch, Link } from "react-router-dom"
import BlogPostForm from "./BlogPostForm"
import BlogPostList from "./BlogPostList"

function App() {
  return (
    <Router>
      <div>
        <nav>
          <ul>
            <li>
              <Link to="/">Home</Link>
            </li>
            <li>
              <Link to="/new">New Blog Post</Link>
            </li>
          </ul>
        </nav>

        <Switch>
          <Route path="/new">
            <BlogPostForm />
          </Route>
          <Route path="/">
            <BlogPostList />
          </Route>
        </Switch>
      </div>
    </Router>
  )
}

export default App
```

This code defines the main App component and sets up two routes using react-router-dom: one for displaying the list of blog posts and one for displaying the form for creating new blog posts. The navigation menu at the top of the page allows users to switch between the two views.

Step 6: Implement editing and deleting of blog posts

Finally, you can implement editing and deleting of individual blog posts by adding new components and HTTP requests:

```
import React, { useState } from "react"
import axios from "axios"

function BlogPostEditor({ blogPost, onClose }) {
  const [title, setTitle] = useState(blogPost.title)
  const [content, setContent] = useState(blogPost.content)
  const [author, setAuthor] = useState(blogPost.author)

  const handleSubmit = async (event) => {
    event.preventDefault()

    const updatedBlogPost = {
      ...blogPost,
      title,
      content,
      author,
    }

    await axios.put(`/api/blog-posts/${blogPost._id}`, updatedBlogPost)

    onClose()
  }

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="title">Title</label>
          <input
            type="text"
            id="title"
            value={title}
            onChange={(event) => setTitle(event.target.value)}
          />
        </div>
        <div>
          <label htmlFor="content">Content</label>
          <textarea
            id="content"
            value={content}
            onChange={(event) => setContent(event.target.value)}
          />
        </div>
        <div>
          <label htmlFor="author">Author</label>
          <input
            type="text"
            id="author"
            value={author}
            onChange={(event) => setAuthor(event.target.value)}
          />
        </div>
        <button type="submit">Save</button>
      </form>
      <button onClick={onClose}>Cancel</button>
    </div>
  )
}

function BlogPost({ blogPost, onEdit, onDelete }) {
  return (
    <div>
      <h2>{blogPost.title}</h2>
      <p>{blogPost.content}</p>
      <p>By {blogPost.author}</p>
      <p>Posted on {new Date(blogPost.date).toLocaleDateString()}</p>
      <button onClick={onEdit}>Edit</button>
      <button onClick={onDelete}>Delete</button>
    </div>
  )
}

function BlogPostList() {
  const [blogPosts, setBlogPosts] = useState([])

  const fetchBlogPosts = async () => {
    const response = await axios.get("/api/blog-posts")
    setBlogPosts(response.data)
  }

  useEffect(() => {
    fetchBlogPosts()
  }, [])

  const handleDelete = async (blogPost) => {
    if (window.confirm(`Are you sure you want to delete "${blogPost.title}"?`)) {
      await axios.delete(`/api/blog-posts/${blogPost._id}`)
      await fetchBlogPosts()
    }
  }

  return (
    <div>
      {blogPosts.map((blogPost) => (
        <BlogPost
          key={blogPost._id}
          blogPost={blogPost}
          onEdit={() => alert("Not implemented yet!")}
          onDelete={() => handleDelete(blogPost)}
        />
      ))}
    </div>
  )
}

export default BlogPostList
```

Note that the editing functionality has not yet been implemented. You can do this by modifying the onEdit prop of the BlogPost component to display the BlogPostEditor component with the current blog post as its initial values:

```
function BlogPost({ blogPost, onEdit, onDelete }) {
  const [editing, setEditing] = useState(false)

  const handleEdit = () => {
    setEditing(true)
  }

  const handleCloseEditor = () => {
    setEditing(false)
  }

  return (
    <div>
      {editing ? (
        <BlogPostEditor blogPost={blogPost} onClose={handleCloseEditor} />
      ) : (
        <div>
          <h2>{blogPost.title}</h2>
          <p>{blogPost.content}</p>
          <p>By {blogPost.author}</p>
          <p>Posted on {new Date(blogPost.date).toLocaleDateString()}</p>
          <button onClick={handleEdit}>Edit</button>
          <button onClick={onDelete}>Delete</button>
        </div>
      )}
    </div>
  )
}
```

This code defines a new state variable editing and a function handleEdit that sets it to true. The BlogPost component now conditionally renders either the BlogPostEditor component or the existing HTML, depending on whether editing is true or false.

When the "Edit" button is clicked, handleEdit is called, which sets editing to true. This causes the BlogPostEditor component to be rendered instead of the existing HTML.

The BlogPostEditor component is passed the current blog post as its initial values, which are set as the initial values of the title, content, and author fields.

When the "Save" button is clicked, the handleSubmit function is called, which updates the blog post with the new values and sends a PUT request to the server. When the request completes, the onClose function is called, which sets editing to false and causes the BlogPost component to be rendered again.

With these changes, you should now be able to edit and delete individual blog posts in your application.

Step 7: Implement creating new blog posts

Now that you can view, edit, and delete existing blog posts, the final step is to implement the ability to create new blog posts. To do this, you'll need to add a new component called NewBlogPostForm that will allow users to enter the information for a new blog post.

1. Create a new component called NewBlogPostForm. This component should render a form that allows the user to enter the title, content, and author of the new blog post.

```
function NewBlogPostForm({ onSubmit }) {
  const [title, setTitle] = useState('')
  const [content, setContent] = useState('')
  const [author, setAuthor] = useState('')

  const handleSubmit = (event) => {
    event.preventDefault()
    const date = new Date().toISOString()
    const blogPost = { title, content, author, date }
    onSubmit(blogPost)
    setTitle('')
    setContent('')
    setAuthor('')
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="title">Title:</label>
        <input
          type="text"
          id="title"
          value={title}
          onChange={(event) => setTitle(event.target.value)}
        />
      </div>
      <div>
        <label htmlFor="content">Content:</label>
        <textarea
          id="content"
          value={content}
          onChange={(event) => setContent(event.target.value)}
        />
      </div>
      <div>
        <label htmlFor="author">Author:</label>
        <input
          type="text"
          id="author"
          value={author}
          onChange={(event) => setAuthor(event.target.value)}
        />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}
```

This code defines a new component called NewBlogPostForm that renders a form with input fields for the title, content, and author of a new blog post.

The component uses the useState hook to manage the state of the input fields. When the user submits the form, the handleSubmit function is called, which creates a new blog post object with the current values of the input fields, sets the state of the input fields to their initial values, and calls the onSubmit prop with the new blog post object as an argument.

1. Add the NewBlogPostForm component to the App component. Render a button that, when clicked, toggles the visibility of the NewBlogPostForm component.

```
function App() {
  const [blogPosts, setBlogPosts] = useState([])
  const [showNewBlogPostForm, setShowNewBlogPostForm] = useState(false)

  const handleCreateBlogPost = async (blogPost) => {
    const response = await fetch('/api/blog-posts', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(blogPost),
    })
    const newBlogPost = await response.json()
    setBlogPosts((prevState) => [...prevState, newBlogPost])
    setShowNewBlogPostForm(false)
  }

  const handleDeleteBlogPost = async (blogPostId) => {
    await fetch(`/api/blog-posts/${blogPostId}`, {
      method: 'DELETE',
    })
    setBlogPosts((prevState) => prevState.filter((post) => post._id !== blogPostId))
  }

  useEffect(() => {
    const fetchBlogPosts = async () => {
      const response = await fetch('/api/blog-posts')
      const data = await response.json()
      setBlogPosts(data)
    }
    fetchBlogPosts()
  }, [])

  return (
    <div>
      <h1>My Blog</h1>
      <button onClick={() => setShowNewBlogPostForm((prevState) => !prevState)}>
        {showNewBlogPostForm ? 'Hide Form' : 'New Blog Post'}
      </button>
      {showNewBlogPostForm && <NewBlogPostForm onSubmit={handleCreateBlogPost} />}
      <BlogPostList blogPosts={blogPosts} onDelete={handleDeleteBlogPost} />
    </div>
  )
}
```

This code adds a new state variable called showNewBlogPostForm, which is initially set to false. It also defines a new function called handleCreateBlogPost that sends a POST request to the /api/blog-posts endpoint with the new blog post data, and updates the state of the blog posts with the new post that is returned by the server. Finally, it adds a new button that toggles the visibility of the NewBlogPostForm component when clicked.

1. Update the server.js file to handle POST requests to the /api/blog-posts endpoint. Add a new route handler that creates a new blog post in the database and returns the new post to the client.

```
app.post('/api/blog-posts', async (req, res) => {
  const { title, content, author, date } = req.body
  const blogPost = new BlogPost({ title, content, author, date })
  await blogPost.save()
  res.json(blogPost)
})
```

This code defines a new route handler that extracts the title, content, author, and date from the request body and creates a new BlogPost object with those values. It then saves the new blog post to the database and returns the new post to the client as a JSON object.

That's it! You now have a fully functional blog system with React and MongoDB. You can add new blog posts, view, edit, and delete existing blog posts, and store everything in a MongoDB database. Of course, there are many additional features you could add to make the system more robust and user-friendly, but this should be enough to get you started.