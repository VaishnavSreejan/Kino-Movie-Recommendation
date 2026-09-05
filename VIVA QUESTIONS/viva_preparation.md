# KTU S5 Big Data - VIVA Preparation 

### 1. Why did you use PySpark instead of Pandas?
Pandas is designed to run on a single machine's RAM, which can crash when handling massive files. We used PySpark because it is a distributed computing framework. It partitions the 20 million rows of data and processes them in parallel across clusters, making it vastly faster and more scalable than Pandas.

### 2. What are DataFrames in Spark?
DataFrames are distributed collections of data organized into named columns, much like tables in a relational database. We used them instead of core RDDs because DataFrames are optimized by Spark's Catalyst engine. This allowed us to easily use SQL-like commands (like groupBy and join) to aggregate our 20 million ratings.

### 3. Why did you choose HBase over traditional MySQL?
Big Data requires databases that scale horizontally and handle sparse data well. HBase is a NoSQL, distributed database built on top of Hadoop. Instead of rigid tables, it uses flexible "Column Families", allowing us to fetch movie statistics natively and rapidly without complex table joins.

### 4. What are Column Families in your project?
In HBase, data is stored in Column Families rather than traditional columns. We designed three families: `info` (for titles and genres), `stats` (for average ratings and vote counts), and `links` (for TMDb URLs). This design groups related data together, speeding up read times for our web application.

### 5. What is the IMDB Weighted Rating and why use it?
Average rating alone is flawed because a movie with only one 5-star rating would beat a masterpiece with 10,000 4.8-star ratings. We used the IMDB Weighted Rating formula to fix this bias. It balances a movie's average score against the dataset's global average, placing higher importance on movies with a larger number of votes.

### 6. How did you connect HBase to your Python backend?
We used the `happybase` Python library to communicate with HBase. It connects directly to the HBase Thrift Server on port 9090. This allowed our FastAPI backend to quickly read the NoSQL rows, convert them to JSON, and serve them to the web interface.

### 7. Fetching 27,000 posters takes too long. How did you optimize it?
Normal sequential API requests would take several hours to fetch 27,000 posters. We solved this by using Python's `ThreadPoolExecutor` for asynchronous programming. By running 20 concurrent network threads at the same time, we reduced the processing time from hours down to just minutes.

### 8. How does the "Mood Search" work?
Instead of complex Machine Learning, we built an NLP-based keyword mapping dictionary. When a user types a mood like "scary" or "romantic", the system parses the text and maps those keywords to specific database genres (like Horror or Romance). It then instantly filters the dataset to return movies matching that emotional intent.

### 9. What happens if your Hadoop/HBase cluster crashes?
We designed the system with a fault-tolerant dual-serving architecture. If the backend detects that the HBase Thrift connection has failed, it catches the error and gracefully falls back to reading a localized CSV file. This guarantees that the web application stays live and fully functional even during database downtime.

### 10. Why use FastAPI instead of Flask or Django?
FastAPI is a modern, high-performance web framework. We chose it because it natively supports asynchronous programming (`async/await`), making API endpoints incredibly fast when querying our dataset. It is also much lighter than Django and automatically generates API documentation.
