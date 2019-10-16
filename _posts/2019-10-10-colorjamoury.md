---
title: Polyjamoury Part II
tags: spotipy spotify python music songs mood color track
article_header:
  type: cover
  image:
    src: /photos/imageedit_1_7997346933.jpg
---
# The Many Moods of the Rainbow.

While messing around with the [Spotify](https://developer.spotify.com/documentation/web-api/) APIs I found one of the most interesting data points they offered insight into was
a track's [audio features](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/). Each song in Spotify has 18 different attributes that can be derived, displayed and utilized as a data reference.
I was impressed by Spotify's ability to seemingly quantify music and represent a song as data and I wondered how far I
could experiment with this information. Using my personal interpretation of how colors correspond
to mood I set out to create user playlists that group together similar sounding tracks for a personalized listening experience.
This project follows a style of functional "data-driven development", I adopt a logical strategy that allows the data to "do the heavy lifting".  

I hope you enjoy this walkthrough as much as I enjoyed making this project!

<img src="/photos/warning-icon.png" alt="warning" width="50" align="left" hspace="20" />If you haven't yet, please refer to [Polyjamoury](https://lambduhh.github.io/2019/09/25/polyjamoury.html) for an introduction to the Project Architecture and Authorization Steps. 

## Composing a mood

When listening to any track I knew what color that ***I*** would categorize it, but I needed to be able to duplicate this process
in my program. To get a better idea of the story that the data wanted to tell I handpicked songs that I felt properly represented each color. 


<p style="max-width:705px;height:40px;background-color:red;color:black;border:1px;padding:1px;"> <strong> Red- Fast, Angry, Passionate </strong> </p> 
<iframe src="https://open.spotify.com/embed/track/27gUeiai56GKF6TxvmPJut" width="350" height="80" style="border:5px solid red;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe>
<iframe src="https://open.spotify.com/embed/track/59WN2psjkt1tyaxjspN8fp" width="350" height="80" style="border:5px solid red;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe> 
 
<p style="max-width:705px;height:40px;background-color:gold;color:black;border:1px;padding:1px;"> <strong> Yellow- Sunny, Positive, Upbeat </strong> </p>                
<iframe src="https://open.spotify.com/embed/track/6YbhspuOar1D9WSSnfe7ds" width="350" height="80" style="border:5px solid gold;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe>
<iframe src="https://open.spotify.com/embed/track/63dv2zKXPamiHHOKQBOzbU" width="350" height="80" style="border:5px solid gold;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe>

<p style="max-width:705px;height:40px;background-color:green;color:black;border:1px;padding:1px;"> <strong> Green- Mellow, Acoustic, Calm </strong> </p>                
<iframe src="https://open.spotify.com/embed/track/6kLqKntp2x4QVXHwqLHMYm" width="350" height="80" style="border:5px solid green;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe>
<iframe src="https://open.spotify.com/embed/track/7ceOfsRdzaLgpf5TAKSgu5" width="350" height="80" style="border:5px solid green;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe>

<p style="max-width:705px;height:40px;background-color:lightskyblue;color:black;border:1px;padding:1px;"> <strong> Blue- Soothing, Slow, Sad </strong> </p>                
<iframe src="https://open.spotify.com/embed/track/3ZkleTkBBKIPKJh9jIqT7s" width="350" height="80" style="border:5px solid lightskyblue;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe> 
<iframe src="https://open.spotify.com/embed/track/26uHwSHlaK5mskSMhbRod3" width="350" height="80" style="border:5px lightskyblue;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe>

<p style="max-width:705px;height:40px;background-color:pink;color:black;border:1px;padding:1px;"> <strong> Pink- Feminine, Fun, Dancable </strong> </p>                
<iframe src="https://open.spotify.com/embed/track/6aLpLTAUASREvQE105XOb3" width="350" height="80" style="border:5px solid pink;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe>
<iframe src="https://open.spotify.com/embed/track/0lsRatBUs9HNIZAmoGABzk" width="350" height="80" style="border:5px solid pink;" frameborder="1" allowtransparency="true" allow="encrypted-media"></iframe>

For each track I copied the `song_features` over to a `json` file using  

     with open('red.json', 'w') as f: f.write(json.dumps(red, indent=2))`

Here is an example of the output:

{% highlight javascript %}
[
  {
    "danceability": 0.488,
    "energy": 0.995,
    "key": 1,
    "loudness": -3.096,
    "mode": 0,
    "speechiness": 0.108,
    "acousticness": 0.00153,
    "instrumentalness": 0,
    "liveness": 0.911,
    "valence": 0.679,
    "tempo": 90.058,
    "type": "audio_features",
    "id": "6HZdbb05lEXLvcmee3ZXO2",
    "uri": "spotify:track:6HZdbb05lEXLvcmee3ZXO2",
    "track_href": "https://api.spotify.com/v1/tracks/6HZdbb05lEXLvcmee3ZXO2",
    "analysis_url": "https://api.spotify.com/v1/audio-analysis/6HZdbb05lEXLvcmee3ZXO2",
    "duration_ms": 187933,
    "time_signature": 4
  }
]
{% endhighlight %}

I was then able to analyze and compare the similarities between each track in relation to one another. 
To accurately paint the picture of the moods I wanted to explore, I then composed my constraints based on this audio information and how it corresponded with the intended mood/color.
For this project I focused on 5 different attributes: danceability, energy, tempo, acousticness and valence. These are the definitions from the [Spotify](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/) documentation.

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;border-color:#9ABAD9;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#9ABAD9;color:#444;background-color:#EBF5FF;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#9ABAD9;color:#fff;background-color:#409cff;}
.tg .tg-lut3{font-style:italic;background-color:#343434;color:#efefef;border-color:inherit;text-align:right;vertical-align:middle}
.tg .tg-4gv2{background-color:#4949dd;color:#efefef;border-color:inherit;text-align:left;vertical-align:middle}
.tg .tg-wgfq{background-color:#3f3f63;font-weight:bold;color:#efefef;border-color:inherit;text-align:right;vertical-align:middle}
.tg .tg-qwnc{background-color:#3f3f63;font-style:italic;color:#efefef;border-color:inherit;text-align:right;vertical-align:middle}
.tg .tg-u11z{font-weight:bold;background-color:#343434;color:#efefef;border-color:inherit;text-align:right;vertical-align:middle}
.tg .tg-zc1e{background-color:#3f3f63;color:#efefef;border-color:inherit;text-align:left;vertical-align:middle}
.tg .tg-9f6c{background-color:#4949dd;color:#efefef;border-color:inherit;text-align:center;vertical-align:middle}
.tg .tg-toj7{background-color:#4949dd;color:#efefef;border-color:inherit;text-align:right;vertical-align:middle}
.tg .tg-9j2z{background-color:#343434;color:#efefef;border-color:inherit;text-align:left;vertical-align:middle}
</style>
<table class="tg">
  <tr>
    <th class="tg-9f6c">Key</th>
    <th class="tg-toj7">Type</th>
    <th class="tg-4gv2">Description</th>
  </tr>
  <tr>
    <td class="tg-wgfq">Danceability</td>
    <td class="tg-qwnc">float</td>
    <td class="tg-zc1e">Danceability describes how suitable a track is for dancing based on a combination of <br>musical elements including tempo, rhythm stability, beat strength, and overall regularity.<br>A value of 0.0 is least danceable and 1.0 is most danceable. </td>
  </tr>
  <tr>
    <td class="tg-u11z">Energy</td>
    <td class="tg-lut3">float</td>
    <td class="tg-9j2z">Energy is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity. <br>Typically, energetic tracks feel fast, loud, and noisy. For example, death metal has high energy, <br>while a Bach prelude scores low on the scale. Perceptual features contributing to this attribute include:<br> dynamic range, perceived loudness, timbre, onset rate, and general entropy.</td>
  </tr>
  <tr>
    <td class="tg-wgfq">Tempo</td>
    <td class="tg-qwnc">float</td>
    <td class="tg-zc1e">The overall estimated tempo of a track in beats per minute (BPM). In musical terminology,<br>tempo is the speed or pace of a given piece and derives directly from the average beat duration.</td>
  </tr>
  <tr>
    <td class="tg-u11z">Acousticness</td>
    <td class="tg-lut3">float</td>
    <td class="tg-9j2z">A confidence measure from 0.0 to 1.0 of whether the track is acoustic.<br>1.0 represents high confidence the track is acoustic.</td>
  </tr>
  <tr>
    <td class="tg-wgfq">Valence</td>
    <td class="tg-qwnc">float</td>
    <td class="tg-zc1e">A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track.<br>Tracks with high valence sound more positive (e.g. happy, cheerful, euphoric),<br>while tracks with low valence sound more negative (e.g. sad, depressed, angry). </td>
  </tr>
</table>


## Sketching out Constraints
 
Remember when I mentioned we were going to get into some "data-driven development"? Well buckle your seatbelts kiddos because here is the dashing dictionary
that is the star of this show!
{% highlight python %}
colors = {"red": {"danceability": {"min": 0, 'max': .5},
                  "energy": {"min": .7, 'max': 1.0},
                  "acousticness": {"min": 0, 'max': .4},
                  "tempo": {"min": 65.0, 'max': 300.0},
                  "valence": {"min": 0, 'max': .45}
                  },
          "yellow": {"danceability": {"min": .45, 'max': 1.0},
                     "energy": {"min": .6, 'max': 1.0},
                     "acousticness": {"min": 0, 'max': .5},
                     "tempo": {"min": 60.0, 'max': 200.0},
                     "valence": {"min": .8, 'max': 1.0}
                     },
          "pink": {"danceability": {"min": .7, 'max': 1.0},
                   "energy": {"min": .25, 'max': .8},
                   "acousticness": {"min": 0, 'max': .5},
                   "tempo": {"min": 0, 'max': 150},
                   "valence": {"min": .5, 'max': .85},
                   },
          "green": {"danceability": {"min": .5, 'max': .7},
                    "energy": {"min": .2, 'max': .5},
                    "acousticness": {"min": .5, 'max': 1.0},
                    "tempo": {"min": 0.0, 'max': 180},
                    "valence": {"min": .35, 'max': .75}
                    },
          "blue": {"danceability": {"min": 0, 'max': .48},
                   "energy": {"min": 0, 'max': .5},
                   "acousticness": {"min": .2, 'max': .8},
                   "tempo": {"min": 0, 'max': 180},
                   "valence": {"min": 0, 'max': .5}
                   }}

{% endhighlight %}


## Creating a pool of data  


<img src="/photos/data-pool.jpg" alt="warning" width="200" align="right" hspace="20" />

Next steps to implementing my algorithm called for creating a pool of data from which to draw songs to compare to my data type.
Rather than just selecting from all
my saved tracks, I thought it would be more interesting to instead create a pool that contained songs that I may not necessarily be acquainted with.
I listen to a myriad of different musical artists and genres and I wanted that to be reflected in my playlists so I decided to take my top artist's top songs.

{% highlight python %}
def get_all_fav_artists() -> list:
    st = get_saved_artists('short_term')
    stitems = st['items']
    mt = get_saved_artists('medium_term')
    mtitems = mt['items']
    lt = get_saved_artists('long_term')
    ltitems = lt['items']
    return append(stitems, mtitems, ltitems)

    
def audio_features_all_favs() -> list:
    fav_artists = get_all_fav_artists()
    saved_artist_uri = set(mapv(get_artist_uris, fav_artists))
    # 91 different top saved artists (9/16/19)
    ttrack_database = []
    for artist in saved_artist_uri:
        all_artist_top_songs = get_artist_top_tracks(artist)['tracks']
        for song in all_artist_top_songs:
            ttrack_database.append(song['uri'])
    # a database that is a list
    # of user's top artist's top tracks, 921 songs (9/16/19), 750 songs (10/11/19)
    return ttrack_database
{% endhighlight %}

I used a similar strategies as I did in [Recently Added Playlist](https://lambduhh.github.io/2019/09/25/polyjamoury.html#recently-added-playlist) except this time I adapted the methods I used to append my `short-term`, `medium-term`, and `long-term` artists.
As of 9/16/19 this extracted 91 of my different **favorite artists**. After finding each `artist_uri` using `mapv`, I looped through and pulled each
artist's **top 10 songs** and added each to `ttrack_database`. This left me with a pool of 921 songs by my most listened to artists to play with.
Down in my `if __name__ == '__main__':` I assigned `top_artist_top_tracks = audio_features_all_favs()` I found that running this section
of code took quite a while so to manage my runtime I copied it all over to a .json file `all_fav_song_features`.
`
## A Small Side of FPS - Functional Programming Snobbery

This next part can get a little complex if you aren't well acquainted with Functional Programming concepts so to
introduce these paradigms we are going to take a little break and step away from our Polyjamoury code for just one moment.
If you are already familiar with `filter` and `partial` than please feel free to skip down to the ___ section.  

Alright folks, as tempted as I am to explore all the different nuances between Procedural/Imperative and Functional Programming ideologies,
that is not the focus of this article. For my intents and purposes I am just going to review a couple of key concepts that may
help you digest the next section of code. If you are intrigued by what you see here, I encourage you to read [SICP](https://mitpress.mit.edu/sites/default/files/sicp/index.html)
(affectionately known as 'The Wizard Book) and watch [this](https://www.youtube.com/watch?v=2Op3QLzMgSY&list=PL8FE88AA54363BC46)
series of videos offered by MITOpenCourseWare. 
 
### Procedures as Black Box Abstractions

<img src="/photos/blackbox.png" alt="blackbox" max-width="500" height="200" align="left" hspace="20" />

When working to solve a problem, a natural strategy is to break it down into smaller, more easily manageable parts. We can rely on 
separation of concerns to dictate the purpose of each different building block or function. Each function has one purpose or objective- and
we can test this by looking at the input (i.e. parameters or arguments) and output (i.e. the return statement).
By observing *only* the input and output we let the inner workings of our function become irrelevant. 
>"At that moment we are not concerned with *how* the procedure computes its result, only with the fact that it computes.
The details of how the (function) is computed can be suppressed, to be considered at a later time.
Indeed, as far as the procedure is concerned, it is not quite a procedure but rather an *abstraction*
of a procedure, a so-called *procedural abstraction*. At this level of abstraction, any procedure that computes
is equally good." (Abelson, H. (1979) **SICP** Cambridge, MA. The MIT Press)



<iframe width="320" height="215" src="https://www.youtube.com/embed/X7HmltUWXgs?start=1" align="left" frameborder="2px" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
You can think of **functions** or **procedures** as mini robots, they have an input, an output and an intended abstract "purpose".
Each robot that is built can operate independent of one another. This allows them to be moved or changed with each robot's `state` remaining contained (i.e. no `side effects`).
This is referred to as `immutable state` and is beneficial to consider when writing large programs.

But what happens if one robot was to build a smaller robot? Or if one robot was able to pick up and use another robot?
This is what a [Higher Order Function](https://en.wikipedia.org/wiki/Higher-order_function) does. Many useful HOF can be found
in the [functools](https://docs.python.org/3/library/functools.html) module.
> A Higher Order Function is a function that does at least one of the following:
- takes on or more functions as arguments
- returns a function as its result  


### Filter
When using a Higher Order Function I often refer to the function I write to "feed" to the Higher Order Function as a "predicate" function.
Let's say you are asked to find which numbers are even when given a list of numbers [1 ,2, 3, 4]. We could use [filter](https://www.w3schools.com/python/ref_func_filter.asp)
to do so, but first we need to write a function to pass to `filter`
*Note: Since computers are lazy when using a HOF `list` must be used to display the content, otherwise your output will read `<filter object at 0x7d98ab32a700>`*
  
{% highlight python %}
def is_even(number):  # this is the predicate function
    if (number % 2) == 0:
        return True


def returns_even_numbers(list_of_numbers):
    even_numbers = list(filter(is_even, list_of_numbers)) 
    return even_numbers


if __name__ == '__main__':
    print(returns_even_numbers([1, 2, 3, 4]))
    
>>> [2, 4]
{% endhighlight %}

Here is a visualization of the process `filter` is going through to return the new list [ 2, 4 ]

<img src="/photos/filter.png" alt="filter" max-width="600" height="300" hspace="20" />

Get it? Got it? Good! Now let's move on to just *one* more concept before getting back to the polyjamoury code.  

### Partial
The next relevant HOF to understand is `functools. partial`. 
> Partial creates a *new* version of the given function
with one or more arguments already filled in.

Implementing `partial` in your code can allow for more flexibility in your callbacks and can make for more elegant and reusable code.
Another benefit to using `partial` is it's ability to be passed in to a function as a single argument, although it carries the
weight of what could possibly be many more arguments.
{% highlight python %}

def adds_some_numbers(a, b, c):
    return a + b + c

def adds_10_to_some_numbers(a, b):
    return adds_some_numbers(10, a, b)
    
if __name__ == '__main__':
    print(adds_10_to_some_numbers(10, 20))
>> 40

{% endhighlight %}

This code above does the same thing as this code below:
{% highlight python %}
def adds_some_numbers(a, b, c):
    return a + b + c

adds_10_to_some_numbers = partial(adds_some_numbers, 10)

if __name__ == '__main__':
    print(adds_10_to_some_numbers(10, 20))
>> 40

{% endhighlight %}

<img src="/photos/goodguypartial.jpg" alt="goodguypartial" height="200" width="250" align="right" hspace="20" />
<br>
So `partial` is kind of like your cool friend Greg who actually brings his own beer to the party.  <br> <br>
  
  

## Comparing to Sort
Now we have all that sorted out we can return to the main project. At this point I have created a pool of song data
for my algorithm and constructed the constraints by which to sort songs into their mood color.  
But how can I go about actually sorting each song and placing it into a playlist?  
Well, my next step will be to create a "predicate function" so that I may be able use `filter` to compare the audio features
of each track to the color desired .
{% highlight python %}
# predicate fn to get passed to (filter(partial(fn))
def is_song_color(color: dict, song: dict) -> bool:
    if not color["danceability"]["min"] <= song["danceability"] <= color["danceability"]["max"]:
        return False
    elif not color["energy"]["min"] <= song["energy"] <= color["energy"]["max"]:
        return False
    elif not color["acousticness"]["min"] <= song["acousticness"] <= color["acousticness"]["max"]:
        return False
    elif not color["tempo"]["min"] <= song["tempo"] <= color["tempo"]["max"]:
        return False
    elif not color["valence"]["min"] <= song["valence"] <= color["valence"]["max"]:
        return False
    else:
        return True

{% endhighlight %}

All this function does is ask the question- Does the information in this *single* tracks's audio features (`song: dict`) fit within the constraints
 of `color :dict` ? If so, return True.
 In other words, `is_song_color`?
  
 {% highlight python %}

def get_song_features():
    with open('json_data/all_fav_songs_features.json') as f:
        all_fav_songs_features = json.load(f)
    return all_fav_songs_features


def create_color_data(color: str) -> list:
    all_fav_songs_features = get_song_features()
    colorsongs = list(filter(partial(is_song_color, colors[color]), all_fav_songs_features.values()))
    return colorsongs


def save_songs(colorsongs):
    with open('colorsongs.json', 'w') as f:
        f.write(json.dumps(colorsongs, indent=2))
    return colorsongs

    
if __name__ == '__main__':
    colorsongs = create_color_data("red")
    save_songs(colorsongs)

    
 {% endhighlight %}
As I mentioned earlier, to manage runtime and make my life easier I wrote the pool of data `all_song_features` to a `.json` file.
When `create_color_data` is run with the selected color passed in as an argument it returns a list of the tracks that fit within the
constraints of that particular color. 

<img src="/photos/filterpartialred.svg" alt="filterpartial" hspace="20" />