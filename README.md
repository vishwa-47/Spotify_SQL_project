# Spotify_SQL_project

---ADVANCED SQL PROJECT on SPOTIFY dataset



--- create table 

drop table if exists spotify;

create table spotify(
	artist varchar(255),
	track varchar(255),
	album varchar(255),
	album_type varchar(50),
	danceability float,
	energy float,
	loudness float,
	speechiness float,
	acousticness float,
	instrumentalness float,
	liveness float,
	valence float,
	tempo float,
	duration_min float,
	titel varchar(255),
	channel varchar(255),
	views float,
	likes float,
	comments bigint,
	licensed boolean,
official_video boolean,
	stream bigint,
	energy_liveness float,
	most_played_on varchar(50)

	)



select * from spotify





----EDA 

select count(*) from spotify

select count(distinct artist) from spotify

select count(distinct album) from spotify

select distinct album_type from spotify


select max(duration_min) from spotify

select min(duration_min) from spotify


select * from spotify where duration_min = 0


delete from spotify where duration_min = 0



select distinct channel from spotify


select distinct most_played_on, album from spotify group by 1,2



--- data analysis easy category

-- Retrieve the names of all tracks that have more than 1 billion streams.


select * from spotify where stream > 1000000000



-- List all albums along with their respective artists.

select distinct album ,artist from spotify order by 1


-- Get the total number of comments for tracks where licensed = TRUE.


select sum(comments) from spotify where licensed = 'True' 


-- Find all tracks that belong to the album type single.

select album  from spotify where album_type = 'single'



-- Count the total number of tracks by each artist.


select artist,
	count(*)as total_number_of_tracks
	from spotify
group by artist
order by 2 desc



-- Calculate the average danceability of tracks in each album.

select 
	album,
	track,
	avg(danceability) as average_danceability
	from spotify
group by 1,2
order by 3 desc


-- Find the top 5 tracks with the highest energy values.

select distinct track,energy from spotify order by 1 desc limit 5



-- List all tracks along with their views and likes where official_video = TRUE.



select distinct track,views,likes from spotify where official_video = 'true' order by 2 desc


select track,
	sum(views) as total_views,
	sum(likes) as total_likes
from spotify
	where official_video = 'true'
group by 1
order by 2 desc



-- For each album, calculate the total views of all associated tracks.


select 
	album,track,sum(views)
	from spotify
group by 1,2
order by 3 desc



-- Retrieve the track names that have been streamed on Spotify more than YouTube.
select * from 
(
select track,
	coalesce(sum(case when most_played_on = 'Youtube' then stream end),0) as streamed_on_youtube,
	coalesce(sum(case when most_played_on = 'Spotify' then stream end),0 )as streamed_on_spotify

	from spotify
group by 1
)as t1
where  streamed_on_spotify>streamed_on_youtube
and streamed_on_youtube<>0
order by 2


-- Find the top 3 most-viewed tracks for each artist using window functions.

with ranking_artist as
	(
select artist,
		track,
		sum(views) as total_views,
	dense_rank()over(partition by artist order by sum(views)desc)as rank
	from spotify
group by 1,2
order by 1,3 desc
)
select * from ranking_artist where rank <=3





-- Write a query to find tracks where the liveness score is above the average.


select liveness from spotify where liveness >(
	select avg(liveness) from spotify 
)





-- Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.
with difference  as
	(
select album,
	max(energy) as highest ,
	min(energy) as lowest 
from spotify
	group by 1
)

select album, highest - lowest as Difference from difference
order by 1






