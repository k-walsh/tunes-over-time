# Tunes Over Time
Team Members: Annabeth Stokely, Kiera Walsh, Sophia Liu, Mandy He

Interested in Spotify's song data and the relationship between pop music and time, we analyzed song data from 1958-2020 to examine changes in music over time. This project includes data collection, exploratory analysis, hypothesis/problem statement, and confirmatory analysis/prototype development.

## Data collection description:
We downloaded a dataset from kaggle that included all songs that appeared on the Billboard hot 100 charts since 1958. We chose to use 5 features of the kaggle dataset: date of the billboard chart, song name, artist, peak rank, and weeks on board. We queried the database using SQL to sort the songs from each year by their top rank on the chart and then their weeks on the chart. We chose the latest entry for the song in order to record the highest rank and weeks on board. We then chose the top 75 songs from each year, resulting in 4,725 popular songs. We made requests to the Spotify API to retrieve the spotify ID using the song and name from the original billboard dataset. The names of songs in the Billboard chart and on Spotify are not necessarily the same, so we compared the titles and artists of each song using a sequence matcher library in order to find songs from the API with titles that were similar enough. We then used these ids to retrieve the audio features from the API to get 4,669 data points (dropping the 56 songs that were not found on spotify). These were all added into a single dataframe that we downloaded as a .csv file.

## Data commentary:
We got the Billboard Hot Charts dataset from Kaggle, which is used by numerous well-known companies for data science and is highly reputable. U.S. Billboard charts going back to 1958 are publicly available online. The Spotify data we gathered for each song is also reliable since we got our data directly from the Spotify API. Since the songs that we have in our final dataset are the ones on the Billboard Charts that are also on Spotify, the data is skewed towards songs that are on Spotify, however only 56 songs out of 4,725 were not found on Spotify.

## Data schema description:
Each line in the database contains information for one song that appears in the Billboard charts. Columns include: 
- date (int): Year song appeared on Billboard charts
- song, Artist (text): Name of song/artist as it appears on Billboard charts
- peak_rank (int): Highest rank the song reached on Billboard charts
- weeks_on_board (int): Total number of weeks the song was on Billboard charts
- spotify_id (text) - Spotify’s ID for the song Spotify_song
- spotify_artist (text): Name of song/artist as it appears on Spotify
- acousticness (float): Confidence measure from 0-1 of whether the track is acoustic
- danceability (float): Describes how suitable a track is for dancing based on a combination of musical features
- duration_ms (int): Duration of the track in milliseconds
- energy (float): A measure from 0-1, represents a perceptual measure of intensity and activity
- instrumentalness (float): Predicts whether a track contains no vocals (Above 0.5 usually means it’s instrumental)
- key (int): key the track is in (Integers map to pitches using standard Pitch Class notation)
- liveness (float): Detects the presence of an audience in the recording. (>0.8 more likely to be live)
- loudness (float): overall loudness of a track in decibels (dB) (Values typically -60-0 db)
- mode (int): Indicates the modality (major - 1 or minor - 0) of a track, the type of scale from which its melodic content is derived
- speechiness (float): Detects the presence of spoken words in a track. Closer to 1 if more speech-like, 0.33-0.66: music and speech,<.33: music/non-speech
- tempo (float): Overall estimated tempo of a track in BPM.
- time_signature (int): Estimated overall time signature of a track which specifies how many beats are in each bar (or measure)
- valence (float): A measure from 0-1 describing the musical positiveness conveyed by a track. High → more positive, low → more negative

The key we used was (song,artist). To join our dataframes, we used spotify_ids. All fields are required.

## Observation of the data prior to cleaning the data:
The raw database of Billboard songs had 24,376 data points. The number of data points per year varied based on the number of songs that charted that year, but each had more than 75. Once this data was cleaned, we queried the Spotify API for information about 4,725 songs. In the kaggle dataset, some entries were missing values for the rank in the previous week, but we are only concerned with the peak rank, so this is not an issue. The database included entries from each week, so each song had entries for every week it was on the charts. The earliest date is August 3rd, 1958 and the latest date is June 11th, 2021. We didn’t include songs from 2021 as the year was not yet complete at the time of the project. The peak rank ranges from 1 to 100, and the weeks on board ranges from 1 to 87; both are heavily skewed towards lower numbers. The date field that was given in the Kaggle dataset was a string in the form of year-month-date, so we parsed the string to only keep the year. We joined the data scraped from the kaggle billboard dataset with the data from the spotify data set. We joined the tables at first on song name and artist to find the corresponding spotify ID’s to each song from the billboard’s dataset. Not all 4,725 songs had ids that were found by our code. Once we were certain we found the right spotify ID’s, we joined the spotify data (danceability, duration, etc.) with the billboard data on the spotify id.

## Data cleaning process and outcome:
For each step that we took to clean and process:

Billboard Step: Each song had an entry in the dataset for each week it was on the chart. We took only the latest entry for each song because we wanted the highest peak rank and weeks on the chart. We then chose the top 75 songs from each year, since we were interested in analyzing the most popular songs. After cleaning, we have 4,725 data points, which is enough data. What we have after cleaning is only representative of the most popular Billboard songs. The songs not included were not the most popular songs from that year, so they were unnecessary for our analysis.

Spotify Step: In order to search for spotify ids, we cleaned the song and artist by removing special characters and other things that made spotify’s search mad. After this, there were still about 400 songs that were not found, which we split up and searched for manually. Some old songs were hard to find as their name/artist might be slightly different now, which was hard to control with one pattern. After this cleaning, only 56 songs were not found, and these rows were dropped. There is thus a slight sampling bias in favor of most popular Billboard songs that are on Spotify. In our final dataset, we have 4,669 songs, which is enough data to perform our analysis on. The songs that were not on Spotify and therefore removed from our dataset were almost all songs before 2000. The average year of the removed songs was 1992. There were no years when more than 3 songs were removed, and the songs removed were distributed fairly evenly between 1980 and 2000. This tells us that our song distribution is slightly skewed towards more recent music.

## Train-test data descriptions:
Our complete final dataset (before splitting) contained about 75 songs per year, for years between 1958 and 2020, 4,669 songs total. The shape of our training set is (3724, 25) and the shape of our test set is (945, 25). This means that our testing set includes 945 songs and our training set includes 3,724 songs (20% and 80% of the data, respectively). To select our test set, we took a random sample of fifteen songs from each year. Since we want to look at trends over time, we wanted an equal distribution of songs over the years rather than just picking an arbitrary 20% of songs from our dataframe. With our data in a pandas dataframe, we looped through each year and took out our random sample of songs and added them to a separate dataframe. We then dropped each of those songs from the original dataframe. This left us with one dataframe containing our test set and one dataframe containing our training set.

## Socio-historical context & impact report:
The major stakeholders of our project are people who are interested in music trends over time. This covers a wide variety of different demographic groups, with a particular focus on Spotify users and people who enjoy pop music, as those are the sources of our data. Our data does not include information about specific people or Spotify users. All of our data is widely available online and refers to commercial music releases. We do not foresee any ethical issues related to privacy in the course of our project. Although our dataset contains songs dating back to the 1950s, any analysis done on the dataset is only really relevant to the U.S since songs that reach the Billboard charts are largely due to their popularity in the U.S and not elsewhere in the world. Hence, our dataset may contain underlying americentrism, especially since only recently has global music been topping billboard charts due to the rise of latin-pop and kpop. Thus, even though we may be tempted to analyse musical trends through the years, we must be careful to specify that the trends we are analyzing are largely related to American listeners. Moreover, many of the older songs have odd titles (i.e. titles with two song names in them) that cannot be found on Spotify. Those songs that we could not find on Spotify were removed from the dataset. This means that our data may contain music that better represents the taste of music-consumers in the more recent years. This bias may be potentially mitigated if we somehow searched for the statistics of these songs manually and inserted them back into our data table. There have been previous works about analyzing music trends, including academic works looking at data accumulated from many sources, and websites that allow Spotify users to look at metrics for their own libraries ([here](https://royalsocietypublishing.org/doi/10.1098/rsos.171274 ), [here](https://journals.sagepub.com/doi/full/10.1177/0305735617748205), and [here](https://escholarship.org/content/qt96b901t6/qt96b901t6_noSplash_929973e502e1e8745c9f9a35fae5ce8e.pdf)) Our project is specifically focused on the popularity of audio features of the music, provided by Spotify, over a long period of time, in the United States.  

## Exploratory Analysis Presentation
[Exploratory Analysis Presentation](https://drive.google.com/file/d/1EmlwilUBlMd1UXtdd9lcaLY5TiMSHYID/view?usp=sharing)

## Final Presentation
[Final Presentation](https://drive.google.com/file/d/1xEdGk8dW4UQMDkh5doJDflZc6BzWh7HC/view?usp=sharing)
