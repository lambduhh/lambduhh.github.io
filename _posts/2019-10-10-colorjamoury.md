---
title: Polyjamoury Part II
tags: spotipy spotify python music songs mood color track
article_header:
  type: cover
  image:
    src: /photos/imageedit_1_7997346933.jpg
---
## The many moods of the rainbow.

While messing around with the [Spotify](https://developer.spotify.com/documentation/web-api/) APIs I found one of the most interesting data points they offered insight into was
a track's "audio features". Each song in Spotify has 18 different attributes that can be derived, displayed and utilized as a data reference.
I was impressed by Spotify's ability to seemingly quantify music and represent a song as data and I wondered how far I
could experiment with this information. Using my personal interpretation of how colors correspond
to mood I set out to create user playlists that group together similar sounding tracks for a personalized listening experience.
This project follows a style of functional "data-driven development", I adopt a logical strategy that allows the data to "do the heavy lifting".  

I hope you enjoy this walkthrough as much as I enjoyed making this project!

<img src="/photos/warning-icon.png" alt="warning" width="50" align="left" hspace="20" />If you haven't yet, please refer to [Polyjamoury](https://lambduhh.github.io/2019/09/25/polyjamoury.html) for an introduction to the Project Architecture and Authorization Steps. 

### Creating a pool of data
<img src="/photos/data-pool.jpg" alt="warning" width="200" align="right" hspace="20" />
First steps to implement my algorithm called for creating a pool of data to draw tracks from. 



To accurately paint the picture of moods I wanted to explore, I created a data type based on colors to correspond with mood. 
