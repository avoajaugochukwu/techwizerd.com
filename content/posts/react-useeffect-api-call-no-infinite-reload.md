---
title: "How to use React useEffect to post data and refresh without infinite callback"
url: "posts/how-to-use-react-use-effect-to-post-data-and-refresh-without-infinite-callback"
author: "Charles Avoaja"
date: 2021-02-24T06:36:48+01:00
categories:
  - React
  - Axios
  - useEffect()
  - React Hooks
  - Intermediate React
draft: false
---

## Introduction
This tutorial will walk you through updating your page DOM after an API call, using react hooks.

In our example, we would be adding to a list of posts. After a new item is added to the list, we want to trigger react hooks to re-run our fetch API to update the list the user can see. The default behaviour of react ``useEffect()`` can lead to an infinite loop, although it is a working solution. But it is not a desirable one.

### Requirements
This tutorial requires a basic understanding of the following concepts
- React
- React Hooks (``useEffect(), useState()``)
- Axios API module (fetching and posting data)
- ``create-react-app``

## Let's get started
```
const Posts = () => {
  const [posts, setPosts] = useState([])
  const baseUrl = 'https://jsonplaceholder.typicode.com/posts/'

  const getPosts = async () => {
    try {
      const userPosts = await axios.get(baseUrl)
      setPosts(userPosts.data)
    } catch (error) {
        console.error(error.message)
    }
  }

  useEffect(() => {
      getPosts()
  }, [])

  /* Hook to add post name through API */
  const [newPost, setNewPost] = useState({ postName: '' })

  const handleChange = (event) => {
    setNewPost({ ...newPost, [event.target.name]: event.target.value })
  }

  const handleSubmit = (e) => {
    e.preventDefault()
    const url = baseUrl + 'add/'
    axios.post(url, {
        name: newPost.postName,
        user: 1
    })
    .then(response => {
        console.log(response.data)
    })
    .catch(error => {
        console.log(error.response.data)
    })
    e.target.reset()
  }

    return (
        <div>
            <form onSubmit={handleSubmit}>
              <input name="postName" onChange={handleChange} required />
              <button type="submit">Add Post Name</button>
            </form>
            
            {posts.map(post=>(
                <li key={post.id}>{post.title}</li>
            ))}
        </div>
    )
}
```

In the code above, we created a functional component called Posts that fetches and displays data from an API using axios. This component also has a form to add a new post name.

> The problem with this code is that when a new post name is added by ``axios.post`` to the database, the user cannot see it immediately. Unless they refresh the page.

By adding the dependency ``posts`` to ``useEffect()``. When a new post name is added, the list will automatically update for the user to see the new post name. 
```
useEffect(() => {
    getPosts()
}, [posts])
```

The problem with this solution is that if we include a simple ``console.log('I was hit')`` in the ``getPost()`` method, we will see that the ``useEffect()`` runs continuously. 

Adding ``posts`` as a dependency leads to an infinite loop, because of the way object reference works in JavaScript. See [react useeffect infinite loop](https://dmitripavlutin.com/react-useeffect-infinite-loop/).

## The Solution

The ideal solution is to use a dependency that is more predictable, like a simple count. This will be updated only after a successful axios POST request. That is we have successfully added a new post name.

Let us update our code.

```
const [postLength, setPostLength] = useState(0) #<----new
useEffect(() => {
    getPosts()
}, [postLength]) #<----new

```
And add ``setCartLength(cartLength + 1)`` to the successful callback of our API call.
```
const handleSubmit = (e) => {
  e.preventDefault()
  const url = baseUrl + 'add/'
  axios.post(url, {
      name: newPost.postName,
      user: 1
  })
  .then(response => {
      console.log(response.data)
      setCartLength(cartLength + 1) #<----new
  })
  .catch(error => {
      console.log(error.response.data)
  })
  e.target.reset()
}
```

What we have done here, is to add a new state called ``postLength``. 

Now the ``useEffect()`` will be listening for changes in ``postLength``. And its value changes, only then will ``getPosts`` be called again.

The API GET request for all posts will only be called again when a new post is added. We ensured that by adding the ``setCartLength(cartLength + 1)`` to the successful callback of our API POST request.

With this solution, we can update our list after every addition to the list. While avoiding the infinite loop of react ``useEffect()`` when using dependencies. 

This solution can also be extended to other operations like editing or deleting any item in the list. All you have to do is change the state of your dependency after a successful API call.