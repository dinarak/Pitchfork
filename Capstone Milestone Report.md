# What would a music label do? 
## Analysis of Pitchfork music album reviews.

Given Pitchfork music record reviews written by their journalists in the last 18 years (https://www.kaggle.com/nolanbconaway/pitchfork-data) and other resources on musical artists and records (e.g. https://developer.musicgraph.com/, https://labrosa.ee.columbia.edu/millionsong/lastfm#desc):
What should a music label company do if all they want is an excellent review on Pitchfork?

1.	How can we predict if an artist will get an excellent review on Pitchfork?
    -	based on the artist, genre, music label, and NLP-related factors such as words used in album titles. 
2.	Can we predict what the review of an artist will be based on reviews of their previous work?
3.	Can we extend this problem to predicting album sales and/or stream numbers on popular streaming platforms?

**Client:** music label company.

### Initial Data

The data covers Pitchfork reviews written by their journalists / contributors over the period of January 5, 1999 to January 8, 2017, and totals over 18,000 reviews. 

Data was scraped by Nolan Conaway and uploaded to Kaggle. It was in the format of SQLite database with multiple tables: 
* artists
* content
* genres
* labels
* reviews
* years

### Data dictionary
(compiled from the dataset's Kaggle page)

Field name	| Description |	Format
----------- | ----------- | -------
`reviewid`	| Review unique key	 | Numeric
`artist` |	Name of the recording artist |	String
`author` |	Name of the reviewer |	String
`author_type` |	Reviewer type Staff are given their specific titles while contributors are listed as "Contributor" |	String
`best_new_music` |	Is this title in the category 'Best new music'? <br> Yes: 1 <br> No: 0 |	Numeric
`content` |	Text of the review |	String
`genre` |	Genre of the release |	String
`label` |	Label (publisher) of the release |	String
`pub_date` |	Review publication date |	String
`score` |	Score on a 10-point scale |	Numeric
`title` |	Name of the album |	String
`url` |	Review URL |	String
`year` |	Year of album release. <br> Sometimes Pitchfork reviews albums from the previous year (usually in early January), or from the forthcoming year (usually in December). Likewise, Pitchfork often reviews reissues and lists the original release year along with the reissue year. |	Numeric

### Features we were interested in initially

**Dependent variable:** `score`. Depending on availability of data, we could also try to predict album sales and/or online streaming numbers.

**Independent variables:** `artist`, `author`, `content`, `genre`, `label`, `pub_date`, `title`. 

### Cleaning the data (Jupyter notebook [here](https://github.com/dinarak/Pitchfork/blob/master/Pitchfork%20data%20wrangling%20-%20for%20submission.ipynb))

1. I pulled the data into a dataframe:
 - joined the SQLite the tables `content`, `genres`, `labels`, `reviews`, `years` on review ID. (The `reviews` table already had info on the artists, so there was no need to join the `artists` table to the rest of the tables.)
 - and extracted the data into a dataframe.
2. Explored the dataset with [Pandas Profiling] (https://github.com/pandas-profiling/pandas-profiling/blob/master/README.md), and found that there are a number of duplicate entries. Removed the duplicates.
3. Did a quick clean-up to make sure there's only one row per review ID in the dataframe: 
    - grouped the dataframe by review ID and URL
    - and for each such tuple made sure to keep a row that had the most non-null values.
4. 13th element in the notebook    
    
