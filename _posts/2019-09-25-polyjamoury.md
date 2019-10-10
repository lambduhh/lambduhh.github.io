---
title: Polyjamoury
tags: spotipy spotify python
article_header:
  type: cover
  image:
    src: /photos/imageedit_1_7997346933.jpg
---

## An application for those of us who have multiple favorite artists and love them all equally.

Early 2000's I would come home from school, immediately bop over to the study and fire up the ole family desktop computer.
I can still see the glow of the blue screen and hear the whirling sounds of Windows XP booting up (the last Windows operating system I've used to this day).
While carefully listening for the telltale thump of adult footsteps, I quickly got a dummy "homework" word document loaded up, ready to fill the screen
at a moment's notice. Then, with my carefully crafted facade in place, I was able to dig out the limewire program I had "hidden" in deeply nested folders. I
got to work launching Itunes, AOL Instant Messenger and my myspace page. Most of these actions were explicitly prohibited of course, which made it all the
more fun.

Creating this application reminded me of those good old days getting lost in the computer screen. My first lines of code were written at that desk
while designing my Myspace page. Long before "daily mixes" and "Discovery Weekly" playlists, I logged many hours at that desktop, manually picking out
and listening to each song and artist to discover new music... tediously categorizing my findings, creating new playlists, and renaming songs.

Now as an adult, I unfortunately do not have the time to commit hours everyday curating the perfect playlists. However, I *have* gained the skills to build a data driven 
computer program with the ability to manipulate, aggregate and perform actions based on musical data. For this project, I used [Spotipy](https://spotipy.readthedocs.io/en/latest/)
"a lightweight Python library" based on Spotify's RESTful APIs which are available at [Spotify for Developers](https://developer.spotify.com) .

## Project Architecture 

This project follows a fairly canonical Flask application architecture for a casual, one-off project (try saying that 3 times fast).
Directory names and modules are based on function and structured to avoid circular imports. 

I frequently use [naga](https://github.com/jjtolton/naga), a super neat python/lisp-hybrid-monster library.  
 
>![folder](/photos/Paomedia-Small-N-Flat-Folder-document.svg)  'spot.py'  
>  All interactions involving spotipy API calls live here  
>
>![folder](/photos/Paomedia-Small-N-Flat-Folder-document.svg)  'utils.py'  
>  A module for more general, reusable functions  
>
>![folder](/photos/Paomedia-Small-N-Flat-Folder-document.svg)  'config.py'  
>  A module to handle global constants mostly for authentication

<img src="/photos/warning-icon.png" alt="warning" width="50" align="left" hspace="20" />
To fully grasp the concepts used in this walkthrough, you should have a basic understanding of how [Higher Order Functions](https://www.oreilly.com/library/view/functional-programming-in/9781492048633/ch04.html) work including `map`, `filter`, `reduce` and `partial`.

## Getting Authorized  

![spotifyfordevelopers](/photos/1569616318416.png)

Authorization required signing up for [Spotify for Developers](https://developer.spotify.com), registering my application and picking a redirect URL. I soon received a 'client_id' and 'secret_id'.
All values were saved in in`config.py` along with the desired `scope` of the project. These were then passed to Spotipy's utility method `util.prompt_for_user_token` as arguments to authorize the user.
Since Spotify's authorization is not indefinite, I also created a `reauth` function to aid in reauthorization efforts. I had to figure out a slight workaround for working in [pycharm](https://www.jetbrains.com/pycharm/) since the reauth link produced as an output to the authorization code cannot be followed from the IDE. 
An additional step was added to print the code and return an access token.  
{% highlight python %}
    def reauth(conn_str: str) -> "token":
        """Reauthorize spotify app.
        :param conn_str: "https://www.google.com/?code=AQBBH8rayjMv1yRGBQFMZK5jJkMEWxJi5HMuK_qZubCj71HoMzMD0mH_v8WVGEd3kuYsTw7TB0uKwlmTVmUz3q8xcSwX6ppHHrP0aykTqtTHlLePVsXuvCdH8MsZjVo6E0fXSEmN4xwCK5MXIDVu6AAn2EzxdlgtdbdKrBXSzR9Tt--B19vYkOPTtteTGF1bmzHUTBGSC-P-pH3nRvtqA4v90LlEMFFcMKUPfMKKOpAxNtGGLNhqc4opynVIJ_Gk3tTLbbrpKdYY_Ftq56gf6RTh9YWGTg"
        :type conn_str: str
        :return: token
        """
        sp_oauth = oauth2.SpotifyOAuth(client_id, client_secret_id, redirect_uri,
                                       scope=scope, cache_path=".cache-" + username)
        code = sp_oauth.parse_response_code(conn_str)
        print(code)
        token_info = sp_oauth.get_access_token(code)
    
        token = token_info['access_token']
        return token
    
    
    try:
        token = util.prompt_for_user_token(username, scope, client_id, client_secret_id, redirect_uri)
    except Exception as e:
        token = None
        raise Exception("Unable to obtain token")
    k
    try:
        spotify = spotipy.Spotify(auth=token)
        user = spotify.current_user()
        user_id = user['id']
    # prints the type of exception error and exits the program if connection
    except Exception as e:
        print(type(e))
        sys.exit('Cannot connect to spotify')


{% endhighlight %} 

Authorization attempts are wrapped in `try-execpt` clauses to avoid the program crashing if not connected to internet.  




## Recently Added Playlist  

<img src="/photos/9d432dd2ed4e418b256cafaf9bf138a2d19366e6288eebf919e44642f57a8419.jpg" alt="rhcp" width="150" align="left" hspace="20" />

This project was the initial inspiration for this project. I often found myself frustrated when "shuffling all saved songs" on Spotify.
It felt like I spent more time skipping past tracks that I felt like I had heard a million times rather than actually listening to my "freshest beats" aka
the songs that had been most recently saved to my library. While ruminating over my annoyance one day I realized "Wait, I'm a super-hacker now...
I wonder if spotify releases their APIs??" And this project was born.  
<br>
{% highlight python %}
def saved_tracks() -> list:
    recent50 = get_recent50()
    items_of_r50 = recent50['items']
    next50 = get_next_50()
    items_of_n50 = next50['items']
    all_100_tracks = append(items_of_r50, items_of_n50)
    return all_100_tracks
{% endhighlight %}


The first step in creating a playlist of recently saved songs, was identifying and isolating my most recently saved songs.
The [Spotipy](https://spotipy.readthedocs.io/en/latest/) method `spotify.current_user_saved_tracks` only collects up to 50 tracks per function call, so the information for
100 tracks needed to be retrieved using two different calls, `get_recent50()` and `get_next50()` (recall that all spotipy methods are wrapped in functions that are imported from the `spot` directory).
After pulling the relevant information from the data structure using the key `['items]`,
the function `saved_tracks` returns a list of dictionaries containing relevant (and irrelevant) information about the track.
The function `append` from the naga libary is then used to concatenate all the dictionaries into one master list that contains the user's last 100 saved tracks.
Although the most recently saved 100 tracks have now been identified, ALL the spotify data associated with each track has been copied over as well.
To get a better idea of how to navigate this cumbersome data structure I copied it over to a `json` file named `s_track_data`. 
![cumbersome](/photos/pycharm1.png)
Nested within almost 24k lines of code were 100 individual `track uri` values that needed to be extracted. OH MY!  
{% highlight python %}

def get_track_uris(d: dict) -> list:
    # get_in returns the value in a nested associative structure, where second arg is a sequence of keys.
    track_uri = get_in(d, ["track", "uri"], not_found=None)
    return track_uri
    
def create_playlist(name_of_playlist):
    playlist = playlist_create(name_of_playlist)
    playlist_id = playlist['id']
    return playlist_id

def make_playlist_of_last_100(name_of_playlist, tracks):
    # mapv = list(map())
    s_track_uri_data = mapv(get_track_uris, tracks)
    pl_id = create_playlist(name_of_playlist)
    add_tracks_to_playlist(pl_id, s_track_uri_data)
    return None
    
if __name__ == '__main__':
    all_tracks = saved_tracks()
    make_playlist_of_last_100("latest added jamzz 10/3/19", all_tracks)

{% endhighlight %}

<br>
To create a predicate function for `map` to apply to the list of tracks, I once again turned to the naga libary for `get_in` and `mapv`.
The function `get_in` returns a value from a nested associative data structure, while `mapv` simply lists and displays the content when mapping over a collection.
I am able to use `mapv` to apply the `get_track_uris` function over each dictionary returned from `saved tracks`
(which is passed into `make_playlist_of_last_100` as the second argument). Each `track_uri` is extracted and placed into a list named `s_track_uri_data`.
The final steps included creating a playlist and adding each track to the playlist.
After running `make_playlist_of_last_100` , I can open spotify on my phone and listen to my new playlist.

<img src="/photos/recentlyaddedfinished.jpg" alt="finished" width="200x" align="left" hspace="20" />


### Roadmap and TODOS
Future improvements to this code could include an `auto_delete` function that automatically deletes the last Recently added playlist created before creating a new one (an action I am currently doing manually).
  
# Part II Colorjamoury
After completing this project I was excited to do more with the spotify API. I 



<!--more-->