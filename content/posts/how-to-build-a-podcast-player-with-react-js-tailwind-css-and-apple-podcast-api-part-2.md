---
title: "How to Build a Podcast Player With React Js Tailwind Css and Apple Podcast Api Part 2"
author: "Ugochukwu Avoaja"
date: 2021-06-23T10:03:28+01:00
categories:
  - React
  - TailwindCSS
  - API
  - React-Redux
  - Axios
  - React Hooks
draft: false
---

## Building the homepage

Now we would focus on the homepage.

Before tackling the homepage, you need to provide the dependencies and files that the homepage needs because the homepage will be the first time, we are interacting with Apples' API. 

The following will be done:

- Install axios
- Create a loading spinner
- Spin up your own reverse proxy server
- Create URL constants in a single file, to easily manage our URLs

### Other parts of the tutorial
📘 [How to Build a Podcast Player With React Js part 1](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api/)

📘 [How to Build a Podcast Player With React Js part 2](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-2/)

📘 [How to Build a Podcast Player With React Js part 3](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-3/)

📘 [How to Build a Podcast Player With React Js part 4](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-4/)

📘 [How to Build a Podcast Player With React Js part 5](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-5/)

### Install axios

From the root of your project. Run:

```
yarn add axios
```

### Create loading spinner

Visit [this site](https://loading.io/css/) to get loading elements. You can choose anyone.

Create a file called ``Spinner`` within the ``containers`` folder. Then create two files in the ``Spinner`` folder named ``Loading.css`` and ``Loading.jsx``.

Click on any loader you like from the site. A modal shows you the HTML and CSS for that loader.

You can copy mine.

> 🔗 You can [visit GitHub](https://github.com/avoajaugochukwu/podcast_player_tutorial/tree/master/Checkpoint_02/src/containers/Spinner) and copy my loading files for the CSS and JavaScript.

### Spin up your own reverse proxy spinner

> 💡 You can skip this step if it is confusing and use mine then come back to it when you are done with the tutorial

A reverse proxy is a server that sits in front of web servers and forwards client (e.g., a web browser) requests to those web servers. They are typically implemented to help increase security, performance, and reliability.

CORS Anywhere provided by [Rob W](https://github.com/Rob--W/cors-anywhere) will be used.

CORS Anywhere is a NodeJS proxy that adds CORS headers to the proxied request. I noticed many CORS issues when I deployed the website; namely, the Apple API server did not set the response header.

CORS Anywhere will prevent this. To ensure that the application doesn't crash when it is deployed.

> 💡 You can use mine by just using my code. But if so many users are using the app. It might hit the limitations set by Heroku.

Heroku will be used to host our own CORS Anywhere proxy server. 

From your command line, go to a folder where you want to host the code.

Then enter the following code in your command prompt or terminal and run them one after the other.

```
git clone https://github.com/Rob--W/cors-anywhere.git
cd cors-anywhere/
npm install
heroku create
git push heroku master
```

It might prompt you to log in to Heroku from the command prompt. Complete that so that the Heroku CLI will have access to your account.

> 💡 I will assume that you are still using mine for the rest of this tutorial. But I will advise that you build yours if you plan to host the application and showcase it to your friends or employers. So you can be sure that it is always online.

The reverse proxy is used by adding it before the API URL. So when the joined URL is requested. The reverse proxy server adds a header to the request before sending it off to the Apples' servers. I will provide more details in the next section.


### Create URL constants in a single file, to easily manage our URLs

In the ``src`` folder, create a new folder called ``utils`` and add a file called ``consts.js`` in the ``utils`` folder.

Then copy the code below into the ``consts.js`` file.

```
const REVERSEPROXY_URL = 'https://secret-beyond-79263.herokuapp.com/' // Custom reverse proxy
const APPLE_PODCAST_URL = 'https://itunes.apple.com/'

export const BASE_URL = String(REVERSEPROXY_URL + APPLE_PODCAST_URL)

const HOME_SCREEN_PODCAST_COLLECTION_IDS = '278981407,863897795,1191775648,582272991,1200361736,1322200189,1379959217,998568017,1081244497,1062418176,1334878780,316045799,480486345,265307784,643055307,1057255460,1077418457,268213039,1258635512,169078375'

export const HOMESCREEN_API_URL = String(`${BASE_URL}lookup?id=${HOME_SCREEN_PODCAST_COLLECTION_IDS}`)
```

If you created your own reverse proxy, replace the ``https://secret-beyond-79263.herokuapp.com/`` with the URL generated by Heroku.

The reverse proxy is hosted at ``https://secret-beyond-79263.herokuapp.com/``, it is saved in the ``REVERSEPROXY_URL`` constant. 

To form the base URL for all API calls in this application, add the ``REVERSEPROXY_URL`` to the ``APPLE_PODCAST_URL`` with the reverse proxy URL coming first. The result will look like this: ``https://secret-beyond-79263.herokuapp.com/https://itunes.apple.com/{{-other parts of URL-}}``. This means that any request is first sent to the reverse proxy, which attaches the header to the request before sending just the ``..https://itunes.apple.com/.....`` part to Apple's servers for processing.

The ``HOME_SCREEN_PODCAST_COLLECTION_IDS`` constant is just a list of podcast ``collectionId``s that will be displayed on the homepage.

In the last line, a new constant is created and exported - ``HOMESCREEN_API_URL``. The ``lookup?id=`` is part of the Apple API specification to search for something. You can find more details about the [Apple Podcast search API here](https://affiliate.itunes.apple.com/resources/documentation/itunes-store-web-service-search-api/).

Now that you have these requirements. You can move to the next task; the homepage, which is represented by the ``HomeScreen`` component. 

You need a child component called ``HomePodcastSection`` to display each category of podcasts on the homepage. First, create a new file for this component.

Create a new file called ``HomePodcastSection.jsx`` in the ``components`` folder.

Copy the code below into the ``HomePodcastSection.jsx``:

```
import React from 'react'

const HomePodcastSection = (props) => {

    const { header, podcasts, history } = props
    
    const handleClick = (collectionId) => {
        history.push(`podcast/${collectionId}`)
    }

    return (
        <>
            <div>
                <h1 className="text-left text-gray-100 text-2xl py-2 sm:pt-10 font-bold ">
                    {header}
                </h1>
            </div>
            <div className="flex flex-wrap flex-row">
                {
                    podcasts.map(podcast => (
                        <div
                            className="xl:w-1/5 md:w-1/3 sm:w-1/3 w-1/3 px-1 py-2"
                            key={podcast.collectionName}
                        >
                            <div onClick={() => handleClick(podcast.collectionId)}>
                                <div className="p-3 bg-gray-900 hover:bg-gray-800 cursor-pointer rounded-lg">
                                    <img className="rounded-lg w-full object-contain mb-1" 
                                      src={podcast.artworkUrl600} alt="content" />

                                    <div className="min-h-full h-14">
                                        <h2 className="text-left mt-2 home-screen-truncate-collection-name 
                                            text-sm text-white font-medium title-font">
                                            {podcast.collectionName}
                                        </h2>
                                        <p className="hidden md:block text-left pt-1 text-gray-400 text-xs">
                                          {podcast.artistName}
                                        </p>
                                        <p className="block md:hidden text-left pt-1 text-gray-400 text-xs">
                                          { podcast.artistName.length >= 30 
                                            ? 
                                            podcast.artistName.slice(0, 30) + '...' 
                                            : 
                                            podcast.artistName.slice(0, 30) }
                                        </p>
                                    </div>
                                </div>
                            </div>
                        </div>
                    ))
                }
            </div>
        </>
    )
}

export default HomePodcastSection
```

In the code in this file. Some data is passed to it as ``props``. This component is a child component of ``HomeScreen``, therefore ``HomeScreen`` passes some data to it to loop through and display.

In this code - ``const { header, podcasts, history } = props`` destructures ([read more about destructuring](https://javascript.info/destructuring-assignment#object-destructuring)) the ``props`` to get the properties inside. The ``header`` and ``podcasts`` represent the header to be displayed and a list of podcasts to be looped through and displayed, respectively.

The ``history`` object was also passed from the parent component ``HomeScreen``. It works with the router to change the URL of the app. In this case, a ``handleClick`` function that accepts a ``collectionId`` parameter, which is used to identify a podcast by Apple. And then pushes it to one of the routes defined earlier in ``MainSection.jsx``.

That is:

```
history.push(`podcast/${collectionId}`)
```

maps to this route

```
<Route exact path="/podcast/:collectionId" render={(props) => (<PodcastDetailsScreen {...props} />)} />
```

defined in the ``MainSection.jsx`` file. 

To recap the last point, the ``handleClick`` function when called passes a ``collectionId`` to the ``/podcast/`` route which prompts the view to change from the ``HomeScreen`` the user is currently viewing to the ``PodcastDetailsScreen``.


In the component return statement, which outputs to the browser, There are two significant segments. The part that displays the destructured ``header`` and the part that loops through the ``podcasts`` with ``map``.

Also in ``HomePodcastSection.jsx`` the ``home-screen-truncate-collection-name`` CSS class is a custom class to truncate any ``collectionName`` or podcast name that is too long and append ``...`` at the end. So that all podcast elements on the webpage will have equal height.

While

```
{ 
 podcast.artistName.length >= 30 
 ? 
 podcast.artistName.slice(0, 30) + '...' 
 : 
 podcast.artistName
}
```

checks the length of the ``artistName`` which is the description of the podcast. If it is greater than 30 characters, it trims it and appends ``...``. If it is not up to 30 characters, print it to the screen as-is.

With the ``HomePodcastSection`` complete. The ``HomeScreen`` component can now be updated.

Copy the code below. And overwrite the entire code in ``HomeScreen.jsx`` in the ``screens`` folder.

```go
import React, { useEffect, useState } from 'react'
import axios from 'axios'

import { HOMESCREEN_API_URL } from '../utils/consts'

import HomePodcastSection from '../components/HomePodcastSection'
import Loading from '../containers/Spinner/Loading'

function HomeScreen(props) {

  const [podcasts, setPodcasts] = useState()

  useEffect(() => {
    const fetchAPI = async () => {
      getPodcasts()
        .then(data => {
          setPodcasts(data)
        })
        .catch(err => console.log(err))
    };
    fetchAPI();
  }, []);

  let popularPodcasts, crimePodcasts, comedyPodcasts, politicsPodcasts

  if (podcasts) {
    // The alternative to this will be to create 4 separate API calls, which is wasteful
    popularPodcasts = podcasts.slice(0, 5)
    crimePodcasts = podcasts.slice(5, 10)
    comedyPodcasts = podcasts.slice(10, 15)
    politicsPodcasts = podcasts.slice(15, 20)
  }

  const { history } = props

  return (
    <>
      {
        podcasts ?
          <>
            <section className="container px-5 mx-auto">
              <HomePodcastSection header={'Popular podcasts'} podcasts={popularPodcasts} history={history} />
              <HomePodcastSection header={'Top crime podcasts'} podcasts={crimePodcasts} history={history} />
              <HomePodcastSection header={'Top comedy podcasts'} podcasts={comedyPodcasts} history={history} />
              <HomePodcastSection header={'Top politics podcasts'} podcasts={politicsPodcasts} history={history} />
            </section>
          </>
          :
          <>
            <Loading />
          </>
      }
    </>
  )
}

export default HomeScreen

const getPodcasts = async () => {
  const response = await axios.get(HOMESCREEN_API_URL)
  return response.data.results
}
```


You should be familiar with all the import statements, because I have talked about them previously. Except ``{ useEffect, useState }``, imported from React, at the top of the file. This are React Hooks that will help with API calls.

``useState`` is a Hook that allows local state in functional components.

``useEffect`` is a Hook that manages side-effects like fetch requests. It is used because it's bad practice to put side-effect code directly in your components.

This code

```
const [podcasts, setPodcasts] = useState()
```

creates a variable named ``podcasts`` and a function -``setPodcasts`` - to set the value of ``podcasts``. Since in our ``useState`` there's is nothing in the parenthesis. It means ``podcasts`` is initialized as undefined. If the code was ``useState('')`` or ``useState({})`` or ``useState({})``, that means ``podcast`` was initialized with an empty string or empty object or empty array respectively.

To change the value of ``podcasts`` in the application at any time, call ``setPodcasts(newValue)``. That is how ``useState`` Hook works. 

For example, to change the value of ``podcasts`` to the value ``const boy = 'Johnny'`` all you have to do is write ``setPodcasts(boy)`` or more clearly:

```
// 'useState example'
const [podcasts, setPodcasts] = useState()
console.log(podcasts) //-> output -> undefined
const names = ['John', 'Regina']
setPodcasts(names)
console.log(podcasts) //-> output -> ['John', 'Regina']
```

You can see that ``podcast`` wasn't assigned to ``names`` directly.

Moving on to the next piece of code:

```
useEffect(() => {
    const fetchAPI = async () => {
      getPodcasts()
        .then(data => {
          setPodcasts(data)
        })
        .catch(err => console.log(err))
    };
    fetchAPI();
}, []);

// Note that the code below is gotten from outside the component
// At the bottom of the 'HomeScreen' file
const getPodcasts = async () => {
  const response = await axios.get(HOMESCREEN_API_URL)
  return response.data.results
}
```
This code used the two hooks introduced earlier. The ``useEffect`` calls the ``getPodcasts`` function which calls the API using the URL constant - ``HOMESCREEN_API_URL`` -  we defined in ``const.js`` and imported here. The function returns a list of podcasts.

Then the ``setPodcasts(data)`` sets ``podcasts`` to the value - ``data`` that was returned from the ``getPodcasts`` function.

Next

```
let popularPodcasts, crimePodcasts, comedyPodcasts, politicsPodcasts

if (podcasts) {
  // The alternative to this will be to create 4 separate API calls, which is wasteful
  popularPodcasts = podcasts.slice(0, 5)
  crimePodcasts = podcasts.slice(5, 10)
  comedyPodcasts = podcasts.slice(10, 15)
  politicsPodcasts = podcasts.slice(15, 20)
}
```

The code above initializes the four variables.

And checks if ``podcasts`` is set. Without the if statement, when you try to slice the ``podcasts`` as seen above, will lead to an error because ``podcasts`` is initialized as undefined since you don't know whether the API call will be successful.

Next, the different ranges of ``podcasts`` are assigned to multiple variables. You can assign the different ranges of ``podcasts`` into different variables because you know what to expect from the API. Remember in the ``consts`` file we assigned ``HOMESCREEN_API_URL`` to a list of ``collectionId``s. The response from the API was organized in the same way we sent the request.

I chose this method to show different podcast categories because the homepage is not just a list of podcasts, but 4 podcasts in 4 categories. One alternative would have been to make multiple calls to the API for the different categories. But that method is an inefficient use of API resources.

```
const { history } = props
```

The code above destructures ``history`` from ``props``. 

Then  finally

```
<>
  {
    podcasts ?
      <>
        <section className="container px-5 mx-auto">
          <HomePodcastSection header={'Popular podcasts'} podcasts={popularPodcasts} history={history} />
          <HomePodcastSection header={'Top crime podcasts'} podcasts={crimePodcasts} history={history} />
          <HomePodcastSection header={'Top comedy podcasts'} podcasts={comedyPodcasts} history={history} />
          <HomePodcastSection header={'Top politics podcasts'} podcasts={politicsPodcasts} history={history} />
        </section>
      </>
      :
      <>
        <Loading />
      </>
  }
</>
```

For the code above.

``podcasts ?`` checks whether ``podcasts`` is set. Remember it was initially set to ``undefined``.

If ``podcasts`` is set, it displays the section container, which calls each of the children component ``HomePodcastSection`` and passes them props. The ``header`` to display, the ``podcasts`` to loop through and display each podcast, and the ``history``.

If ``podcast`` is not set, that is undefined. It displays the loading spinner with ``<loading />``.

### 💡 Checkpoint 2

If you have followed correctly. When you run ``yarn start``. You should see a homepage with a list of podcasts. But when you click on them, they don't show any details. But nothing breaks in the application.

> 🔗 If you encounter any errors, you can find the source code for this first part on [GitHub](https://github.com/avoajaugochukwu/podcast_player_tutorial/tree/master/Checkpoint_02). You can crosscheck with your work to find where you missed something.

> See a live version of the application so far. Click on 'Open Sandbox' to see the full code. Because the SideBar is not showing in the version below. Because the ```<iframe>``` is not wide enough to simulate a wider width screen.


{{< myiframe "https://codesandbox.io/embed/rppcheckpoint02-5gf3y?fontsize=14&hidenavigation=1&theme=dark&previewwindow=browser" >}}


### Other parts of the tutorial
📘 [How to Build a Podcast Player With React Js part 1](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api/)

📘 [How to Build a Podcast Player With React Js part 2](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-2/)

📘 [How to Build a Podcast Player With React Js part 3](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-3/)

📘 [How to Build a Podcast Player With React Js part 4](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-4/)

📘 [How to Build a Podcast Player With React Js part 5](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-5/)



