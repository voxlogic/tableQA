# tableQA
Tool for querying natural language on tabular data like csvs,excel sheet,etc.

[![Build Status](https://travis-ci.com/abhijithneilabraham/tableQA.svg?branch=master)](https://travis-ci.com/abhijithneilabraham/tableQA)

#### Features    
* Supports detection from multiple csvs
* Support FuzzyString implementation. i.e, incomplete csv values in query can be automatically detected and filled in the query.
* Open-Domain, No training required.
* Add manual schema for customized experience
* Auto-generate schemas in case schema not provided


### Configuration:

##### install via pip:   

```pip install tableqa```

##### installing from source:   

```git clone https://github.com/abhijithneilabraham/tableQA ```  

```cd tableqa```

```python setup.py install```


## Quickstart


#### Getting an SQL query from csv

```
from tableqa.agent import Agent
agent=Agent(data_dir) #specify the absolute path of the data directory.
print(agent.get_query("Your question here")) #returns an sql query
```

#### Do Sample query on database
```
response=agent.query_db("Your question here")  
print("Response ={}".format(response)) #returns the result of the sql query after feeding the csv to the database
```


#### Adding Manual schema

include the directory containing the schemas of the respective csvs, with the same filename. Refer [cleaned_data](tableqa/cleaned_data)  and [schema](tableqa/schema) for examples.

##### Schema Format:
```
{
    "name": DATABASE NAME,
    "keywords":[DATABASE KEYWORDS],
    "columns":
    [
        {
        "name": COLUMN 1 NAME,
        "mapping":{
            CATEGORY 1: [CATEGORY 1 KEYWORDS],
            CATEGORY 2: [CATEGORY 2 KEYWORDS]
        }

        },
        {
        "name": COLUMN 2 NAME,
        "keywords": [COLUMN 2 KEYWORDS]
        },
        {
        "name": "COLUMN 3 NAME",
        "keywords": [COLUMN 3 KEYWORDS],
        "summable":"True"
        }
    ]
}

```
* Mappings are for those columns whose values have only few distinct classes.
* Include only the column names which need to have manual keywords or mappings.Rest will will be autogenerated.
* ```summable``` is included for Numeric Type columns whose values are already count representations. Eg. ```Death Count,Cases``` etc. consists values which already represent a count.



Example (with manual schema):    

##### SQL query
```
from tableqa.agent import Agent
agent=Agent(data_dir,schema_dir) 
print(agent.get_query("How many people died of stomach cancer in 2011")) 
#sql query: SELECT SUM(Death_Count) FROM cancer_death WHERE Cancer_site = "Stomach" AND Year = "2011" 
```


##### Database query

```
response=agent.query_db("how many people died of stomach cancer in 2011")
print("Response ={}".format(response)) #returns the result of the sql query after feeding the csv to the database
#Response =[(22,)]
```
