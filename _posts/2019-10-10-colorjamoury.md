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

First steps to implementing my algorithm called for creating a pool of data from which to draw songs. Rather than just selecting from all
my saved tracks, I thought it would be more interesting to instead create a pool that contained songs that I may not necessarily be acquainted with.
{% highlight python %}
def get_all_saved_artists() -> list:
    st = get_saved_artists('short_term')
    stitems = st['items']
    mt = get_saved_artists('medium_term')
    mtitems = mt['items']
    lt = get_saved_artists('long_term')
    ltitems = lt['items']
    return append(stitems, mtitems, ltitems)
    
def audio_features_all_saved():
    saved_artists = get_all_saved_artists()
    saved_artist_uri = set(mapv(get_artist_uris, saved_artists))
    # 91 different top saved artists (9/16/19)
    ttrack_database = []
    for artist in saved_artist_uri:
        all_artist_top_songs = get_artist_top_tracks(artist)['tracks']
        for song in all_artist_top_songs:
            ttrack_database.append(song['uri'])
    # a database that is a list
    # of user's top artist's top tracks, 921 songs (9/16/19)
    return ttrack_database

{% endhighlight %}

I used a similar strategies as I did in [Recently Added Playlist](https://lambduhh.github.io/2019/09/25/polyjamoury.html#recently-added-playlist) except this time I adapted the methods I used to append my `short-term`, `medium-term`, and `long-term` artists.
As of 9/16/19 this extracted 91 of my different **favorite artists**. After finding each `artist_uri` using `mapv`, I looped through and pulled each
artist's **top 10 songs** and added each to `ttrack_database`. This left me with a pool of 921 songs by my favorite artists to play with. Now the real fun can begin!

## Creating a mood

When listening to any track I knew what "color" that **I** would categorize it into, but I needed to be able to duplicate this process
in my program. To get a better idea of the story that the data wanted to tell I handpicked songs that I felt properly represented each color. 

<div style="box-shadow: 3px 3px 2px darkred"> 
<iframe src="https://open.spotify.com/embed/track/27gUeiai56GKF6TxvmPJut" width="300" height="80" frameborder="0" allowtransparency="true" allow="encrypted-media"></iframe>
</div>
<div style="box-shadow: 3px 3px 3px darkred"> 
<iframe src="https://open.spotify.com/embed/track/59WN2psjkt1tyaxjspN8fp" width="300" height="80" frameborder="0" allowtransparency="true" allow="encrypted-media"></iframe> 
 </div>
 
To accurately paint the picture of moods I wanted to explore, I created a data type based on colors to correspond with mood. 
