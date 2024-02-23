---
title: "Portfolio"
output: 
  flexdashboard::flex_dashboard:
    storyboard: true
    
---

```{r setup, include=FALSE}
library(flexdashboard)
library(tidyverse)
library(ggplot2)
library(spotifyr)
library(compmus)
library(plotly)

corpus_features <- get_playlist_audio_features("", '6iRQ0PFwwV932O3MjgkI5g')
```



### Introduction


*Pasted from assignment 1*
My chosen corpus contains songs from playlists found on Spotify titled or closely resembling ‘autumn’. I chose this corpus because a season is not universally associated with (a particular type of) music, and yet there are many playlists about specific seasons. What makes autumn especially so interesting is that despite it being the most disliked season of the four (at least in my experience), it has so many playlists, many of which do not contain sad music (as one would expect in a playlist about a least favourite season). There are also no direct reasons to create a playlist for this season, like going on a vacation could be a reason to create a summer playlist, or Christmas to create a winter playlist.

The playlists incorporated in the corpus are bound to differ at least a little, since different people from different backgrounds who know and like different songs created them, but significant trends should be discernible. For example, I expect the energy of the songs to be similar, but the duration could differ.

How representative this corpus is, is hard to say. Playlists like these also exist on other music streaming platforms like YouTube, and these can either be incredibly similar or very different, so it is difficult to judge the representativeness.

Some very typical songs for this corpus include The Night We Met by Lord Huron, Cardigan by Taylor Swift, I Wanna Be Yours by the Arctic Monkeys, Yellow by Coldplay and Sweater Weather by The Neighbourhood. These are typical because they appear in several of the playlists I used and looked at for my corpus, indicating that all of the creators of the playlists thought of the same song, so the song must really fit the criteria.

Tabs {data-width=350}
-----------------------------------------------------------------------

### First visualisation

```{r}
valencexdanceability <- ggplot(corpus_features, aes(x=valence, y=danceability)) +
  geom_point(alpha=0.3, color='darkgreen')

valencexdanceability
```

***
This graph depicts the valence on the x-axis and the danceability on the y-axis. It shows that the happier the song, the higher the danceability, which was to be expected. However, is also a big cluster of songs that are classified as having a low valence that still score somewhat high on the danceability scale. Therefore, one of the assumptions that can be made is that some 'autumn' songs can be classified as negative sounding with still some level of danceability.


### Second visualisation

```{r}
## All too well t version
tversion <-
  get_tidy_audio_analysis("5enxwA8aAbwZbf5qCHORXi") |>
  select(segments) |>
  unnest(segments) |>
  select(start, duration, pitches)
## All too well sad girl autumn version
sgaversion <-
  get_tidy_audio_analysis("1n3b9Eekoy3S9ZSZ5DmTW1") |>
  select(segments) |>
  unnest(segments) |>
  select(start, duration, pitches)

compmus_long_distance(
  tversion |> mutate(pitches = map(pitches, compmus_normalise, "manhattan")),
  sgaversion |> mutate(pitches = map(pitches, compmus_normalise, "manhattan")),
  feature = pitches,
  method = "euclidean"
) |>
  ggplot(
    aes(
      x = xstart + xduration / 2,
      width = xduration,
      y = ystart + yduration / 2,
      height = yduration,
      fill = d
    )
  ) +
  geom_tile() +
  coord_equal() +
  labs(x = "All too well Taylor's version", y = "All too well sad girl autumn version") +
  theme_minimal() +
  scale_fill_viridis_c(guide = NULL)
```

***
This graph depicts two performances of 'All too well' by Taylor Swift. 