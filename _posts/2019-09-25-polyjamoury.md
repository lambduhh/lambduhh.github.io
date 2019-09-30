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
computer program with the ability to manipulate, aggregate and perform actions based on musical data. For this project, I used [Spotipy](https://spotipy.readthedocs.io/en/latest/?highlight=delete#spotipy.client.Spotify.current_user_saved_tracks_delete)
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

## Getting Authorized  

![spotifyfordevelopers](/photos/1569616318416.png)

Authorization required signing up for [Spotify for Developers](https://developer.spotify.com), registering my application and picking a redirect URL. I soon received a 'client_id' and 'secret_id'.
All values were saved in in`config.py` along with the desired `scope` of the project. These were then passed to Spotipy's utility method `util.prompt_for_user_token` as arguments to authorize the user.  
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
This project was the initial inspiration for this project. I often found myself frustrated when "shuffling all saved songs" on Spotify.
It felt like I spent more time skipping past tracks that I felt like I had heard a million times rather than actually listening to my "freshest beats" aka
the songs that had been most recently saved to my library. While ruminating over my annoyance one day I realized "Wait, I'm a super-hacker now...
I wonder if spotify releases their APIs??" And this project was born.


<!--more-->