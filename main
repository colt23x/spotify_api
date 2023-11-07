# Custom DJ from Spotify
from dotenv import load_dotenv
import os
from requests import post, get
import base64
import json

# Function to load client credientials saved in .env
load_dotenv()

client_id = os.getenv("CLIENT_ID")
client_secret = os.getenv("CLIENT_SECRET")

# Client credentials instructions: https://developer.spotify.com/documentation/web-api/tutorials/client-credentials-flow
# Requires auth to be converted to base64 format
def get_token():
    auth_str = client_id + ":" + client_secret
    auth_bytes = auth_str.encode("utf-8")
    auth_base64 = str(base64.b64encode(auth_bytes), "utf-8")

    url = "https://accounts.spotify.com/api/token"
    headers = {
        "Authorization": "Basic " + auth_base64,
        "Content-Type": "application/x-www-form-urlencoded"
    }
    data = {"grant_type": "client_credentials"}
    result = post(url, headers=headers, data=data)
    json_result = json.loads(result.content)
    token = json_result["access_token"]
    return token

# Create the header we need for future requests
def get_auth_header(token):
    return {"Authorization": "Bearer " + token}

# Get artist ID from Spotify
def search_for_artist(token, artist_name):
    url = 'https://api.spotify.com/v1/search'
    headers = get_auth_header(token)
    query = f"?q={artist_name}&type=artist&limit=1"

    query_url = url + query
    result = get(query_url, headers=headers)
    json_result = json.loads(result.content)["artists"]["items"]

    if len(json_result) == 0:
        print("No artist with this name exists")
        return None

    return json_result[0]

# Get top 10 songs from artist using artist ID from Spotify
def get_songs_by_artist(token, artist_id):
    url = f"https://api.spotify.com/v1/artists/{artist_id}/top-tracks?country=US"
    headers = get_auth_header(token)
    result = get(url, headers=headers)
    json_result = json.loads(result.content)["tracks"]
    return json_result

# Get playlist ID from Spotify
def search_for_playlist(token, playlist_name):
    url = 'https://api.spotify.com/v1/search'
    headers = get_auth_header(token)
    query = f"?q={playlist_name}&type=playlist&limit=1"

    query_url = url + query
    result = get(query_url, headers=headers)
    json_result = json.loads(result.content)["playlists"]["items"]

    if len(json_result) == 0:
        print("No playlist with this name exists")
        return None

    return json_result[0]

# Get songs from playlist using playlist ID from Spotify
def get_songs_from_playlist(token, playlist_id):
    url = f"https://api.spotify.com/v1/playlists/{playlist_id}/tracks"
    headers = get_auth_header(token)
    result = get(url, headers=headers)
    json_result = json.loads(result.content)["items"]
    return json_result

# Determine what the user wants to get from spotify
def main():
    token = get_token()
    queue = input("DJ here, what would you like to listen to? Artist or Playlist: ").lower()

    if queue == 'artist':
        artist_name = input("Enter the name of an artist: ")
        result = search_for_artist(token, artist_name)
        if result:
            artist_id = result['id']
            songs = get_songs_by_artist(token, artist_id)
            print(f"Artist Name: {artist_name}")
            for idx, song in enumerate(songs):
                print(f"{idx + 1}. {song['name']}")
        else:
            print("No artist found with that name.")
    elif queue == 'playlist':
        playlist_name = input("Enter the name of a playlist: ")
        result = search_for_playlist(token, playlist_name)
        if result:
            playlist_id = result['id']
            playlist_name = result['name']
            print(f"Playlist Name: {playlist_name}")
            playlist = get_songs_from_playlist(token, playlist_id)
            for n, song in enumerate(playlist):
                print(f"{n + 1}. {playlist[n]['track']['name']}")
    else:
        print("Enter a valid type")



if __name__ == "__main__":
    main()
