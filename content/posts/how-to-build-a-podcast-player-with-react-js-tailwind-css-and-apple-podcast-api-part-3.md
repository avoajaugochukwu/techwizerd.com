---
title: "How to Build a Podcast Player With React Js Tailwind Css and Apple Podcast Api Part 3"
author: "Ugochukwu Avoaja"
date: 2021-06-23T16:29:49+01:00
categories:
  - React
  - TailwindCSS
  - API
  - React-Redux
  - Axios
  - React Hooks
draft: true
---

## Setting up the React-Redux store to handle playing audio

In this section, we would implement the react-redux state management that handles our currently playing podcast. And allows users to visit other pages without stopping the music. 

This means that the playing audio will be attached to a component that is rendered throughout the entire application. The ``App`` component is the best component that fits this bill in our application. Because all other components are its children in one way other.

### A little about React-Redux

[Redux](https://redux.js.org/introduction/getting-started) is a predictable state container for JavaScript apps.

[React Redux](https://react-redux.js.org/introduction/getting-started) is the official React UI bindings layer for Redux. It lets your React components read data from a Redux store and dispatch actions to the store to update the state.

React-Redux helps manage the state across the application. Whenever the application state is updated, it will reflect everywhere the state is subscribed. It is perfect for our functional components. And even for state components, you do not have to pass the state down through props. It can get messy. With React-Redux, you can maintain a single source of truth where all components can subscribe to. Since it is a state management tool, it can persist between page navigations, which is perfect for our audio player.

If you want to learn more about React Redux, please visit the [official site](https://react-redux.js.org/introduction/getting-started).

To set up a working React Redux store, you have to do the following:

- Install react-redux, redux-thunk
- Create a play audio reducer (React Redux)
- Create a play audio action (React Redux)
- Create a store
- Subscribe the entire application to the store
- Create functions in ``App.js`` to handle audio


### Install react-redux, redux-thunk

Run:

```jsx
yarn add react-redux redux-thunk
```

### Create a play audio reducer (React Redux)

Create a folder called ``redux`` within the ``src`` folder. This folder will house all the react-redux related files.

Create a folder within the ``redux`` folder called ``reducers``. Then create a new file called ``playEpisodeReducer.js`` within the ``reducers`` folder.

Reducers are functions that take the current ``state`` and an ``action`` as arguments and return a new ``state`` result. In other words, ``(state, action) => newState``.

Copy the code below into the ``playEpisodeReducer.js`` file.

```jsx
export const EPISODE_PLAY_REQUEST = 'EPISODE_PLAY_REQUEST'
export const EPISODE_PLAYING = 'EPISODE_PLAYING'
export const EPISODE_PAUSE = 'EPISODE_PAUSE'

const initialState = {
  loading: false,
  isPlaying: undefined,
  episode: {},
  error: false
}

export const playEpisodeReducer = (state = initialState, action) => {
  switch (action.type) {
    case EPISODE_PLAY_REQUEST:
      return {
        ...state,
        episode: action.payload,
        loading: true,
      }

    case EPISODE_PLAYING:
      return {
        ...state,
        isPlaying: true,
        loading: false
      }

    case EPISODE_PAUSE:
      return {
        ...state,
        isPlaying: false
      }

    default:
      return state
  }
}
```

This reducer has 3 actions. 

When the ``EPISODE_PLAY_REQUEST`` is triggered. It returns the state and ``episode`` object, which holds details of the episode the user just clicked on. It then sets ``loading`` to ``true``, although no loading signal was implemented in the application. You can implement that later. It will not impact the application's functionality; the audio is either playing or not in the application. But most audio apps have a loading functionality; as the audio loads, you get a spinning indicator telling you that the music is downloading and will start soon.

The ``episode`` object can now be accessed anywhere in the application because it has been added to the store. The store will be created soon.

The ``EPISODE_PLAYING`` is triggered right after ``EPISODE_PLAY_REQUEST``, and it also returns the state. It changes ``loading`` to ``false``. And also returns ``isPlaying`` as ``true``. ``isPlaying`` will be used to display the pause button. When ``isPlaying`` is ``true``, the pause button will be displayed, and when ``isPlaying`` is ``false``, the play button will display. The play/pause buttons will be implemented in the components.

If no action is passed the code defaults to the ``default`` and just returns the ``state``.

### Create a play audio action (React Redux)

Create a folder within the ``redux`` folder called ``actions``. Then create a new file called ``playEpisodeActions.js`` within the ``actions`` folder. 

Copy the code below into the ``playEpisodeActions.js`` file.

```jsx
import { EPISODE_PLAY_REQUEST, EPISODE_PLAYING, EPISODE_PAUSE } from '../reducers/playEpisodeReducer'

export const play = (episode) => async (dispatch) => {

  const episodeDetails = getEpisodeDetails(episode)
  
  dispatch ({
    type: EPISODE_PLAY_REQUEST,
    payload: episodeDetails
  })

  try {
    dispatch({
      type: EPISODE_PLAYING
    })
  } catch (error) {
    console.log(error)
  }
}

export const pause = () => (dispatch) => {
  dispatch({
    type: EPISODE_PAUSE
  })
}

export const getEpisodeDetails = (episode) => {
  // episode object has so many properties, take only relevant ones
  const { episodeUrl, artworkUrl60, trackId, trackTimeMillis, trackName, shortDescription, collectionName } = episode

  const episodeDetails = {
    episodeUrl,
    artworkUrl60, 
    trackId, 
    trackTimeMillis, 
    trackName, 
    shortDescription, 
    collectionName
  }
  return episodeDetails
}
```

This file has 3 functions, 2 (``play`` and ``pause``) are redux ``actions``. The other one, ``getEpisodeDetails``, is a helper function.

The ``getEpisodeDetails`` takes an episode as a parameter, destructures it to get only the properties that will be displayed in the application. Then creates and returns an object called ``episodeDetails``. This function is necessary because each episode record from Apple's API has a lot of properties. And only a few are needed by the application.

The ``play`` function will be called when the play button is clicked. In the ``play`` function, ``getEpisodeDetails`` is called to trim down episode and save the result in ``episodeDetails`` constant. 

Next, you dispatch the ``EPISODE_PLAY_REQUEST`` action and pass ``episodeDetails`` to the ``reducer``. This dispatch will match the ``EPISODE_PLAY_REQUEST`` condition in the ``playEpisodeReducer.js``'s ``switch`` case, which will save the ``episodeDetails`` to the store.

Then a ``try/catch`` is used to dispatch ``EPISODE_PLAYING`` to the store. This will match ``EPISODE_PLAYING`` condition in the ``playEpisodeReducer.js`` ``switch`` case, which changes the ``isPlaying`` to true, to indicate that audio is playing.

The ``pause`` function dispatches the ``EPISODE_PAUSE`` action to the store, which changes the ``isPlaying`` to false.

The play and pause functions are handled by React components, while redux maintains the currently playing episode. So that you can have it in our footer section, which doesn't change as we navigate between different pages, and you can also navigate through different pages while the audio is playing.

### Create a store

The next step is to bring this all together. By creating a store. The store is responsible for coordinating the flow between the ``action`` and ``reducer``. 

In the ``redux`` folder, create a file called ``store.js``.

Copy the code below into the ``store.js`` file.

```jsx
import { createStore, combineReducers, compose, applyMiddleware } from 'redux'
import thunk from 'redux-thunk';

import { playEpisodeReducer } from './reducers/playEpisodeReducer'

const initialState = {
  currentTrack: { episode : {episodeUrl: undefined}}
}

const reducer = combineReducers({
  currentTrack: playEpisodeReducer
})

const composeEnhancer = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

const store = createStore(
  reducer,
  initialState,
  composeEnhancer(applyMiddleware(thunk))
)

export default store
```

The process of creating a redux store is the same across most applications. For a comprehensive guide to React-Redux, [see this tutorial](https://www.valentinog.com/blog/redux/) and the [official guide](https://react-redux.js.org/tutorials/quick-start#create-a-redux-store).

In this file, multiple modules are imported through ``redux`` and ``redux-thunk``.

The ``reducer`` - ``playEpisodeReducer `` is also imported to register it.

Next, an ``initialState`` object is created. This object has nested objects ``episode`` within ``currentTrack``. And the ``episode`` object has an ``episodeUrl`` property set to ``undefined``.  This property will be used to hide or display the ``FooterPlayer`` component. If ``episodeUrl`` is ``undefined``, the ``FooterPlayer`` will not show. Because it means the user has not tried to play any episode since visiting the application.

This ``initialState`` object is a representation of the redux store when the application is first loaded. When the user clicks on ``play`` then ``episodeDetails`` will be updated with details of the episode that was clicked.

The net part is boiler plate code.

Next  register the ``reducer`` through ``combineReducers``.

Then add the Redux chrome dev tools.

Then finally create the store with the reducer and initial state.

### Subscribe the entire application to the store

Go back to the ``index.js`` file in the root of the ``src`` folder.

Overwrite all the code in ``index.css`` with the code below

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

import { Provider } from 'react-redux'
import store from './redux/store'

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
    
  </React.StrictMode>,
  document.getElementById('root')
);
```

Here you are registering the redux store in our application. All you have done so far is create the store its reducer and actions.


### Create functions in ``App.js`` to handle audio

Now go back to the ``App.js`` file.

And overwrite the code in there with the one below.

```jsx
import React, { useState } from 'react'
import { BrowserRouter } from 'react-router-dom'

import SideBar from './containers/SideBar'
import MainSection from './containers/MainSection'
import MobileHeader from './components/MobileHeader'
import FooterPlayer from './components/FooterPlayer'

import { useDispatch, useSelector } from 'react-redux'
import { play, pause } from './redux/actions/playEpisodeActions'

import './App.css';

function App() {
  const [audio, setAudio] = useState({})
  const dispatch = useDispatch()
  const currentTrack = useSelector((state) => state.currentTrack)
  const { isPlaying, episode: { episodeUrl, collectionName } } = currentTrack

  document.title = collectionName && isPlaying ? collectionName : 'Podcast Player'

  const handlePlay = (episode) => (e) => {  
    let sound
    if (!episodeUrl) {
      sound = new Audio(episode.episodeUrl)
      sound.play()
      setAudio(sound)
      dispatch(play(episode))
    } else if (episodeUrl !== episode.episodeUrl) {
      audio.pause()
      sound = new Audio(episode.episodeUrl)
      sound.play()
      setAudio(sound)
      dispatch(play(episode))
    } else {
      audio.play()
      dispatch(play(episode))
    }
  }

  const handlePause = () => {
    audio.pause()
    dispatch(pause())
  }

  return (
    <div className="App">
      <BrowserRouter>
      <div>
        <MobileHeader />
        <div className="flex relative">
          <SideBar />
          <MainSection handlePause={handlePause} handlePlay={handlePlay} />
          <FooterPlayer handlePause={handlePause} handlePlay={handlePlay} />
        </div>
      </div>
      </BrowserRouter>
    </div>
  );
}

export default App
```

The updated ``App.js`` will handle the play/pause functionality with its ``handlePlay`` and ``handlePause`` functions.

In this file, imported the React-Redux modules that required ``useDispatch`` and ``useSelector``. And we imported the ``play`` and ``pause`` action. When users click on the pause/play button, these actions will be dispatched. They will trigger a change of state of our application by either adding an episode to the redux store, changing the ``isPlaying`` to ``true`` or ``false`` as discussed in the previous sections.

```
// Don't copy part of App.js above
const [audio, setAudio] = useState({})
const dispatch = useDispatch()
const currentTrack = useSelector((state) => state.currentTrack)
const { isPlaying, episode: { episodeUrl, collectionName } } = currentTrack
```

In the snippet above, an audio placeholder is created and initialized to an empty object using the ``useState`` React Hook. As explained in part 2 of this tutorial.

Then ``useDispatch`` is assigned.

And then ``currentTrack`` is gotten from the redux state management or store to get the ``currenctTrack`` object that we passed into the store.

Then ``currentTrack`` is desctructured to get the ``isPlaying``, ``episodeUrl`` and ``collectionName``. Since this is related to state management whenever any of the properties of ``currentTrack`` is updated ``isPlaying``, ``episodeUrl``, and ``collectionName`` will be updated immeditely. Since we are implementing this in the ``App.js``, we can pass this variable down to any proponent at prop to be consumed by any component in our application.

The ``handlePlay()`` function will handle playing new episodes and episodes already in the states ``currentTrack``. It uses JavaScript currying, to pass the particular element to the function. Since the episodes are in a list in the ``PodcastDetailsScreen`` component. If you don't use currying it can lead to unexpected behavior when the button is clicked.

The ``handlePlay()`` has an if statement with 2 conditions and 1 fall back. If the is ``episodeUrl`` is ``undefined`` denoted by ``!episodeUrl``. It means the user just launched the application and has not played any video. So the ``FooterPlayer`` component at the bottom of the page will not be displayed.

```
sound = new Audio(episode.episodeUrl)
sound.play()
setAudio(sound)
dispatch(play(episode))
```

Then assign the episodes' URL to the Audio API, which downloads the audio file. And call the ``play()`` function of the Audio API. Next, call ``setAudio(sound)``, a React Hook, to assess the playing audios' object to our application state variable ``audio``.

Then dispatch the ``play(episode)`` action to React-Redux so that it will save the episode in the state. And make it available to the ``FooterPlayer`` component. The ``play(episode)`` dispatch will also change ``isPlaying`` to ``true`` which will make the pause image appear.

The next condition in the ``if`` statement - ``episodeUrl !== episode.episodeUrl``. This passed the ``episodeUrl`` is ``undefined`` check. It will only be true when the user tries to play a different episode.

```
audio.pause()
sound = new Audio(episode.episodeUrl)
sound.play()
setAudio(sound)
dispatch(play(episode))
```

It pauses any audio currently playing. Before assigning the new ``episodeUrl`` to the Audio API. Besides pausing the audio its functionality is similar to the first condition.

Then:

```jsx
else {
    audio.play()
    dispatch(play(episode))
}
```

The fallback of the if statement. The program only gets to this point if the user is trying to continue playing an episode that was paused.

The ``handlePause()`` just pauses the audio and dispatches ``pause()`` action to change ``isPlaying`` to ``false`` in the redux store.

In the return statement of the ``App`` component. You pass the function down as props to ``MainSection`` and ``FooterPlayer`` component.

```jsx
<MainSection handlePause={handlePause} handlePlay={handlePlay} />
<FooterPlayer handlePause={handlePause} handlePlay={handlePlay} />
```

### 💡 Checkpoint 3
If you have come this far. You should have an empty ``currentTrack`` in your Redux Chrome Extension.

> 🔗 If you don't have the extension. Download it [for Chrome here](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en).

> 🔗 You can find the code for this part here [GitHub](https://github.com/avoajaugochukwu/podcast_player_tutorial/tree/master/Checkpoint_03)

> There will be no live code for this part. But please crosscheck your code with the GitHub source code for this part.





















