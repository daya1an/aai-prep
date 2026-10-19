# Data Augmentation Technologies — AAI Exam Notes

This unit covers **big-data processing**, **data transformation/enrichment**, **automated data generation**, and **cloud integration**. The core technologies are **Hadoop**, **Apache Spark**, and **Apache MXNet**, with Python as the primary working language. Hadoop provides distributed storage and batch processing; Spark provides fast in-memory batch and stream processing; MXNet is a scalable deep-learning framework with multi-language support. [hadoop.apache](https://hadoop.apache.org/)

## 1. Big Data Fundamentals

**Big data** refers to datasets too large, fast, varied, or complex for conventional single-machine processing tools.

| Property | Meaning | E-commerce example |
|---|---|---|
| Volume | Huge amount of data | Billions of customer clicks, orders, product views |
| Velocity | Data arrives rapidly | Real-time checkout events and payment streams |
| Variety | Many data formats | JSON orders, images, reviews, clickstream logs |
| Veracity | Data quality/trustworthiness | Duplicate orders, invalid addresses, missing ratings |
| Value | Useful business insight | Recommendations, fraud detection, demand forecasting |

**Key terms:**
- **Batch processing:** Processes stored data in large groups; suitable for nightly sales reports.
- **Real-time/stream processing:** Processes data as it arrives; suitable for fraud alerts during payment.
- **Distributed computing:** Splits work across multiple machines/nodes.
- **Cluster:** Group of interconnected computers working as one system.
- **Node:** One machine in a cluster.
- **Commodity hardware:** Low-cost, standard servers rather than specialized high-end machines.
- **Fault tolerance:** System continues working despite node/disk/network failures.
- **Scalability:** Ability to add nodes to handle more data or load.
- **Horizontal scaling:** Add more machines; typical in Hadoop/Spark clusters.
- **Vertical scaling:** Add CPU/RAM to one machine.

Hadoop is designed to scale from single servers to thousands of machines and handles failures at the application layer rather than relying only on hardware high availability. [hadoop.apache](https://hadoop.apache.org/)

## 2. Apache Hadoop

**Apache Hadoop** is an open-source framework for distributed storage and processing of large datasets across clusters using simple programming models. [hadoop.apache](https://hadoop.apache.org/)

### Core components

| Component | Full form | Role |
|---|---|---|
| HDFS | Hadoop Distributed File System | Distributed, high-throughput storage |
| YARN | Yet Another Resource Negotiator | Resource management and job scheduling |
| MapReduce | — | Parallel batch-processing model |
| Hadoop Common | — | Shared Java libraries and utilities |

Hadoop’s official components include HDFS for storage, YARN for scheduling/resource management, and MapReduce for parallel processing. [hadoop.apache](https://hadoop.apache.org/)

### HDFS

- Stores very large files by splitting them into **blocks**.
- Default HDFS block size is commonly **128 MB** in modern Hadoop versions.
- Each block is replicated across multiple DataNodes for fault tolerance; the common default replication factor is **3**.
- Follows a **master–slave architecture**.

| HDFS daemon | Type | Function |
|---|---|---|
| NameNode | Master | Stores metadata: file names, permissions, block locations |
| DataNode | Worker | Stores actual data blocks |
| Secondary NameNode | Helper | Periodically merges/checkpoints filesystem metadata; **not** a live backup |

**Important distinctions:**
- **NameNode:** Metadata only, not actual customer-order file contents.
- **DataNode:** Stores actual blocks of order logs, product images, etc.
- **Block:** Physical split of a large file.
- **Replication:** Copies of a block on different nodes.
- **Rack awareness:** Places replicas across racks to survive rack failure.

### YARN

YARN separates resource management from application logic, allowing multiple processing engines—not only MapReduce—to run on the same Hadoop cluster. [datacamp](https://www.datacamp.com/blog/hadoop-architecture)

| YARN component | Role |
|---|---|
| ResourceManager | Global resource scheduler and cluster authority |
| NodeManager | Manages containers/resources on an individual node |
| ApplicationMaster | Manages lifecycle of one application/job |
| Container | Allocated CPU + memory unit for a task |

### MapReduce

MapReduce processes data in two main phases:

1. **Map:** Converts input records into intermediate key–value pairs.
2. **Shuffle and Sort:** Groups values by key.
3. **Reduce:** Aggregates values for each key.

**E-commerce example — count orders by city:**

```text
Input:
("Chennai", order1), ("Mumbai", order2), ("Chennai", order3)

Map output:
("Chennai", 1), ("Mumbai", 1), ("Chennai", 1)

Shuffle/Sort:
Chennai -> [1, 1]
Mumbai ->  [hadoop.apache](https://hadoop.apache.org/)

Reduce output:
Chennai -> 2
Mumbai -> 1
```

- **Mapper:** Performs the map phase.
- **Reducer:** Performs aggregation.
- **Combiner:** Optional local mini-reducer that reduces data transferred to reducers.
- **Partitioner:** Decides which reducer receives a key.
- **Shuffle:** Movement/grouping of intermediate outputs.
- **Writable:** Hadoop’s serialization format for keys/values.
- **InputSplit:** Logical division of input data for a mapper.

### Note: 

**InputSplit is logical.**
**HDFS Block is physical.**

Interview-favorite distinction:

```text
HDFS Block  = Physical storage unit

InputSplit  = Logical processing unit
```

---

# Complete MapReduce Flow

```text
Input File
    │
    ▼
InputSplit
    │
    ▼
Mapper
    │
    ▼
Combiner (Optional)
    │
    ▼
Partitioner
    │
    ▼
Shuffle & Sort
    │
    ▼
Reducer
    │
    ▼
Final Output
```

---

# Word Count Example End-to-End

### Input

```text
Hello Hadoop
Hello MapReduce
Hello Spark
```

### Mapper Output

```text
(Hello,1)
(Hadoop,1)
(Hello,1)
(MapReduce,1)
(Hello,1)
(Spark,1)
```

### Combiner Output

```text
(Hello,3)
(Hadoop,1)
(MapReduce,1)
(Spark,1)
```

### Partitioner

Determines which reducer receives each key.

Example:

```text
Hello      -> Reducer 0
Hadoop     -> Reducer 1
MapReduce  -> Reducer 0
Spark      -> Reducer 1
```

### Shuffle & Sort Output

```text
Hello     -> [1,1,1]
Hadoop    -> [1]
MapReduce -> [1]
Spark     -> [1]
```

### Reducer Output

```text
Hello     -> 3
Hadoop    -> 1
MapReduce -> 1
Spark     -> 1
```

---

# Visual Representation

```text
Input File
--------------------------------
Hello Hadoop
Hello MapReduce
Hello Spark
--------------------------------

           │
           ▼

Mapper
--------------------------------
(Hello,1)
(Hadoop,1)
(Hello,1)
(MapReduce,1)
(Hello,1)
(Spark,1)
--------------------------------

           │
           ▼

Combiner (Optional)
--------------------------------
(Hello,3)
(Hadoop,1)
(MapReduce,1)
(Spark,1)
--------------------------------

           │
           ▼

Partitioner
--------------------------------
Assign keys to reducers
--------------------------------

           │
           ▼

Shuffle & Sort
--------------------------------
Hello     -> [1,1,1]
Hadoop    -> [1]
MapReduce -> [1]
Spark     -> [1]
--------------------------------

           │
           ▼

Reducer
--------------------------------
Hello     -> 3
Hadoop    -> 1
MapReduce -> 1
Spark     -> 1
--------------------------------
```

---

# Common Interview Question

### Q: Which component actually groups all values belonging to the same key?

**Answer:** **Shuffle & Sort Phase**

The Reducer does **not** group the values.

The **Shuffle & Sort** phase:

1. Transfers data from mappers to reducers.
2. Sorts keys.
3. Groups values belonging to the same key.

By the time data reaches the Reducer, it is already grouped:

```text
Hello -> [1,1,1]
Hadoop -> [1]
MapReduce -> [1]
Spark -> [1]
```

The Reducer simply performs aggregation on these grouped values.

### Hadoop ecosystem tools

| Tool | Purpose |
|---|---|
| Hive | SQL-like querying over Hadoop data; HiveQL |
| Pig | High-level scripting platform for data flows |
| HBase | NoSQL, column-oriented distributed database |
| Sqoop | Transfers data between RDBMS and Hadoop |
| Flume | Collects, aggregates, moves streaming log data |
| Oozie | Workflow scheduler for Hadoop jobs |
| ZooKeeper | Distributed coordination and configuration service |
| Kafka | Distributed event-streaming platform |

**Likely one-mark facts:**
- HDFS = storage; YARN = resource management; MapReduce = processing.
- NameNode stores metadata; DataNode stores blocks.
- HDFS is optimized for **high throughput**, not low-latency random reads.
- Hadoop is written primarily in **Java**.
- Hadoop is best for **batch processing**.

## 3. Apache Spark

**Apache Spark** is a fast, general-purpose distributed computing engine for large-scale data processing. It improves on MapReduce by keeping intermediate data in memory, reducing disk I/O.

### Spark architecture

| Component | Purpose |
|---|---|
| Driver | Runs the main program, creates SparkContext/SparkSession, builds execution plan |
| Cluster Manager | Allocates resources: Standalone, YARN, Kubernetes, Mesos |
| Executor | Runs tasks on worker nodes and stores cached data |
| Task | Smallest unit of work executed on one data partition |
| Job | Triggered by an action |
| Stage | Group of tasks based on shuffle boundaries |

### Core data abstractions

| Abstraction | Description |
|---|---|
| RDD | Resilient Distributed Dataset; immutable, fault-tolerant distributed collection |
| DataFrame | Distributed collection organized into named columns; similar to SQL table |
| Dataset | Typed DataFrame API; mainly Scala/Java, not native Python |
| Spark SQL | SQL and DataFrame processing engine |
| Structured Streaming | Scalable, fault-tolerant stream processing built on Spark SQL |

Spark supports DataFrame/Dataset operations in Python, Java, Scala, and R; Structured Streaming uses a micro-batch engine by default and can achieve low end-to-end latency with exactly-once fault-tolerance guarantees. [spark.apache](https://spark.apache.org/docs/latest/streaming/index.html)

### RDDs

- **RDD** stands for **Resilient Distributed Dataset**.
- RDDs are **immutable**: transformations create new RDDs.
- RDDs are **lazy**: transformations are not executed until an action occurs.
- RDDs recover lost partitions using **lineage**, the sequence of transformations used to create them.

**Transformations vs actions:**

| Type | Definition | Examples |
|---|---|---|
| Transformation | Creates a new RDD/DataFrame; lazy | `map`, `filter`, `flatMap`, `reduceByKey`, `join`, `groupBy` |
| Action | Triggers computation and returns a result | `collect`, `count`, `take`, `first`, `saveAsTextFile`, `reduce` |

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("OrderAnalytics").getOrCreate()

orders = spark.read.csv("orders.csv", header=True, inferSchema=True)

# Transformation: lazy
high_value_orders = orders.filter(orders.order_amount > 10000)

# Action: triggers execution
high_value_orders.count()
```

### Spark SQL and DataFrames

- **SparkSession** is the entry point for DataFrame and SQL APIs.
- DataFrames support SQL-like operations: `select`, `filter`, `groupBy`, `join`, `agg`.
- A **schema** defines column names and data types.
- **Catalyst Optimizer** optimizes Spark SQL/DataFrame query plans.
- **Tungsten** improves memory and CPU execution efficiency.

```python
orders.createOrReplaceTempView("orders")

spark.sql("""
    SELECT customer_city, COUNT(*) AS total_orders, SUM(order_amount) AS revenue
    FROM orders
    GROUP BY customer_city
""").show()
```

### Spark MLlib

**MLlib** is Spark’s scalable machine-learning library. Its primary API is the DataFrame-based `spark.ml` package; the older RDD-based `spark.mllib` API is in maintenance mode. [spark.apache](https://spark.apache.org/docs/latest/ml-guide.html)

| MLlib concept | Meaning |
|---|---|
| Transformer | Converts one DataFrame to another, e.g. `Tokenizer`, `VectorAssembler` |
| Estimator | Learns from data and produces a Model, e.g. `LogisticRegression` |
| Pipeline | Chains transformers and estimators |
| Parameter | Configuration value for an algorithm |
| Model | Fitted estimator used for prediction |

**Typical ML pipeline:**
`Raw data -> Tokenizer -> HashingTF/TF-IDF -> Logistic Regression -> Model -> Prediction`

For e-commerce, MLlib can be used for customer-churn prediction, product-review classification, order-fraud detection, and recommendation systems.

### Spark Streaming

- **DStream** was the older Spark Streaming abstraction, built on RDDs.
- **Structured Streaming** is the modern API, built on DataFrames and Spark SQL.
- Streaming modes include **micro-batch** and, where supported, **continuous processing**.
- Common sources: Kafka, Kinesis, socket, files.
- Common sinks: console, memory, Kafka, HDFS, cloud storage.

```python
streaming_orders = (
    spark.readStream
    .format("kafka")
    .option("kafka.bootstrap.servers", "kafka:9092")
    .option("subscribe", "orders")
    .load()
)
```

**Likely one-mark facts:**
- Spark’s primary language is **Scala**, but PySpark provides the Python API.
- Spark uses **in-memory computing**.
- RDD transformations are **lazy**.
- `reduceByKey` is a transformation; `count` is an action.
- Spark MLlib’s current primary API is **DataFrame-based `spark.ml`**. [spark.apache](https://spark.apache.org/docs/latest/ml-guide.html)
- Structured Streaming is built on the **Spark SQL engine**. [spark.apache](https://spark.apache.org/docs/latest/streaming/index.html)

## 4. Apache MXNet

**Apache MXNet** is a scalable, flexible, open-source deep-learning framework supported by the Apache Software Foundation. It supports efficient training and deployment and multiple programming languages, including Python, Scala, R, Julia, C++, and JavaScript.

### Key features

- **Hybrid programming:** Supports both imperative and symbolic programming.
- **Multi-language support:** Python, Scala, R, Julia, C++, Perl, JavaScript.
- **Scalability:** Can train models across multiple GPUs and machines.
- **Gluon API:** High-level, user-friendly API for building and training neural networks.
- **Dynamic graphs:** Models can be defined dynamically using Python control flow.
- **Deployment:** Supports deployment across cloud, edge, and mobile environments.

MXNet’s Gluon API offers plug-and-play neural-network building blocks, flexible imperative structure, dynamic graphs, and high performance. [mxnet.apache](https://mxnet.apache.org/versions/1.3.1/gluon/index.html)

### MXNet vs PyTorch vs TensorFlow

| Feature | Apache MXNet | PyTorch | TensorFlow |
|---|---|---|---|
| Developer | Apache Software Foundation | Meta/Linux Foundation ecosystem | Google |
| Primary Python API | Gluon | `torch.nn` | Keras / TF APIs |
| Programming style | Imperative + symbolic | Mostly imperative/eager | Graph-based and eager options |
| Deployment strength | Multi-language, cloud/edge/mobile | Flexible research and production | Strong production ecosystem |
| Distributed training | Strong multi-GPU/multi-machine support | Strong, especially DDP | Strong via strategies |
| Best known for | Scalable, lightweight multi-language DL | Research-friendly dynamic models | Production ML pipelines |

Since you already know PyTorch and TensorFlow, focus on MXNet’s differentiators: **Gluon**, **hybrid imperative/symbolic execution**, **multi-language support**, and **scalable deployment**. [mxnet.apache](https://mxnet.apache.org/versions/1.3.1/gluon/index.html)

### MXNet terminology

| Term | Meaning |
|---|---|
| NDArray | MXNet’s multidimensional array, similar to NumPy array |
| Symbol | Symbolic computational graph representation |
| Gluon | High-level neural-network API |
| `gluon.nn` | Module containing neural-network layers and blocks |
| Trainer | Object that applies optimization updates |
| HybridBlock | Block that can be compiled into a symbolic graph |
| Executor | Executes a symbolic graph |

```python
from mxnet import gluon, nd
from mxnet.gluon import nn

# Example: product-category classifier
net = nn.Sequential()
net.add(
    nn.Dense(128, activation="relu"),
    nn.Dense(64, activation="relu"),
    nn.Dense(10)  # 10 product categories
)
net.initialize()

x = nd.random.normal(shape=(32, 100))  # batch of 32 customer-feature vectors
output = net(x)
```

**Likely one-mark facts:**
- MXNet’s high-level API is **Gluon**.
- MXNet supports both **imperative and symbolic** programming.
- MXNet’s core array object is **NDArray**.
- MXNet supports **multiple languages**.
- MXNet is designed for **scalable deep learning** and efficient deployment.

## 5. Data Transformation & Enrichment

**Data transformation** converts raw data into a clean, consistent, analysis-ready format. **Data enrichment** adds useful external or derived information to improve analytics and ML performance.

### Data-cleaning steps

| Step | Purpose | E-commerce example |
|---|---|---|
| Removing duplicates | Avoid repeated records | Remove duplicate order IDs |
| Handling missing values | Maintain dataset completeness | Fill missing product ratings |
| Correcting errors | Fix invalid values | Correct negative order quantity |
| Standardization | Put values in common format | Convert dates to ISO format |
| Normalization | Scale numeric values to a range | Scale order amount to 0–1 |
| Standardization / z-score | Mean 0 and standard deviation 1 | Standardize customer spend |
| Encoding | Convert categories to numbers | Encode payment mode |
| Outlier handling | Detect abnormal values | Detect unusually large orders |
| Type conversion | Ensure correct data type | Convert order date string to datetime |

### Common transformation techniques

- **Normalization:** Scales values into a fixed range, commonly 0–1.
- **Standardization:** Centers data at mean 0 with standard deviation 1.
- **Min-max scaling:** Maps values using minimum and maximum.
- **One-hot encoding:** Creates binary columns for categories.
- **Label encoding:** Assigns integers to categories.
- **Binning/discretization:** Converts continuous values into ranges.
- **Aggregation:** Summarizes data, e.g. monthly revenue per city.
- **Feature extraction:** Derives new features from existing data.
- **Feature selection:** Chooses the most relevant features.
- **Dimensionality reduction:** Reduces number of features, e.g. PCA.
- **Tokenization:** Splits text into words/tokens.
- **TF-IDF:** Weighs words by importance in text data.
- **Embedding:** Represents categories or words as dense vectors.

```python
import pandas as pd
from sklearn.preprocessing import StandardScaler, OneHotEncoder

orders = pd.DataFrame({
    "order_amount": [500, 1500, 2500, 10000],
    "payment_mode": ["UPI", "CARD", "UPI", "COD"]
})

scaler = StandardScaler()
orders["amount_scaled"] = scaler.fit_transform(orders[["order_amount"]])

encoder = OneHotEncoder(sparse_output=False)
payment_encoded = encoder.fit_transform(orders[["payment_mode"]])
```

### Data enrichment

**Data enrichment** adds new information from internal or external sources.

| Enrichment type | Example |
|---|---|
| Geographical enrichment | Add city, state, PIN code, delivery zone |
| Demographic enrichment | Add customer age group or income segment |
| Behavioral enrichment | Add purchase frequency, cart-abandonment rate |
| Temporal enrichment | Add day of week, festival season, hour of order |
| Derived enrichment | Add average order value, customer lifetime value |
| External enrichment | Add weather, currency rate, competitor price |
| Synthetic enrichment | Generate artificial but realistic training samples |

**E-commerce examples:**
- Add `delivery_zone` from customer PIN code.
- Add `is_festival_season` from order date.
- Add `customer_tenure_days` from registration and order dates.
- Add `avg_order_value` from historical orders.
- Add `product_popularity_score` from clickstream data.

### Synthetic data

**Synthetic data** is artificially generated data that preserves the statistical patterns of real data without necessarily copying actual records.

| Method | Description |
|---|---|
| Rule-based generation | Create records using business rules |
| Statistical sampling | Sample from estimated distributions |
| SMOTE (**S**ynthetic **M**inority **O**ver-sampling **TE**chnique) | Creates synthetic minority-class samples |
| GANs (**G**enerative **A**dversarial **N**etwork) | Generator creates fake data; discriminator distinguishes real vs fake |
| VAEs (**V**ariational **A**uto**e**ncoder) | Learn compressed latent representation and generate new samples |
| Diffusion models | Generate data by learning to reverse a noise process |
| LLM-based generation | Generate realistic text records or conversations |

**SMOTE** stands for **Synthetic Minority Oversampling Technique**. It is used when a classification dataset is imbalanced—for example, very few fraudulent orders compared with genuine orders.

### Data quality dimensions

| Dimension | Question |
|---|---|
| Accuracy | Is the value correct? |
| Completeness | Are required fields present? |
| Consistency | Do values agree across systems? |
| Timeliness | Is the data current? |
| Validity | Does it follow allowed format/range? |
| Uniqueness | Are there duplicates? |
| Integrity | Are relationships valid? |

## 6. Automated Data Generation

**Automated data generation** creates additional training examples from existing data to improve model robustness, reduce overfitting, and handle class imbalance.

### Image augmentation

| Technique | Effect | Use case |
|---|---|---|
| Rotation | Rotates image by an angle | Product photos taken at different angles |
| Cropping | Removes outer portions | Focus on product object |
| Flipping | Mirrors image horizontally/vertically | Increase orientation variety |
| Scaling/resizing | Changes image size | Handle different camera distances |
| Translation | Shifts image position | Product not always centered |
| Brightness change | Makes image brighter/darker | Different store lighting |
| Contrast change | Changes light-dark difference | Low-quality images |
| Noise addition | Adds random pixel disturbance | Camera-sensor variation |
| Blur | Reduces sharpness | Motion or focus issues |
| Color jitter | Changes hue/saturation/brightness | Different product-photo conditions |
| Cutout/random erasing | Hides random image region | Improves occlusion robustness |
| Mixup | Blends two images and labels | Regularization |
| CutMix | Replaces image region with another image’s region | Regularization |

```python
from torchvision import transforms

augment_product_image = transforms.Compose([
    transforms.RandomRotation(20),
    transforms.RandomResizedCrop(224),
    transforms.RandomHorizontalFlip(),
    transforms.ColorJitter(brightness=0.2, contrast=0.2),
    transforms.ToTensor()
])
```

### Text augmentation

| Technique | Description |
|---|---|
| Synonym replacement | Replace words with synonyms |
| Random insertion | Insert random synonym words |
| Random deletion | Delete random words |
| Random swap | Swap word positions |
| Back translation | Translate to another language and back |
| Paraphrasing | Rewrite sentence with same meaning |
| Noise injection | Add typos, punctuation changes, casing variation |
| LLM-based generation | Generate new reviews/questions/queries |

**E-commerce example:** For a review classifier, “The delivery was very fast” can become “The shipping was extremely quick.”

### Tabular-data augmentation

| Technique | Description |
|---|---|
| SMOTE | Generates minority-class samples by interpolation |
| ADASYN | Generates more synthetic samples for harder minority examples |
| Random oversampling | Duplicates minority samples |
| Random undersampling | Reduces majority samples |
| Jittering | Adds small noise to numeric features |
| Mixup for tabular data | Blends feature vectors and labels |
| CTGAN/TVAE | Deep generative models for tabular synthetic data |

### Audio augmentation

- Noise addition
- Time stretching
- Pitch shifting
- Speed change
- Volume change
- Room-impulse simulation
- SpecAugment: masks time/frequency regions in spectrograms

### Time-series augmentation

- Jittering
- Scaling
- Window slicing
- Time warping
- Magnitude warping
- Permutation
- Adding seasonality/noise

### Augmentation cautions

- Do not use transformations that change the label.
- For digit recognition, vertical flipping can make `6` look like `9`.
- For product-category classification, excessive cropping may remove the product.
- For fraud detection, blindly duplicating fraud records can cause overfitting.
- Synthetic data should be validated for realism, privacy, and distribution similarity.

**Likely one-mark facts:**
- Rotation, cropping, flipping, and noise addition are **image augmentation** techniques.
- SMOTE is used for **class imbalance**.
- Mixup blends two samples and their labels.
- Augmentation mainly reduces **overfitting** and improves **robustness/generalization**.
- Back translation is a **text augmentation** method.

## 7. Cloud Services Integration

Cloud computing provides on-demand access to computing, storage, networking, and managed AI/ML services over the internet.

### Major cloud providers

| Provider | Main platform | Common storage | ML service |
|---|---|---|---|
| AWS | Amazon Web Services | S3, EBS, EFS | SageMaker |
| Microsoft Azure | Azure | Blob Storage, Data Lake Storage | Azure Machine Learning |
| Google Cloud | Google Cloud Platform | Cloud Storage, BigQuery | Vertex AI |

### AWS services

| Service | Purpose |
|---|---|
| EC2 | Virtual servers/compute |
| S3 | Object storage |
| EBS | Block storage for EC2 |
| EFS | Elastic file storage |
| RDS | Managed relational database |
| DynamoDB | Managed NoSQL database |
| Redshift | Data warehouse |
| Glue | Serverless ETL and data catalog |
| Kinesis | Real-time streaming ingestion |
| EMR | Managed Hadoop/Spark cluster |
| Lambda | Serverless function execution |
| SageMaker | Build, train, deploy ML models |
| IAM | Identity and access management |

### Azure services

| Service | Purpose |
|---|---|
| Azure Virtual Machines | Compute |
| Azure Blob Storage | Object storage |
| Azure Data Lake Storage | Big-data analytics storage |
| Azure SQL Database | Managed relational database |
| Azure Synapse Analytics | Analytics and data warehouse |
| Azure Data Factory | Data integration/ETL |
| Azure Databricks | Managed Apache Spark analytics |
| Azure Machine Learning | ML lifecycle platform |
| Azure Functions | Serverless compute |
| Azure Event Hubs | Event streaming ingestion |

### Google Cloud services

| Service | Purpose |
|---|---|
| Compute Engine | Virtual machines |
| Cloud Storage | Object storage |
| BigQuery | Serverless data warehouse |
| Cloud SQL | Managed relational database |
| Dataflow | Stream/batch data processing |
| Dataproc | Managed Hadoop/Spark |
| Pub/Sub | Messaging/event ingestion |
| Cloud Functions | Serverless compute |
| Vertex AI | End-to-end ML platform |

### Cloud deployment models

| Model | Description | Example |
|---|---|---|
| Public cloud | Services owned by a provider and shared by customers | AWS, Azure, GCP |
| Private cloud | Dedicated infrastructure for one organization | Bank’s internal cloud |
| Hybrid cloud | Combination of private and public cloud | Sensitive data on-premise, ML training in cloud |
| Multi-cloud | Uses more than one public cloud provider | AWS + Azure |

### Cloud service models

| Model | Full form | User manages | Provider manages | Examples |
|---|---|---|---|---|
| IaaS | Infrastructure as a Service | OS, apps, data | Hardware, virtualization | EC2, Azure VM, Compute Engine |
| PaaS | Platform as a Service | Apps and data | OS, runtime, infrastructure | App Engine, Heroku |
| SaaS | Software as a Service | Usage/configuration | Entire stack | Gmail, Salesforce |
| FaaS | Function as a Service | Function code | Servers and scaling | Lambda, Cloud Functions |

### Cloud-based augmentation pipeline

A typical scalable pipeline:

```text
Customer/order events
        |
        v
Ingestion: Kafka / Kinesis / Pub/Sub
        |
        v
Storage: S3 / Blob / Cloud Storage
        |
        v
Processing: Spark on EMR / Databricks / Dataproc
        |
        v
Cleaning, transformation, enrichment
        |
        v
Augmentation: image/text/tabular generation
        |
        v
Feature store / data lake
        |
        v
ML training: SageMaker / Azure ML / Vertex AI
        |
        v
Model deployment and monitoring
```

### Cloud concepts for MCQs

| Term | Meaning |
|---|---|
| Elasticity | Automatically scale resources up/down with demand |
| Scalability | Handle increased workload by adding resources |
| High availability | Minimize downtime through redundancy |
| Fault tolerance | Continue operating despite component failure |
| Latency | Delay between request and response |
| Throughput | Amount of data processed per unit time |
| Object storage | Stores files/objects with metadata; e.g. S3 |
| Block storage | Disk-like storage attached to VMs; e.g. EBS |
| Serverless | Provider manages servers and scaling |
| ETL | Extract, Transform, Load |
| ELT | Extract, Load, Transform |
| Data lake | Stores raw structured and unstructured data |
| Data warehouse | Stores curated, structured analytical data |
| Feature store | Stores and serves ML features consistently |
| CI/CD | Continuous Integration / Continuous Delivery |
| IaC | Infrastructure as Code |
| CDN | Content Delivery Network |

## 8. Python Tools to Remember

| Task | Python library/tool |
|---|---|
| Data manipulation | pandas, NumPy |
| Visualization | Matplotlib, Seaborn |
| ML | scikit-learn |
| Deep learning | PyTorch, TensorFlow, MXNet |
| Spark with Python | PySpark |
| Image augmentation | torchvision, Albumentations, imgaug, OpenCV |
| Text augmentation | NLTK, spaCy, TextAttack, nlpaug |
| Imbalanced data | imbalanced-learn |
| AWS SDK | boto3 |
| Azure SDK | azure-storage-blob, azure-ai-ml |
| Google Cloud SDK | google-cloud-storage, google-cloud-bigquery |
| Workflow orchestration | Apache Airflow, Prefect |
| Experiment tracking | MLflow, Weights & Biases |

```python
import boto3

s3 = boto3.client("s3")
s3.upload_file("augmented_orders.csv", "ecommerce-ml-bucket", "training/augmented_orders.csv")
```

## 9. Rapid Revision: One-Liners

- **Hadoop** = distributed storage and batch processing.
- **HDFS** = Hadoop Distributed File System.
- **YARN** = Yet Another Resource Negotiator; resource management.
- **MapReduce** = Map, Shuffle/Sort, Reduce.
- **NameNode** stores metadata; **DataNode** stores blocks.
- **Spark** = fast, in-memory distributed data-processing engine.
- **RDD** = Resilient Distributed Dataset.
- RDDs are **immutable**, **distributed**, and **lazy**.
- **PySpark** = Python API for Apache Spark.
- **MLlib** = Spark’s scalable ML library.
- Current MLlib primary API = DataFrame-based **`spark.ml`**. [spark.apache](https://spark.apache.org/docs/latest/ml-guide.html)
- **Structured Streaming** = Spark’s scalable, fault-tolerant stream-processing engine. [spark.apache](https://spark.apache.org/docs/latest/streaming/index.html)
- **MXNet** = Apache-supported scalable deep-learning framework.
- **Gluon** = MXNet’s high-level neural-network API.
- **NDArray** = MXNet multidimensional array.
- **Data cleaning** removes errors, duplicates, and missing values.
- **Normalization** scales values; **standardization** gives mean 0 and SD 1.
- **Enrichment** adds new useful information to existing data.
- **Synthetic data** is artificially generated data.
- **SMOTE** = Synthetic Minority Oversampling Technique.
- **Image augmentation** includes rotation, cropping, flipping, noise addition.
- **Mixup** blends two samples and labels.
- **AWS S3** = object storage; **Azure Blob** = object storage; **GCP Cloud Storage** = object storage.
- **SageMaker** = AWS ML platform; **Azure ML** = Azure ML platform; **Vertex AI** = Google Cloud ML platform.
- **EMR** = AWS managed Hadoop/Spark; **Dataproc** = GCP managed Hadoop/Spark; **Databricks** = managed Spark platform.
- **ETL** = Extract, Transform, Load.
- **IaaS/PaaS/SaaS/FaaS** = Infrastructure/Platform/Software/Function as a Service.

## 10. Probable One-Mark Questions

1. **What does HDFS stand for?**  
   Hadoop Distributed File System.

2. **Which Hadoop component manages cluster resources?**  
   YARN.

3. **Which Hadoop component stores file metadata?**  
   NameNode.

4. **Which Hadoop component stores actual data blocks?**  
   DataNode.

5. **What is the default HDFS block size in modern Hadoop?**  
   128 MB.

6. **Which Spark abstraction is immutable and fault tolerant?**  
   RDD.

7. **Which PySpark object is the entry point for DataFrame APIs?**  
   SparkSession.

8. **Is `filter()` a transformation or action in Spark?**  
   Transformation.

9. **Is `count()` a transformation or action in Spark?**  
   Action.

10. **Which Spark library is used for scalable machine learning?**  
   MLlib.

11. **Which MXNet API is used for high-level deep learning?**  
   Gluon.

12. **What is MXNet’s multidimensional array object called?**  
   NDArray.

13. **Which technique creates synthetic minority-class samples?**  
   SMOTE.

14. **Name three image-augmentation techniques.**  
   Rotation, cropping, noise addition.

15. **Which AWS service provides object storage?**  
   Amazon S3.

16. **Which Azure service provides object storage?**  
   Azure Blob Storage.

17. **Which Google Cloud service is a serverless data warehouse?**  
   BigQuery.

18. **Which AWS service is used for managed Hadoop/Spark?**  
   Amazon EMR.

19. **Which GCP service is used for managed Hadoop/Spark?**  
   Dataproc.

20. **What does ETL stand for?**  
   Extract, Transform, Load.

21. **What is the main goal of data augmentation?**  
   Increase data diversity and improve model robustness/generalization.

22. **Which augmentation technique should be avoided for handwritten digits such as 6 and 9?**  
   Vertical flipping.

23. **What is data enrichment?**  
   Adding new or derived information to existing data.

24. **What is batch processing?**  
   Processing accumulated data in groups at scheduled intervals.

25. **What is stream processing?**  
   Processing data continuously as it arrives.
