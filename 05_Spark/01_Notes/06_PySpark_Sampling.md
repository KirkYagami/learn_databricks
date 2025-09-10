
![alt text](../../../assets/images/pyspark_probabilistic_sampling.png)

---

# PySpark Sampling:

---

## 1. Introduction to Sampling

**Sampling** is the process of selecting a subset of data from a larger dataset. In big data contexts, sampling is crucial for:

- **Data exploration** and analysis on manageable datasets
- **Model training** with representative subsets
- **Testing** and development with smaller datasets
- **Performance optimization** by reducing computational overhead

PySpark provides several sampling methods, each with different characteristics and use cases.

---

## 2. Types of Sampling

### 2.1 Simple Random Sampling

- Each row has an equal probability of being selected
- No replacement by default
- Most common type in PySpark

### 2.2 Stratified Sampling

- Maintains proportional representation across different groups
- Useful when your data has distinct categories
- Ensures each stratum is represented

### 2.3 Systematic Sampling

- Selects every nth row from the dataset
- Not directly available in PySpark but can be implemented

### 2.4 Cluster Sampling

- Samples entire partitions or clusters
- Useful for distributed computing scenarios

---

## 3. The `sample()` Method Deep Dive

### 3.1 Method Signature

```python
DataFrame.sample(withReplacement=False, fraction=None, seed=None)
```

### 3.2 Parameters Explained

#### `withReplacement` (Boolean)

- **False** (default): Each row can only be selected once
- **True**: Rows can be selected multiple times

#### `fraction` (Float between 0 and 1)

- **Key Point**: This is NOT a percentage of rows to return
- **Reality**: Probability that each row will be selected
- **Example**: `fraction=0.3` means each row has a 30% chance of selection

#### `seed` (Integer, optional)

- Controls randomness for reproducible results
- Same seed = same sample every time
- Different seeds = different samples

---

## 4. Probabilistic vs Deterministic Sampling

### 4.1 Probabilistic Sampling (fraction parameter)

**How it works:**

- Each row undergoes a random test
- If random number < fraction, row is included
- Result size varies each time

**Example:**

```python
# 14 rows, fraction=0.3
# Expected: 14 × 0.3 = 4.2 rows
# Actual: Could be 0-14 rows (typically 2-6)

df = spark.createDataFrame([(i,) for i in range(14)], ["id"])
sample1 = df.sample(fraction=0.3, seed=42)
sample2 = df.sample(fraction=0.3, seed=43)

print(f"Sample 1 count: {sample1.count()}")  # Might be 3
print(f"Sample 2 count: {sample2.count()}")  # Might be 5
```

**Statistical Distribution:**

- Follows binomial distribution: B(n, p)
- n = total rows, p = fraction
- Mean = n × p
- Variance = n × p × (1-p)

### 4.2 Fixed Sample Size (Alternative Methods)

**Important:** DataFrame.sample() doesn't have a `num` parameter. For exact row counts, use these approaches:

**Method 1: Using `takeSample()` on RDD**

```python
# Convert to RDD, sample, then back to DataFrame
rdd_sample = df.rdd.takeSample(withReplacement=False, num=4, seed=42)
sample_df = spark.createDataFrame(rdd_sample, df.schema)
```

**Method 2: Using `limit()` with shuffling**

```python
from pyspark.sql.functions import rand
# Shuffle and take top N
sample_df = df.orderBy(rand(seed=42)).limit(4)
```

**Method 3: Using window functions**

```python
from pyspark.sql.functions import rand, row_number
from pyspark.sql.window import Window

# More complex but handles large datasets well
window = Window.orderBy(rand(seed=42))
sample_df = df.withColumn("row_num", row_number().over(window)) \
             .filter(col("row_num") <= 4) \
             .drop("row_num")
```

---

## 5. Common Misconceptions

### 5.1 Misconception: "fraction=0.3 means 30% of rows"

**Reality:** Each row has a 30% probability of selection

### 5.2 Misconception: "Results should be exactly predictable"

**Reality:** Probabilistic sampling introduces natural variation

### 5.3 Misconception: "Sampling is always uniform across partitions"

**Reality:** Each partition's contribution can vary

### 5.4 Misconception: "Larger datasets give more predictable percentages"

**Reality:** Larger datasets do converge closer to expected fraction due to Law of Large Numbers

---
