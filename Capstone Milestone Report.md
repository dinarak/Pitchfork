# What would a music label do? 
## Analysis of Pitchfork music album reviews.

Given Pitchfork [music record reviews](https://www.kaggle.com/nolanbconaway/pitchfork-data) written by their journalists in the last 18 years, and other resources on musical artists and records (e.g. [MusicGraph](https://developer.musicgraph.com/) and [Million Song Dataset](https://labrosa.ee.columbia.edu/millionsong/lastfm#desc)):

**What should a music label company do if all they want is an excellent review on Pitchfork?**

1.	How can we predict if an artist will get an excellent review on Pitchfork?
    -	based on the artist, genre, music label, and NLP-related factors such as words used in album titles. 
2.	Can we predict what the review of an artist will be based on reviews of their previous work?
3.	Can we extend this problem to predicting album sales and/or stream numbers on popular streaming platforms?

**Client:** music label company.

## Data at the outset

### Overview
The data covers Pitchfork reviews written by their journalists / contributors over the period of January 5, 1999 to January 8, 2017, and totals over 18,000 reviews. 

Data was scraped by Nolan Conaway and uploaded to Kaggle. It was in the format of SQLite database with multiple tables: 
* `artists`
* `content`
* `genres`
* `labels`
* `reviews`
* `years`

### Data dictionary
(compiled from the dataset's Kaggle page)

Field name	| Description |	Format
:---         |     :---      |          :--- 
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

## Cleaning the data 
### Jupyter notebook [here](https://github.com/dinarak/Pitchfork/blob/master/Pitchfork%20data%20wrangling%20-%20for%20submission.ipynb)

1. I pulled the data into a dataframe:
    - joined the SQLite the tables `content`, `genres`, `labels`, `reviews`, `years` on review ID. (The `reviews` table already had info on the artists, so there was no need to join the `artists` table to the rest of the tables.)
    - and extracted the data into a dataframe.
2. Explored the dataset with [Pandas Profiling](https://github.com/pandas-profiling/pandas-profiling/blob/master/README.md), and found that there are a number of duplicate entries. Removed the duplicates.
3. Did a quick clean-up to make sure there's only one row per review ID in the dataframe. In case we need more granularity on data, we could pull up the specific tables from SQLite database again, but this will do for now.
    - grouped the entries by review ID and URL
    - and for each such tuple made sure to keep a row that had the most non-null values.
4. Removed rows with empty strings in `content` column. 

## Initial findings

### Pitchfork vs Billboard
For the data story exercise, I pulled in Billboard charts data ([Billboard data import and cleaning](https://github.com/dinarak/Pitchfork/blob/master/Downloading%20and%20munging%20Billboard%20data.ipynb)) from the last 20 years to take a look at Pitchfork through commercial success lens. After all, the assumption is that every music label's dream is to be an overall crowd-pleaser: both to the general public (Billboard ratings), and the indie critics (Pitchfork).

Full data story is in [this Jupyter notebook](https://github.com/dinarak/Pitchfork/blob/master/Data%20Story_Billboard%20Top%20Albums%20vs%20Pitchfork%20album%20reviews.ipynb), but here are the main findings:

1. Only about 9.4% of albums reviewed by Pitchfork ever appear on Billboard 100 Top Album Sales Chart. Pitchfork definitely lives up to its indie image!
2. Over half of these albums first appear on Billboard within 2-3 weeks after the Pitchfork review. 
3. Among the albums that Pitchfork reviewed, the one that spent the most number of weeks on Billboard albums chart: Eminem's [*Curtain Call: the Hits*](https://pitchfork.com/reviews/albums/2773-curtain-call-the-hits/). Pitchfork reviewed it a couple of weeks before it debuted on Billboard, and the review is less than complimentary.
4. Pitchfork does not review a lot of albums that are on Billboard, but for the ones it does, the mean and median scores are slightly lower than for its entire body of reviews, and standard deviation is higher.
5. Drake, Beyonce, and Kendrick Lamar were the top three artists appreciated both by the general public (i.e. earned high ratings on Billboard) and the more indie Pitchfork between 1999 and 2016! 
6. Rap was the most crowdpleasing genre for both general public and indie folks, which is a surprise for someone who thought it was rock or rock-adjacent genres.

### Descriptive and Inferential Stats on Pitchfork data

1. Pitchfork review scores are described as follows:

    Stat | Number
    :------- | ------:
    count   | 18379
    mean    | 7.00
    std     | 1.29
    min     | 0.00
    25%     | 6.40
    50%     | 7.20
    75%     | 7.80
    max     | 10.00

    With the following boxplot:

    ![Alt text](https://user-images.githubusercontent.com/14901467/39159138-a24f50fe-4729-11e8-855c-20b887fabaa0.png)



*The rest of the findings are from [this Jupyter notebook](https://github.com/dinarak/Pitchfork/blob/master/Pitchfork_Inferential_Statistics.ipynb)*. 
*I accepted alpha = .05, but these results would hold up for an alpha that is significanlty lower than that. In fact, the highest p-value I got was 4.398005412824096e-24.*

   

2. Experimental genre of music tends to recieve, on average, higher scores from Pitchfork. 
3. Music labels with 30+ reviews, who were in the top 10% by median score, receive statistically significantly higher ratings than the rest of the labels. These labels are: *'rhino', 'light in the attic', 'profound lore', 'matador', 'emi', 'editions mego', 'epitaph', 'constellation', 'relapse', and '4ad'.*
4. Top ten authors by median album score give on average higher review scores. This result is also statistically significant. The authors in question are: Jenn Pelly, Seth Colter Walls, Mark Richardson, Grayson Haver Currin, David Drake, Philip Sherburne, Andy O'Connor, Andy Beta, Amanda Petrusich, and Marc Masters.

## Ideas for further analysis

1. Figure out a way to predict how a particular album will be scored if no information on the review author was known. 
2. Compare it to the prediction that takes into account the author. Does the author make a difference in the score? Would a music label be better off pitching their albums to particular Pitchfork contributors?
