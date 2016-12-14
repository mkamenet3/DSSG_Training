#Notes from 2016 Data Science for Social Good Fellowship

Full curriculum: <https://github.com/dssg/hitchhikers-guide/tree/master/curriculum>

##USEFUL COMMAND LINE Tools

Download from website
```
curl -O websitename
```
see permissions
```
ls -la
```

unzip a zip file
```
gunzip mycsv.csv.gz
```
exploring a csv
```
head 2016.csv
```

just look at first 3 entries
```
head -n 3 2016.csv
```

using csvlook
```
head 2016.csv | csvlook
```

doing .txt
```
head myfile.txt | csvlook -H
```

I want to look something up with a keyword
```
grep CHICAGO myfile.txt | csvlook -H
```

extract first column and explore
```
cut -d, -f 1 2016.csv | head
```

now, do above and extract observations from OHare
```
cut -d , -f 1 2016.csv | grep USW00094846 | head
```

word count
```
wc -l something.txt
```

sed = streaming editor
- useful for large text files because doesn't have to load everything into memory to make changes

example: replace PRCP with RAIN in a .csv
```
sed s/PRCP/RAIN/ 2016.csv | head
```
but this doesn't change the actual file, we need to write to the file to save changes
```
sed s/PRCP/RAIN/ 2016.csv > 2016_clean.csv
```

man pages in cygwin
```
curl --manual
```

We can also use `awk` for subsitution, but this time, let's replace "WSFM" with "WINDSPEED" in all the weather files in the directory. Once again, stackoverflow is your friend here.
```
ls -la > files.txt
awk '$9 ~/2016*/ {gsub(/WSFM/, "WINDSPEED"); print;}' files.txt
```

##Useful Code tidbits

SSH into a project with a key
```
ssh -i _pathtosshkey_ username@server
```

Commonly used Git code:
```
 git clone "newrepo"
 git remote add upstream "repo name"
 git remote -v
 git remote update
 git log --decorate
```
Git code that's good for trouble-shooting when working on a team (but use with caution and double-check what it all does (esp. rebase))
```
 git remote update
 git status
 git rebase upstream/master
 git status
 git log --decorate --oneline
 git push
 git log --decorate --oneline --graph
```
You will git commit, git push, git pull between the GitHub versions and local copies

 Using pip with Python:
 ```
 pip freeze
 pip freeze > requirements.txt
 pip3 install -r /path/requirements.txt
 ```


Running jupyter notebook on a server:
 ```
 ssh -L NUMBER:localhost:NUMBER user@server
```
Where `NUMBER` is some number between 10000 and 60000 (just choose it); USERNAME is your username, and BOX is your project’s box. On the prompt, type:

`jupyter notebook --port NUMBER --no-browser`

And then in a web browser on your machine, open the page `localhost:NUMBER`

##Databases Tutorial
Bash code:
```
head -n 100 filename.csv | csvcut -c 1,9 | csvlook
head -n 100 filename.csv | csvlook | less -S
```

Python code:
```
ipython
import pandas as pd
pd.readcsv()

.columns.values()
```

PSQL stuff:
```
cat mycsv.csv | psql -c "\copy raw.mycsv from stdin with csv header;"
```

##Functional Programming in Python
In python: """This is a doc string that should document a python function"""
Ex:
```
	"""Returns factors of input x
	:param int x: the number to factors
	:return: a list of facotrs
	:rtype: list[int]

	:param list[list] the input: List contaning lists to flatten
	:param int max_length:
	:return: flattened, uniquified, filtered list
	:rtype list



# if it returns two floats:
	:rtype: (float, float)

```
<http://khwilson.github.io/#15>

##Port Forwarding with Jupyter Notebooks

connecting to jupyter notebook through the kernel:
```
ssh -i _pathtosshkey_ -L #:localhost:# user@server
```
```
jupyter notebook --port 12848 --no-browser
```

##More Python and Git Tidbits:

```
git fetch
git log --decorate --oneline --graph
```
Try-except errors in python:
```
try:
	""
except:
```


##Drake
```
Drakefile _______ +=... #(force drake to run)
```


##GIS
Types of files:
- .shp/.dbf/ .
- vector data and other
- raster data
	- image on a grid (image formed by pixels)


##RECORD-LINKAGE AND MAPPING

- first consider if it's m:1, 1:m, or 1:1
- confidence scores on linkages
	- whenever you match someone, give it a confidence level (even if it's hard-coded)
	- Ex: perfect match = 1; address not sure give it a 0.9
- 3 types of matching:
	- exact matching
	- rule-based matching
		- thousands of rules for matching
	- probabilistic linkage
- Most common:
	- edit distance: number of insertions; deletions required to make things the same
	- sounds
		- phonetic system
- Fuzzy Matching
	- Blocking (to avoid all cross-product comparisons)
		- pick an attribute you block on
		- You create a bunch of subsets on a block
			- Ex: year/decade of birth; people in the same state; first letter of first name and first 3 letters of last name
		- Usually block on multiple things to bring down computation times
		- Goal: Minimize number of pair-wise matches
		- Blocks are rule-based
	- Can use a rule to generate a positive and negative but then classification algorithm will just over-fit
- Machine Learning-Based Record LINKAGE
	- 1) Generate training data
		- label pairs as match/no match
	- 2) Generate features over each pair-wise
		= Distance metrics over different attributes(fname, lname, dob)
		- Tfidf (words that occur often get high score and rare words get high score)
	-3) Build classifiers



##DEDUPE
- <https://github.com/datamade/csvdedupe>
- <https://dedupe.readthedocs.io/en/latest/>
- Default metric (affine; Levenstein distance)
- Generally, before doing record-linkage you want to de-duplicate all of the individual datasets


##DATABASES

- csv's don't scale; can't random access
- csv's need to be loaded into memory
	- with databases, you can efficiently access huge data

- RELATIONAL DATABASES
	- mysql has slightly different syntax
	- we use psql which works with postgresql
	- should just de-normalize the tables so you don't do a ton of joins over and over again which take time
	- (Open Source):
		- sqlite; mysql; postgres; ingres
	- Inserts are really slow cuz every record (one at a time) needs to be moved and then re-indexed (very slow)
	- ACID Properties
		- Atomic = all work in transaction completes (commit) or none of it completes
		- Consistent = a transaction transforms the database from one consistent state to another consistent state
		- Isolated = results of any changes made during a transaction are not visible until the transaction has committed
		- Durable = results of committed transaction survive failure
	-CAP Theorem (NoSQL Database)
		- consistency, availability, partitions; can have at most two of these three properties (usually consistency is sacrificed because each query goes to different server; stream-lined)
		- Use NoSQL when:
			- you don't know what your schema will be or if it will be changing a lots
			- easy scaling (ex: T-giving traffic)
			- simplish queries
			- fast read access
	- NoSQL DATABASE TYPES
		- key-value pair
		- document database
		- column database
		- graph database
	- Document Stores
		- typically JSON objects (maybe xml)



##Almost All of Machine Learning

- features = indicators = variables = predictors

#1) Supervised:
	- regression; something with a specific outcome you're trying to predictors
		- labelled data
#2) Unsupervised:
	- clustering/PCA
#3) Weakly Supervised:
	- anomaly detection
	- when you have some labelled and some unlabelled data
	-semi-supervised clustering (some things should always/never be clustered together)

Unsupervised: Clustering
	- K-means is most common
	- Good thing to do first
	- can often figure out how the data looks/works after you try a method
		- esp true in high dimensional data space
	- Mean shift
		- create a window; circle moves around until it finds a dense space
		- does not assume spherical clusters
	- agglomerative clustering
	- divisive clustering
	- spectral clustering
	- PCA:
	- Topic Models

	- association rules: people who buy x also buy y
		- good way to find data quality errors
			- Ex: pregnancy codes and females (was only true 98% of the time - what was wrong with that 2%? Improper entry)

Supervised Learning:
	- training/testing
	- K Nearest Neighbor:
		- need distance function (most often Euclidean)
		- no training required
		- decision boundaries are quite difficult to interpret
		- can be slow
		-need to store all the data
		- extra variables can hurt
		- variables need to be normalized b/c all variables are equally-weighted -> you can get sparse neighbors
			- maybe need to do variable selection prior or iteratively
	- SVM's
		- linear usually works pretty well; you can project things onto non-linear spaces
		- Kernel trick: you can do this mapping (from x to x^2) without actually mapping it
		- Logistic regression maximizes likelihood of the data vs. goal is to separate the two planes (not goal to replicate the outcome)
	- Decision Trees
		- uses information game (change in entropy/randomness)
			- I'm trying to find a feature where if I split it on that thing, that will reduce my entropy the most
	- Bagging (bootstrap aggregation)
		- decision trees work well with bagging; smooths out variance (can be parallelized efficiently)
	- Boosting
		- builds model; tests the model; sees what it got wrong. Things that it got wrong, increase the weight and re-run model (do this iteratively)
			- focus on data points previous model got wrong
	- Random Forests
		- create n bootstrap samples with replacement (same as bagging)
		- sample m <<M columns from each sample		
			- do the same thing for the next level down
			- Ex: start to 100, take 10; as soon as it gets to _________, sample another 10
		- n decision trees with m features, aggregate the trees from the separate trees
		- <https://www.stat.berkeley.edu/~breiman/RandomForests/>

#sql examples

SQL code to flag something by year:

```
select left(month,4) as year
,myID
,case when max(myquantity) > # then true
else false
end as myflag
from
(select left(date::varchar(#),#) as month
, sum(myqty) as qty
,ID
from myschema
group by 1,3) as grouped
group by 1,2
order by ID
```


#vim tricks
```
set number #show number lines
set list	#show all characters (tabs, spaces, etc)
ctrl + [ (same as esc)
a = append (go to last line and add new line)
```


##FEATURE Generation

- anything that might be predictive
- complexity in features allows us to use less complex models
- Classes of features:
	- categorical to binary
	- features for missing values
	- discretization
	-date/time features
	- scaling
	- transformations
	- aggregations
	- interactions **

- Categorical to Binary
		- one vs all (dummy vars)
		- groups
		- presence vs. absence

- Missing Values
	- Be Careful! Investigate why
		- create new indicator (missing_age yes/no)
	- add binary feature for missing vs. not missing
- Discretization
	- equal width bin
	- equal size bins
	- entropy-based bins
	- domain-specific bins

- Feature scaling
	- Deal with outliers first
	- usually good idea to scale features to have similar range

- Feature transformations
	- log (decreasing marginal utility)
	- (square) root
	- squared

- Spatial/Temporal
	- aggregations:
		- count
		- min/max/avg. st.dev
	- date differences (# dates since....)
	- aggregates over different time periods
		- min/max/avf
		- avg spend in past 3 months
	- relative aggregates
	- distances
	- aggregates over different distances
	- seasonality

- Feature Interactions
	- 	generate features for combinations of features
		- age * gender
	- allows you to use linear models but still model non-linear relationships
	- random forests are one way of discovering useful interactions
	- in general, try to keep features interpretable
		- but if you include something that's not interpretable (ex: Box-Cox transformation) and it gives you a lot of power, then
			you should explore this more and investigate why that's happening


#TESTING
<https://github.com/redshiftzero/testing-tutorial>

#######
1) Unit tests vs. integration tests
	- Unit tests
		smaller chunks of code
	-Integration tests
		- pipeline test
- an exception may mean there's something wrong with your test
- don't forget to tear down your tests:
	SetUp and TearDown

setUp() runs before every test and tearDown() runs after every test

```
import unittest
import pipeline

class PipelineTest(unittest.TestCase):
    def setUp(self):
        # Set up some records in the test db

    def test_a_thing_here(self):
        self.assertEquals(foo, bar)

    def test_another_thing_here(self):
        self.assertNotEquals(foo, bar)

    def tearDown(self):
        # Remove records in the test db
```

- Good rule of thumb: if you get a bug, write a test for it; model evaluation code: write a test
