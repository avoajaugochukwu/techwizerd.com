---
title: "How to Build a Podcast Player With React Js Tailwind Css and Apple Podcast Api Part 5"
author: "Ugochukwu Avoaja"
date: 2021-06-24T14:13:12+01:00
categories:
  - React
  - TailwindCSS
  - API
  - React-Redux
  - Axios
  - React Hooks
draft: false
---

## Add footer player and build the search functionality

## Add footer player

The footer player will allow us to navigate through the application and keep the ability to pause or play any episode. When the application is first loaded, it will be hidden. And only appear when the user clicks play on any episode.

### Other parts of the tutorial
📘 [How to Build a Podcast Player With React Js part 1](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api/)

📘 [How to Build a Podcast Player With React Js part 2](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-2/)

📘 [How to Build a Podcast Player With React Js part 3](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-3/)

📘 [How to Build a Podcast Player With React Js part 4](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-4/)

📘 [How to Build a Podcast Player With React Js part 5](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-5/)

Overwrite the code in the ``FooterPlayer.jsx`` file in the ``component`` folder we created earlier.

```jsx
import React from 'react'
import { useSelector } from 'react-redux'
import EpisodeDescription from './EpisodeDescription'

import play_button from '../img/play_button.svg'
import pause_button from '../img/pause_button.svg'

const FooterPlayer = ({ handlePause, handlePlay }) => {
  const currentTrack = useSelector((state) => state.currentTrack)
  const { isPlaying, episode: { episodeUrl } } = currentTrack
  const { episode } = currentTrack

  
  return (
    <>
      {
        episodeUrl &&
        <>
          <div
            className="fixed left-0 bottom-0 min-w-full z-10"
            style={{ backgroundColor: "#1a1a1a" }}>
            <div className="relative h-full w-full flex">
              <div className="sm:w-1/3"></div>
              <div key={episode.trackId} className="flex flex-row md:p-4 p-1 ">
                <img
                  className=" rounded"
                  alt="User avatar"
                  src={episode.artworkUrl60} />
                {/*  */}
                
                  {
                    isPlaying === true && episodeUrl === episode.episodeUrl ?
                      <img
                        src={pause_button}
                        alt="button"
                        onClick={handlePause}
                        id={episode.trackId}
                        className="align-middle md:pl-3 pl-2"
                      />
                      :
                      <img
                        src={play_button}
                        alt="button"
                        onClick={(e) => handlePlay(episode)(e)}
                        id={episode.trackId}
                        className="align-middle md:pl-3 pl-2"
                      />
                  }
                
                {/*  */}
                <div className="text-gray-100 p-2 px-4 text-left">
                  <p className="">{episode.trackName}</p>
                  <EpisodeDescription description={episode.shortDescription} characterCount={100} readMore={false} />
                </div>
              </div>

            </div>
          </div>
        </>
      }

    </>

  )
}

export default FooterPlayer
```

This code obtains the ``episodeUrl`` from the ``redux store``, remember it was set to ``undefined`` when the store was initialized. That means the code here will only appear when ``episodeUrl`` is has a value other than ```undefined``. And this happens once the user plays any episode. And it never disappears again after that, even when the episode is paused.

We have talked about the all the other functionality here in other parts of the tutorial.


## Build the search functionality

The search page has a rather complex view flow.

First, when the user lands on the search page, they are shown genres of podcasts with the search button at the top. If the users find the genres they like on the home of the search page., they can click on it to see the top 100 podcasts in that genre. But if they do not find the podcast they like. They can click on the search input to type in their search term. When they start typing, a skeleton page is displayed to serve as a placeholder before the search results are returned. I will provide more explanation as I work through the search functionality.

To build the functionality, you have to do the following:

- Create ``TopPodcastBox.jsx`` component
- Create ``SearchTopGenres.jsx`` component
- Update ``GenreListScreen.jsx`` component
- Create ``SearchResultContainer.jsx`` component
- Create ``SearchResultSkeleton.jsx`` component
- Create ``SearchResults.jsx`` component
- Install ``use-debounce``
- Update ``earchScreen.jsx`` component

First, create two folders inside the ``containers`` folder called ``SearchResult`` and ``SearchTopGenres``. These folders will house new components.

### Create ``TopPodcastBox.jsx`` component

This component is a child component that will receive a ``genreId``, query the Apple API for the top 4 podcasts in that genre, and displays only the images of those 4 podcasts. Users can also click on the entire box and be taken to the ``GenreListScreen`` component if they want a bigger list of that genre.

Create a file called ``TopPodcastBox.jsx`` inside “SearchTopGenres`` folder.

Copy the code below into ``TopPodcastBox.js``

```jsx
import React, { useEffect, useState } from 'react'
import { useHistory } from 'react-router-dom'
import axios from 'axios'
import { BASE_URL } from '../../utils/consts'

const TopPodcastBox = (props) => {

  const history = useHistory()

  const { genreId, genre } = props

  const [podcasts, setPodcasts] = useState({})

  useEffect(() => {
    const fetchAPI = async () => {
      getPodcasts(genreId)
        .then(data => {
          setPodcasts(data)
        })
        .catch(err => console.log(err))
    };
    fetchAPI();
  }, [genreId]);

  const { results } = podcasts

  const handleClick = (genreId) => {
    history.push(`/genre/${genreId}`)
  }

  return (
    <>
      {
        results &&
        <>
          <div className="xl:w-1/4 md:w-1/2">
            <div className="m-4 cursor-pointer" onClick={() => handleClick(genreId)}>
              <div className="bg-gray-900 hover:bg-gray-800 pl-1 pt-2 pr-1 rounded-lg">
                <div className="flex flex-wrap">
                  {results.map(podcast => (

                    <div className="w-1/2" key={podcast.collectionId} >
                      <img src={podcast.artworkUrl600} alt="pod_art" className="w-full" />
                    </div>
                  ))}
                </div>

                <h3 className="text-gray-300 font-bold py-2">
                  {
                    genre ?
                      genre
                      :
                      results[0].genres[0] //get the genre of the first item in result
                  }
                </h3>
              </div>
            </div>
          </div>
        </>
      }
    </>
  )
}

export default TopPodcastBox

const getPodcasts = async (genreId = 1533) => {
  const response = await axios.get(`${BASE_URL}search?term=podcast&genreId=${genreId}&limit=4`)
  return response.data
}
```
### Create ``SearchTopGenres.jsx`` component

This file will be used to call the ``TopPodcastBox`` component in 10 places with different ``genreId``, so that it can display the box of 4 podcast per genre.

Create a file called ``SearchTopGenres.jsx`` inside “SearchTopGenres`` folder.

Copy the code below into ``SearchTopGenres.js``

```jsx
import React from 'react'

import TopPodcastBox from './TopPodcastBox'

const SearchTopGenres = () => {
  return (
    <section className="text-gray-600 body-font">
      <h1 className="text-left text-gray-100 text-2xl py-2 sm:pt-10 font-bold px-5">
        Top genres
      </h1>
      <div className="container px-5 py-5 mx-auto">

        <div className="flex flex-wrap -m-4">
          <TopPodcastBox genreId="1402" /> {/* Design */}
          <TopPodcastBox genreId="1488" genre="Crime" /> {/* Crime */}
          <TopPodcastBox genreId="1303" /> {/* Comedy */}
          <TopPodcastBox genreId="1321" /> {/* Business */}
          <TopPodcastBox genreId="1314" /> {/* Religion */}
          <TopPodcastBox genreId="1304" /> {/* Self-Improvement */}
          <TopPodcastBox genreId="1527" /> {/* Politics */}
          <TopPodcastBox genreId="1324" genre="Society & Culture" /> {/* Society */}
          <TopPodcastBox genreId="1533" genre="Science" /> {/* Science */}
          <TopPodcastBox genreId="1487" /> {/* History */}
        </div>
      </div>
    </section>
  )

}

export default SearchTopGenres
```

### Update ``GenreListScreen.jsx`` component

Update the ``GenreListScreen.jsx`` file in the ``screens`` folder inside ``src`` older.

Overwrite the code in ``GenreListScreen.jsx``with the code below:

```jsx
import React, { useEffect, useState } from 'react'
import { useHistory } from 'react-router-dom'
import axios from 'axios'

import { BASE_URL } from '../utils/consts'
import Loading from '../containers/Spinner/Loading'

const GenreListScreen = (props) => {
  const { match: { params: { genreId } } } = props

  const [podcasts, setPodcasts] = useState({})

  useEffect(() => {
    const fetchAPI = async () => {
      getPodcasts(genreId)
        .then(data => {
          setPodcasts(data)
        })
        .catch(err => console.log(err))
    };
    fetchAPI();
  }, [genreId]);

  const { results } = podcasts

  const history = useHistory()
  const handleClick = (collectionId) => {
    history.push(`../podcast/${collectionId}`)
  }

  return (
    <>
      {
        results ?
        <>
          <section className="container px-5 mx-auto">
            <h1 className="text-left text-gray-100 text-2xl py-2 sm:pt-10 font-bold pb-5 ">{results[0].genres[0]}</h1>
            
              <div className="flex flex-wrap">
                {
                  results.map(podcast => (
                    <div
                      className="xl:w-1/5 md:w-1/2"
                      key={podcast.collectionName}

                    >
                      <div className="p-1">
                        <div className="p-3 bg-gray-900 hover:bg-gray-800 cursor-pointer rounded-lg" onClick={() => handleClick(podcast.collectionId)}>
                          <img className="rounded-lg w-full object-contain mb-1" src={podcast.artworkUrl600} alt="content" />

                          <div className="min-h-full h-14">
                            <h2 className="text-left mt-2 home-screen-truncate-collection-name text-sm text-white font-medium title-font">
                              {podcast.collectionName}
                            </h2>
                            <p className="text-left text-gray-400 text-xs">
                              {podcast.artistName}
                            </p>
                          </div>
                        </div>
                      </div>
                    </div>
                  ))
                }
              </div>
            
          </section>
        </>
        :
        <Loading />
      }

    </>
  )
}

export default GenreListScreen

const getPodcasts = async (genreId) => {
  const response = await axios.get(`${BASE_URL}search?term=podcast&genreId=${genreId}&limit=200`)
  return response.data
}
```

This file gets the ``genreId`` from the URL as a prop. Then queries the Apple API to get the top 200 podcasts in that genre. And the user can click on any of these podcasts to see their details served by the ``PodcastDetailsScreen`` component discussed in prior parts of these tutorials.

### Create ``SearchResults.jsx`` component

This component will receive the results of searching the API as props and display them.

Create a file called ``SearchResults.jsx`` in ``src/containers/SearchResult``.

Copy the code below into ``SearchResults.jsx``.

```jsx
import React from 'react'
import { getGenreColor, getGenreGradientColor } from '../../utils/genreColor'
import EpisodeDescription from '../../components/EpisodeDescription'

const SearchResults = ({ podcastResults, episodeResults, activeSearchText, handleClick }) => {
  
  const topResult = podcastResults[0]
  const episodes = episodeResults
  const otherPodcasts = podcastResults.slice(1, topResult.length)

  return (
    <>
      <div className="flex md:flex-row flex-col md:space-x-4">
        <div className="md:w-2/5 w-full p-4">
          <h1 className="text-left text-gray-100 text-2xl font-bold pb-2">Top result</h1>
          <div className={` bg-gradient-to-b ${getGenreGradientColor(topResult.genres[0])} bg-gray-900 p-5 rounded-lg hover:bg-gray-800 cursor-pointer`} onClick={() => handleClick(topResult.collectionId)}>

            <img
              className="object-cover w-32 h-32 rounded-lg"
              alt="User avatar"
              src={topResult.artworkUrl600} />

            <div className="pt-5 text-gray-300 text-left">
              <h3 className="text-xl text-gray-200">{topResult.trackName}</h3>
              <p>{topResult.artistName}</p>
              <div className="text-left pt-4">
                {topResult.genres.map(genre => (
                  <span
                    className={`text-xs text-white p-0.5 mr-1 rounded ${getGenreColor(genre)}`} key={genre}
                  >
                    {genre}
                  </span>
                ))}
              </div>
            </div>
          </div>

        </div>
        {/*  */}
        <div className="md:w-3/5 w-full p-4">
          <h1 className="text-left text-gray-100 text-2xl font-bold pb-2">Episodes</h1>
          {
            episodes &&
            episodes.map(item => (
              <div key={item.trackId} className="flex flex-row bg-gray-900 mb-2 hover:bg-gray-800">
                <img
                  className="object-cover rounded"
                  alt="User avatar"
                  src={item.artworkUrl60} />
                <div className="text-gray-100 p-2 px-4 text-left">
                  <p className="">{item.trackName}</p>
                  <EpisodeDescription description={item.shortDescription} characterCount={150} readMore={true} />
                </div>
              </div>
            ))
          }
        </div>
      </div>

      <h1 className="text-left text-gray-100 text-2xl font-bold pb-5">Podcast that match '{activeSearchText}'</h1>
      <div className="flex flex-wrap flex-row">
        {otherPodcasts.map(podcast => (

          <div className="xl:w-1/5 md:w-1/3 sm:w-1/3 w-1/3 p-1" key={podcast.collectionId} >
            <div onClick={() => handleClick(podcast.collectionId)}>
              <div className="p-3 bg-gray-900 hover:bg-gray-800 cursor-pointer rounded-lg">

                <img className="rounded-lg w-full object-contain mb-1" src={podcast.artworkUrl600} alt="content" />
                
                <div className="min-h-full h-14">

                  <h2 className="text-left mt-2 home-screen-truncate-collection-name text-sm text-white font-medium title-font">
                    {podcast.collectionName}
                  </h2>
                  <p className="text-left pt-1 text-gray-400 text-xs">
                    {podcast.artistName}
                  </p>

                </div>
              </div>
            </div>

          </div>

        ))}
      </div>
    </>
  )
}

export default SearchResults
```


### Create ``SearchResultSkeleton.jsx`` component

This component doesn't show any data, all it does it display a placeholder while the API gathers the search result. It works like a skeleton. See more about them here [ant design skeleton](https://ant.design/components/skeleton/).

Create a file called ``SearchResultSkeleton.jsx`` in ``src/containers/SearchResult``.

Copy the code below into ``SearchResultSkeleton.jsx``.

```jsx
import React from 'react'

const SearchResultSkeleton = () => {
  return (
    <div className="flex flex-row space-x-4">
      <div className="w-2/5 p-4">
        <h1 className="text-left text-gray-100 text-2xl font-bold pb-5">Top result</h1>
        <div className="bg-gray-900 p-5">
          <div className="h-32 w-32 rounded-full bg-gray-800"></div>
          <div className="pt-5">
            <div className="h-7 pb-3 w-2/3 bg-gray-800"></div>
            <div className="h-2"></div>
            <div className="h-5 w-2/5 bg-gray-800"></div>
          </div>
        </div>

      </div>
      <div className="w-3/5 p-4">
        <h1 className="text-left text-gray-100 text-2xl font-bold pb-5">Podcast</h1>
        <div className="h-10 w-full bg-gray-800"></div>
        <div className="h-3"></div>
        <div className="h-10 w-full bg-gray-800"></div>
        <div className="h-3"></div>
        <div className="h-10 w-full bg-gray-800"></div>
        <div className="h-3"></div>
        <div className="h-10 w-full bg-gray-800"></div>
      </div>
    </div>
  )
}

export default SearchResultSkeleton
```

### Create ``SearchResultContainer.jsx`` component

This component handles which component to show between the ``SearchResults`` and ``SearchResultSkeleton`` component depending on whether the API has returned results for the usrs search term.

Create a file called ``SearchResultContainer.jsx`` in ``src/containers/SearchResult``.

Copy the code below into ``SearchResultContainer.jsx``.

```jsx
import React from 'react'

import SearchResults from './SearchResults'
import SearchResultSkeleton from './SearchResultSkeleton'

const SearchResultContainer = ({ podcastResultCount, podcastResults, episodeResults, activeSearchText, handleClick }) => {
  return (
    <div className="min-h-screen w-full">
      {
        podcastResultCount > 0 ?
        <SearchResults podcastResults={podcastResults} episodeResults={episodeResults} activeSearchText={activeSearchText} handleClick={handleClick} />
        :
        <SearchResultSkeleton />
      }
    </div>
  )
}

export default SearchResultContainer
```

### Install use-debounce

Run:

```jsx
yarn add use-debounce
```

``Debounce`` delays sending an API call while the user is typing to reduce the number of API calls. For more on debounce please see [JavaScript debounce example](https://www.freecodecamp.org/news/javascript-debounce-example/). It will be used in the ``SearchScreen`` component.

### Update "SearchScreen.jsx`` component

Now it is time to update the ``SearchScreen`` component.

Copy the code below into ``SearchScreen.jsx`` in ``src/screens``.

```jsx
import React, { useState, useEffect } from 'react'
import { useDebounce } from "use-debounce";
import axios from 'axios';

import SearchResultContainer from '../containers/SearchResult/SearchResultContainer'
import SearchTopGenres from '../containers/SearchTopGenres/SearchTopGenres'

import right_chevron_circle from '../img/chevron_circle_right_icon.svg'
import left_chevron_circle from '../img/chevron_circle_left_icon.svg'
import search_icon_black from '../img/search_icon_black.svg'
import cancel_close_delete_icon from '../img/cancel_close_delete_icon.svg'

import { BASE_URL } from '../utils/consts'

const initalText = " ";

function SearchScreen(props) {
  const [searchText, setSearchText] = useState(initalText)
  const [searchPodcastResults, setSearchPodcastResults] = useState({})
  const [searchEpisodeResults, setSearchEpisodeResults] = useState({})
  const [debouncedText] = useDebounce(searchText, 500)
  const [activeSearchText, setActiveSearchText] = useState('')

  useEffect(() => {
    const source = axios.CancelToken.source()

    if (debouncedText) {
      getPodcasts(debouncedText)
        .then(data => {
          setSearchPodcastResults(data)
        })
        .catch(err => console.log(err))

      getEpisodes(debouncedText)
        .then(data => {
          setSearchEpisodeResults(data)
        })
        .catch(err => console.log(err))
    }
    else {
      setSearchPodcastResults({})
      setSearchEpisodeResults({})
    }

    return () => {
      source.cancel(
        "Canceled because of component unmounted or debounce Text changed"
      )
    }
  }, [debouncedText, searchText])

  const { history } = props
  const handleClick = (collectionId) => {
    history.push(`podcast/${collectionId}`)
  }

  const handleBack = () => {
    history.goBack()
  }

  const handleSearchCancel = (e) => {
    setActiveSearchText('')
    setSearchText('')
    e.target.value = ''
  }

  const { resultCount: podcastResultCount, results: podcastResults } = searchPodcastResults
  const { results: episodeResults } = searchEpisodeResults

  return (
    <section>
      <div className="container px-5 mx-auto">
        <div className="flex flex-row space-x-5 pt-3 ">

          <img
            src={left_chevron_circle}
            className="w-8 my-3 rounded-full bg-gray-400 hover:bg-gray-600 cursor-pointer"
            alt="left_chevron"
            onClick={() => handleBack()} />

          <img
            src={right_chevron_circle}
            className="w-8 my-3 rounded-full bg-gray-400 cursor-not-allowed hover:bg-gray-600 cursor-pointer"
            alt="right_chevron" />

          <div className="relative w-full md:w-4/12">
            <span className="absolute inset-y-0 left-0 flex items-center pl-3">
              <img src={search_icon_black} alt="search_icon_black" />
            </span>
            <input
              type="text"
              className="w-full py-3 pl-10 pr-4 text-gray-900 bg-white border border-gray-300 rounded-full"
              placeholder="Podcast"
              aria-label="Podcast"
              value={searchText}
              onChange={(e) => {
                setSearchText(e.target.value)
                setActiveSearchText(e.target.value)
              }} />
            {
              activeSearchText && 
              <span className="absolute inset-y-0 right-0 flex items-center pr-2 cursor-pointer">
                <img 
                  src={cancel_close_delete_icon} 
                  alt="cancel_close_delete_icon"
                  onClick={(e) => handleSearchCancel(e)} />
              </span>
            }
          </div>
        </div>

        {
          activeSearchText !== ''
            ?
            <SearchResultContainer
              podcastResultCount={podcastResultCount}
              podcastResults={podcastResults}

              episodeResults={episodeResults}
              activeSearchText={activeSearchText}
              handleClick={handleClick} />
            :
            <SearchTopGenres />
        }
      </div>

    </section>
  )
}

const getPodcasts = async (text) => {
  const response = await axios.get(`${BASE_URL}search?term=${text}&entity=podcast`)
  return response.data
}

const getEpisodes = async (text) => {
  const response = await axios.get(`${BASE_URL}search?term=${text}&entity=podcastEpisode&limit=4`)
  return response.data
}

export default SearchScreen
```

This component handles the entire Search Page and even the ``GenreListScreen`` component. It renders the ``GenreListScreen`` component when ``activeSearchText !== ''`` is false.

Most of the code in this component have been discussed in other parts of this tutorial. But we have not talked about ``debounce``.

``Debounce`` delays sending an API call while the user is typing to reduce the number of API calls. For more on debounce please see [JavaScript debounce example](https://www.freecodecamp.org/news/javascript-debounce-example/).


### 💡 Checkpoint 5


> 🔗 You can also find all the code for the fourth [checkpoint here](https://github.com/avoajaugochukwu/podcast_player_tutorial/tree/master/Checkpoint_05). Copy the parts that are missing from your project.

> See a live version of the application so far. Click on 'Open Sandbox' to see the full code. Because the SideBar is not showing in the version below. Because the ```<iframe>``` is not wide enough to simulate a wider width screen.

{{< myiframe "https://codesandbox.io/embed/rppcheckpoint05-3t1s2?fontsize=14&hidenavigation=1&theme=dark&previewwindow=browser" >}}

### Other parts of the tutorial
📘 [How to Build a Podcast Player With React Js part 1](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api/)

📘 [How to Build a Podcast Player With React Js part 2](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-2/)

📘 [How to Build a Podcast Player With React Js part 3](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-3/)

📘 [How to Build a Podcast Player With React Js part 4](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-4/)

📘 [How to Build a Podcast Player With React Js part 5](../how-to-build-a-podcast-player-with-react-js-tailwind-css-and-apple-podcast-api-part-5/)