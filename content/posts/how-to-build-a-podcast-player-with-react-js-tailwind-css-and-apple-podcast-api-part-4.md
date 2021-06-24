---
title: "How to Build a Podcast Player With React Js Tailwind Css and Apple Podcast Api Part 4"
author: "Ugochukwu Avoaja"
date: 2021-06-24T13:08:05+01:00
categories:
  - React
  - TailwindCSS
  - API
  - React-Redux
  - Axios
  - React Hooks
draft: true
---

## Building the Podcast Details Screen

In this part, we would go back to the ``PodcastDetailsScreen`` component to put the finishing touches on it. In the first part, we created the ``PodcastDetailsScreen`` and added a placeholder text to test it.

The ``PodcastDetailsScreen`` component displays the podcast details screen, which has more details about podcasts and their episodes with buttons for users to play/pause each episode. 

The ``PodcastDetailsScreen`` component has a lot of children components... and even grandchildren components. But the technology behind it has been discussed in earlier parts of this tutorial. So I won't explain them in detail here.

To implement the podcast details screen, do the following

- Install ``react-infinite-scroll-component`` and ``moment``
- Create two functions to handle colors for podcast categories
- Update ``MainSection`` component
- Create ``ReleaseDate`` component
- Create ``EpisodeDuration`` component
- Create ``EpisodeDescription`` component
- Create ``EpisodeDescription`` component
- Create ``PodcastDetailsEpisodes`` component
- Create ``PodcastDetailsBody`` component
- Create ``PodcastDetailsHeader`` component
- Create a ``LoadMore`` icon
- Update ``PodcastDetailsScreen`` component

### Install ``react-infinite-scroll-component`` and ``moment``

Run:

```
yarn add react-infinite-scroll-component moment
```

``react-infinite-scroll-component`` is a module that handles lazy loading. 

Some podcasts have more than 500 episodes. So when a user visits the podcast details page, at first, only ten episodes will be displayed. Then, as they scroll down, more episodes will appear. That is where ``react-infinite-scroll-component`` comes in.

``moment`` is a module that handles the formatting of date and time elegantly. It will be used to get the podcast release date format.

### Create two functions to handle colors for podcast categories

In the ``utils``  folder created earlier, create a file called ``genreColor.js``

Copy the code below into ``genreColors.js``:

```jsx
export const getGenreColor = (genre) => {
  // Return specific color for each genre
  switch (genre) {
    case 'Entrepreneurship':
      return 'bg-pink-500'
    case 'Podcasts':
      return 'bg-purple-500'
    case 'Business':
      return 'bg-indigo-500'
    case 'News':
      return 'bg-blue-500'
    case 'History':
      return 'bg-green-500'
    case 'Society & Culture':
      return 'bg-red-500'
    case 'Religion & Spirituality':
      return 'bg-yellow-500'
    case 'True Crime':
      return 'bg-red-800'
    case 'Design':
      return 'bg-blue-500'

    default:
      return 'bg-gray-500'
  }
}

export const getGenreGradientColor = (genre) => {
  // Return specific color for each genre
  switch (genre) {
    case 'Entrepreneurship':
      return 'from-pink-900'
    case 'Podcasts':
      return 'from-purple-900'
    case 'Business':
      return 'from-indigo-900'
    case 'News':
      return 'from-blue-900'
    case 'History':
      return 'from-green-900'
    case 'Society & Culture':
      return 'from-red-900'
    case 'Religion & Spirituality':
      return 'from-yellow-900'
    case 'True Crime':
      return 'from-red-900'
    case 'Design':
      return 'from-blue-900'

    default:
      return 'from-blue-500'
  }
}
```

This file has two functions:

``getGenreColor`` takes the genre of a podcast and returns a color for the podcast genre pills. If the genre doesn't match any of the cases, it returns the default.

``getGenreGradientColor`` does the same, although it returns a background color in a gradient format used to make the top section of each podcast details screen look different.

### Update ``MainSection`` component

Overwrite the code in ``MainSection.jsx`` with the code below:

```jsx
import React from 'react'
import {
  Switch,
  Route
} from 'react-router-dom'

import HomeScreen from '../screens/HomeScreen'
import SearchScreen from '../screens/SearchScreen'
import PodcastDetailsScreen from '../screens/PodcastDetailsScreen'
import GenreListScreen from '../screens/GenreListScreen'

const MainSection = ({ handlePause, handlePlay }) => {
  return (
    <>
      <main className=" player-section pl-0 md:pl-60  min-h-screen min-w-full">
        <Switch>
          <Route exact path="/" component={HomeScreen}></Route>
          <Route exact path="/Search" component={SearchScreen}></Route>
          <Route exact path="/podcast/:collectionId" render={(props) => (<PodcastDetailsScreen {...props} handlePause={handlePause} handlePlay={handlePlay} />)} />
          <Route exact path="/genre/:genreId" component={GenreListScreen}></Route>
        </Switch>
      </main>
    </>
  )
}

export default MainSection
```

Updated the route for ``PodcastDetailsScreen`` so that it can pass the functions down to its children's components.

### Create ``ReleaseDate`` component

Create a file in the ``components`` folder called ``ReleaseDate.jsx``.

Copy the following code into ``ReleaseDate.jsx``

```jsx
import React from 'react'
import moment  from 'moment'

const ReleaseDate = (props) => {
    const { date } = props

    const date_string = moment(date).format('DD MMM YYYY')
    return (
        <div>
          <span className="text-xs pl-3">{ date_string }</span>
        </div>
    )
}

export default ReleaseDate
```

This component uses ``moment`` to get the date of a podcast episode release in our desired format.

### Create ``EpisodeDuration`` component

Create a file in the ``components`` folder called ``EpisodeDuration.jsx``.

Copy the following code into ``EpisodeDuration.jsx``:

```jsx
import React from 'react'

const EpisodeDuration = ({ duration }) => {

    let new_duration = ''

    if (duration) {
        new_duration = Math.floor((duration / 1000) / 60) + " min"
    }
    
    return (
        <div>
            {
                new_duration ?
                <>
                    <span className="px-1">&bull;</span>
                    <span className="text-xs">
                        {new_duration}
                    </span>
                </>
                :
                <></>
            }
            
        </div>
    )
}

export default EpisodeDuration
```

This component receives the duration of a podcast episode as a destructured property of the ``prop`` then returns the duration in minutes if it is defined. Else it returns nothing.

### Create ``EpisodeDescription`` component

Create a file in the ``components`` folder called ``EpisodeDescription.jsx``.

Copy the following code into ``EpisodeDescription.jsx``:

```jsx
import React, { useState } from 'react'

const NewLine = (text) => {
  return <div style={{ whiteSpace: "pre-wrap" }}>{text}</div>
}

const EpisodeDescription = ({ description, characterCount, readMore }) => {

  const [isReadMore, setIsReadMore] = useState(true)

  const toggleReadMore = () => {
    setIsReadMore(!isReadMore)
  }

  return (
    <>
      <div className="text-xs">
        {
          description ?
            <>
              {isReadMore ? description.slice(0, characterCount) + '...' : NewLine(description)}
                &nbsp;&nbsp;
              {
                readMore &&
                <span onClick={toggleReadMore} className="font-black underline cursor-pointer text-white">
                  {isReadMore ? <span>read more</span> : <span className="block text-right -mt-4 ">read less</span>}
                </span>
              }
            </>
            :
            <span className="italic text-gray-600">No description</span>
        }

      </div>

    </>
  )

}

export default EpisodeDescription
```

This component is reusable. It accepts the ``description``, ``characterCount``, ``readMore`` which is a boolean to determine whether some text should be hidden and show a 'read more' button when truncated.

It cuts the ``description`` according to ``characterCount`` specified. Then, it uses the ``NewLine`` to ensure that React respects the ``HTML`` tags within the ``description`` because the Apple API description includes ``HTML`` tags.

And it returns 'No description' if description is undefined.

### Create ``PodcastDetailsEpisodes`` component

Create a file in the ``components`` folder called ``PodcastDetailsEpisodes.jsx``.

Copy the following code into ``PodcastDetailsEpisodes.jsx``:

```jsx
import React from 'react'
import { useSelector } from 'react-redux'
import ReleaseDate from './ReleaseDate'
import EpisodeDuration from './EpisodeDuration'
import EpisodeDescription from './EpisodeDescription'

import play_button from '../img/play_button.svg'
import pause_button from '../img/pause_button.svg'

const PodcastDetailsEpisodes = ({ episodes, handlePause, handlePlay }) => {
  const currentTrack = useSelector((state) => state.currentTrack)
  const { isPlaying, episode: { episodeUrl } } = currentTrack

  return (
    <>
      {
        episodes &&
        <>
          {episodes.map(episode => (
            <div
              key={episode.releaseDate}
              className="my-3"
            >
              <div
                className="flex w-full max-w-full mx-auto overflow-hidden 
                            hover:bg-gray-900 border-b border-gray-800
                             shadow-md px-4  py-6">
                <img
                  className="object-cover w-32 h-32 rounded pt-5 md:pt-1"
                  alt="User avatar"
                  src={episode.artworkUrl160} />

                <div className="flex items-center">
                  <div className="pl-3">
                    <div className=" dark:text-gray-200">
                      <h3 className="font-medium pb-2 text-white">{episode.trackName}</h3>
                      <EpisodeDescription description={episode.description} characterCount={150} readMore={true} />
                      <div className="pt-3 flex ">
                        {
                          isPlaying === true && episodeUrl === episode.episodeUrl ?
                            <img
                              src={pause_button}
                              alt="button"
                              onClick={handlePause}
                            />
                            :
                            <img
                              src={play_button}
                              alt="button"
                              onClick={(e) => handlePlay(episode)(e)}
                            />
                        }
                        <ReleaseDate date={episode.releaseDate} />
                        <EpisodeDuration duration={episode.trackTimeMillis} />
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          ))

          }
        </>
      }
    </>
  )
}

export default PodcastDetailsEpisodes
```

This component brings together some of the smaller components that have been defined above. Like the ``ReleaseDate, ``EpisodeDuration`` and ``EpisodeDescription``. It is the component that holds data of each episode on the podcast details page.

It receives 3 properties ``episodes``, ``handlePause`` and ``handlePlay``. It also subscribes to the Redux store because it implements the play/pause functionality. So it accesses ``isPlaying`` from the store. It has two images for buttons that users can click to play or pause audio. The images/buttons change according to the value of ``isPlaying`` whether it's ``true/false``.

### Create ``PodcastDetailsBody`` component

Create a file in the ``components`` folder called ``PodcastDetailsBody.jsx``.

Copy the following code into ``PodcastDetailsBody.jsx``:

```jsx
import React, { useState } from 'react'
import InfiniteScroll from 'react-infinite-scroll-component'

import PodcastDetailsEpisodes from './PodcastDetailsEpisodes'
import LoadMore from '../containers/Spinner/LoadMore'

const PodcastDetailsBody = ({ episodes, handlePause, handlePlay }) => {

  const [count, setCount] = useState({ prev: 1, next: 10 })
  const [hasMore, setHasMore] = useState(true);
  const [current, setCurrent] = useState(episodes.slice(count.prev, count.next))

  const getMoreData = () => {
    if (current.length  + 10 >= episodes.length) {
      setHasMore(false);
      return;
    }
    setTimeout(() => {
      setCurrent(current.concat(episodes.slice(count.prev + 10, count.next + 10)))
    }, 1000)
    setCount((prevState) => ({ prev: prevState.prev + 10, next: prevState.next + 10 }))
  }

  return (
    <div className="flex max-w-full md:px-8 px-0 text-left text-gray-300">
      <div className="md:w-7/12 w-full">
        <InfiniteScroll
          dataLength={current.length}
          next={getMoreData}
          hasMore={hasMore}
          loader={<LoadMore />}
        >
          <PodcastDetailsEpisodes episodes={current} handlePause={handlePause} handlePlay={handlePlay} />
        </InfiniteScroll>
      </div>

      <div className="w-5/12 h-32 pl-12 md:block hidden">
        <h2 className="font-semibold text-2xl">About</h2>
        <p className="text-1xl font-thin pt-4">
          Lorem ipsum dolor sit amet consectetur adipisicing elit. Consequuntur corrupti cupiditate quibusdam magnam ipsum.
          Eveniet numquam sit, dignissimos rerum nihil quod mollitia, natus tempora ullam, esse dolores error repellat nisi.
          </p>
      </div>
    </div>
  )
}

export default PodcastDetailsBody
```

# Explain more of the top Component
# Explain more of the top Component
# Explain more of the top Component

### Create ``PodcastDetailsHeader`` component

Create a file in the ``components`` folder called ``PodcastDetailsHeader.jsx``.

Copy the following code into ``PodcastDetailsHeader.jsx``:

```jsx
import React from 'react'
import { getGenreColor, getGenreGradientColor } from '../utils/genreColor'

const PodcastDetailsHeader = ({ podcastDetails }) => {
  return (
    <div className={` bg-gradient-to-b ${getGenreGradientColor(podcastDetails.genres[0])}`}>

      <div className="md:px-8 md:pt-28 md:py-6 p-3 pb-6 flex flex-row flex-grow">

        <div className="md:w-1/5 w-2/6">
          <img src={podcastDetails.artworkUrl600} className="rounded-lg" alt={podcastDetails.artworkUrl600} />
        </div>

        <div className="text-left md:pl-4 pl-2 shadow font-semibold hover:shadow-md">
          <h1 className="text-left text-gray-100 text-3xl md:text-5xl md:pt-5 font-black ">
            {podcastDetails.collectionName}
          </h1>
          <p className="text-left text-gray-100 py-1 pb-1 text-sm font-thin">
            {podcastDetails.artistName}
          </p>
          <div className="text-left p-1">

            {podcastDetails.genres.map(genre => (
              <span
                className={`text-xs text-white p-0.5 mr-1 rounded font-thin ${getGenreColor(genre)}`} 
                key={genre}
              >
                {genre}
              </span>
            ))}

          </div>

          <p className="text-left text-xs text-gray-300 py-1">{podcastDetails.trackCount} Episodes</p>
          
        </div>

      </div>
      <div className="px-8 flex flex-row">
              {/* <ListenButton /> the non functional red button */}
      </div>
    </div>
  )
}

export default PodcastDetailsHeader
```

This component is the header of the podcast details screen. It displays relevant details about the podcast. Like the name, description, and genres. It also implements the background color gradient according to the main podcast genre.

### Create a LoadMore icon

We need a loading icon to support our infinite scrolling. It would be an indicator to let the user know that more podcast is loading. And when there is no more podcast to load. This load more icon will disappear.

Visit [this site](https://loading.io/css/) to get loading elements. You can choose anyone.

In the  ``Spinner`` within the ``containers`` folder. Then create two files in the ``Spinner`` folder called ``LoadMore.css`` and ``LoadingMore.jsx``.

You can copy mine.

> 🔗 You can [visit GitHub](https://github.com/avoajaugochukwu/podcast_player_tutorial/tree/master/Checkpoint_04/src/containers/Spinner) and copy LoadMore.css and LoadMore.jsx.

### Update ``PodcastDetailsScreen`` component

Now we can update the ``PodcastDetailsScreen`` component, that is in the ``screens`` folder.

Copy the code below and overwrite the ``PodcastDetailsScreen`` file:

```jsx
import React, { useState, useEffect } from 'react'
import axios from 'axios'

import PodcastDetailsBody from '../components/PodcastDetailsBody'
import PodcastDetailsHeader from '../components/PodcastDetailsHeader'

import Loading from '../containers/Spinner/Loading'

import { BASE_URL } from '../utils/consts'

function PodcastDetailsScreen({ match: { params: { collectionId } }, handlePause, handlePlay }) {
  const [podcast, setPodcast] = useState({})

  useEffect(() => {
    const fetchAPI = async () => {
      getPodcast(collectionId)
        .then(data => {
          setPodcast(data)
        })
        .catch(err => console.log(err))
    };
    fetchAPI();
  }, [collectionId]);

  const { results } = podcast

  let podcastList, podcastDetails

  if (results) {
    podcastDetails = results[0]
    podcastList = results.slice(1, results.length)
  }

  return (
    <section>
      {
        results ?
          <>
            <PodcastDetailsHeader podcastDetails={podcastDetails} />
            <PodcastDetailsBody episodes={podcastList} handlePause={handlePause} handlePlay={handlePlay} />
          </>
          :
          <>
            <Loading />
          </>
      }
    </section>
  )
}

const getPodcast = async (collectionId) => {
  const response = await axios.get(`${BASE_URL}lookup?id=${collectionId}&country=US&media=podcast&entity=podcastEpisode&limit=400`)
  return response.data
}

export default PodcastDetailsScreen
```

Most of the technology in this file has been covered in other parts of this tutorial. Like the loading, API call, ``BASE_URL``, and using React Hooks (``useState`` and ``useEffects``).

I will only discuss technology that you are seeing for the first time.

Like what is passed to the ``PodcastDetailsScreen`` component.

```jsx
function PodcastDetailsScreen({ match: { params: { collectionId } }, handlePause, handlePlay })
```

First the ``props`` is destructured to obtain the items inside - ``handlePause``, ``handlePlay`` and ``collectionId``.

``handlePause`` and ``handlePlay`` are functions to be passed down to ``PodcastDetailsBody`` component.

``collectionId`` is destructured from ``params`` which is destructured from ``match``. ``collectionId`` is a portion of the URL that represents the id of a podcast. This route was defined in the URL in the ``MainSection.jsx`` file.

```jsx
<Route exact path="/podcast/:collectionId" 
	render={(props) => (
		<PodcastDetailsScreen {...props} 
			handlePause={handlePause} 
			handlePlay={handlePlay} />
)} /> 
```

The ``collectionId`` will be used to query the API to get details about the podcast in the ``getPodcast`` function that is at the bottom of ``PodcastDetailsScreen.jsx``.

### 💡 Checkpoint 4


> 🔗 You can also find all the code for the fourth [checkpoint here](https://github.com/avoajaugochukwu/podcast_player_tutorial/tree/master/Checkpoint_04). Copy the parts that are missing from your project.

> See a live version of the application so far. Click on 'Open Sandbox' to see the full code. Because the SideBar is not showing in the version below. Because the ```<iframe>``` is not wide enough to simulate a widers width screen

{{< myiframe "https://codesandbox.io/embed/rppcheckpoint04-b9cyt?fontsize=14&hidenavigation=1&theme=dark&previewwindow=browser" >}}







