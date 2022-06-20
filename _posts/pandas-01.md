---
title: Pandas学习笔记(一)
date: 2022-06-15 10:32:29
tags: Pandas
---

# Access Row
```python
df.iloc[i] # Access i'th Row
```
# Access Column
# Convert PySpark DataFrames to and from pandas DataFrames
```python
import numpy as np
import pandas as pd

# Enable Arrow-based columnar data transfers
spark.conf.set("spark.sql.execution.arrow.enabled", "true")

# Generate a pandas DataFrame
pdf = pd.DataFrame(np.random.rand(100, 3))

# Create a Spark DataFrame from a pandas DataFrame using Arrow
df = spark.createDataFrame(pdf)

# Convert the Spark DataFrame back to a pandas DataFrame using Arrow
result_pdf = df.select("*").toPandas()
```
[https://docs.microsoft.com/en-us/azure/databricks/spark/latest/spark-sql/spark-pandas](https://docs.microsoft.com/en-us/azure/databricks/spark/latest/spark-sql/spark-pandas)
