```python
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType, ArrayType, MapType, DateType
from datetime import date

# Initialize Spark Session
spark = SparkSession.builder.appName("SuperheroesDataset").getOrCreate()

# Define Schema
schema = StructType([
    StructField("name", StringType(), True),
    StructField("universe", StringType(), True),              # Marvel or DC
    StructField("alias", StringType(), True),
    StructField("debut_year", IntegerType(), True),
    StructField("powers", ArrayType(StringType()), True),     # List of powers
    StructField("attributes", MapType(StringType(), StringType()), True),  # key-value traits
    StructField("team", ArrayType(StringType()), True),       # List of teams or affiliations
    StructField("first_appearance", DateType(), True)
])

# Sample Data
data = [
    ("Spider-Man", "Marvel", "Peter Parker", 1962,
     ["Wall-Crawling", "Spider-Sense", "Super Strength"],
     {"intelligence": "high", "morality": "heroic"},
     ["Avengers", "Fantastic Four"], date(1962, 8, 1)),

    ("Iron Man", "Marvel", "Tony Stark", 1963,
     ["Genius Intellect", "Powered Armor Suit"],
     {"wealth": "billionaire", "charisma": "high"},
     ["Avengers"], date(1963, 3, 1)),

    ("Thor", "Marvel", "Thor Odinson", 1962,
     ["Weather Control", "Super Strength", "Immortality"],
     {"god": "yes", "morality": "noble"},
     ["Avengers"], date(1962, 8, 1)),

    ("Hulk", "Marvel", "Bruce Banner", 1962,
     ["Super Strength", "Regeneration"],
     {"anger_level": "high", "intellect": "genius"},
     ["Avengers", "Defenders"], date(1962, 5, 1)),

    ("Captain America", "Marvel", "Steve Rogers", 1941,
     ["Enhanced Agility", "Shield Mastery"],
     {"loyalty": "extreme", "bravery": "legendary"},
     ["Avengers"], date(1941, 3, 1)),

    ("Batman", "DC", "Bruce Wayne", 1939,
     ["Martial Arts", "Stealth", "Genius Intellect"],
     {"wealth": "billionaire", "fear_factor": "high"},
     ["Justice League", "Bat Family"], date(1939, 5, 1)),

    ("Superman", "DC", "Clark Kent", 1938,
     ["Flight", "Heat Vision", "Super Strength"],
     {"planet": "Krypton", "symbol": "hope"},
     ["Justice League"], date(1938, 6, 1)),

    ("Wonder Woman", "DC", "Diana Prince", 1941,
     ["Super Strength", "Lasso of Truth", "Flight"],
     {"origin": "Amazon", "wisdom": "immense"},
     ["Justice League"], date(1941, 12, 1)),

    ("Flash", "DC", "Barry Allen", 1956,
     ["Super Speed", "Time Travel"],
     {"speed_force": "yes", "science_skill": "high"},
     ["Justice League"], date(1956, 10, 1)),

    ("Aquaman", "DC", "Arthur Curry", 1941,
     ["Water Manipulation", "Talk to Sea Creatures"],
     {"king": "Atlantis", "strength": "superhuman"},
     ["Justice League"], date(1941, 11, 1)),

    ("Black Panther", "Marvel", "T'Challa", 1966,
     ["Enhanced Senses", "Vibranium Suit"],
     {"nation": "Wakanda", "leadership": "king"},
     ["Avengers"], date(1966, 7, 1)),

    ("Green Lantern", "DC", "Hal Jordan", 1940,
     ["Energy Constructs", "Flight"],
     {"willpower": "strong", "sector": "2814"},
     ["Justice League", "Green Lantern Corps"], date(1940, 7, 1)),

    ("Doctor Strange", "Marvel", "Stephen Strange", 1963,
     ["Magic", "Time Manipulation"],
     {"title": "Sorcerer Supreme", "dimension": "Multiverse"},
     ["Avengers", "Defenders"], date(1963, 7, 1)),

    ("Scarlet Witch", "Marvel", "Wanda Maximoff", 1964,
     ["Chaos Magic", "Reality Manipulation"],
     {"mutant": "yes", "emotion": "volatile"},
     ["Avengers"], date(1964, 3, 1))
]
# Create DataFrame
df = spark.createDataFrame(data, schema)

# Show the DataFrame
df.show(truncate=False)
df.printSchema()

```