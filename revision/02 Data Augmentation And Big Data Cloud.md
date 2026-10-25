# Module 2: Data Augmentation Technologies, Big Data & Cloud Services
**Syllabus Coverage:** Part-B, Section 2 (Intro, i, ii, iii) — Hadoop, Apache Spark, Apache MXNet, Batch vs. Real-Time Processing, Data Transformation & Enrichment, Automated Data Generation, Integration with Cloud Services (AWS, Azure, Google Cloud).

## 2.1 Big Data Tools & Frameworks (Hadoop, Apache Spark, Apache MXNet)

### 1. Apache Hadoop Ecosystem
* **Definition:** Open-source distributed framework written in **Java** for storing and batch-processing massive datasets across clusters of commodity hardware using a **Master-Slave (Master-Worker)** architecture.
* **4 Core Hadoop Modules:**
  1. **Hadoop Common:** Shared utilities and libraries required by other Hadoop modules.
  2. **HDFS (Hadoop Distributed File System):** Distributed, fault-tolerant, write-once-read-many (**WORM**) block storage system optimized for **high aggregate throughput** rather than low-latency random access.
  3. **YARN (Yet Another Resource Negotiator):** Cluster resource management and job scheduling layer (introduced in **Hadoop 2.0**, decoupling MapReduce from resource management).
  4. **MapReduce:** Disk-based distributed batch parallel programming model (`Map` $\rightarrow$ `Shuffle & Sort` $\rightarrow$ `Reduce`).

#### HDFS Architecture & High-Yield Exam Parameters
| Component / Parameter | Technical Specification & Exam Facts |
| :--- | :--- |
| **Default Block Size** | **128 MB** in Hadoop 2.x / 3.x (**64 MB** in Hadoop 1.x) |
| **Default Replication Factor** | **3** (1 replica on local rack node, 1 on a different rack, 1 on a different node in that second rack — **Rack Awareness**) |
| **NameNode (Master)** | Stores filesystem **metadata** (namespace directory tree, file-to-block mapping, block-to-DataNode locations) in RAM; persists state via **`FsImage`** (point-in-time snapshot) and **`EditLog`** (transaction log of changes). Never stores actual user block data. |
| **DataNode (Worker/Slave)** | Stores actual data blocks on local disks; sends periodic **Heartbeats** (default every **3 seconds**) and **Block Reports** to NameNode. If no heartbeat is received for **10 minutes**, NameNode marks the DataNode dead and triggers re-replication. |
| **Secondary NameNode** | **NOT a hot standby backup!** It periodically merges `FsImage` + `EditLog` (checkpointing) to prevent `EditLog` from growing indefinitely. |
| **HDFS High Availability (HA)** | Uses **Active NameNode** + **Standby NameNode** sharing edits via a quorum of **JournalNodes (QJM)** and coordinated for automatic failover by **Apache ZooKeeper** (`ZKFC`). |
| **Erasure Coding (Hadoop 3.x)** | Reduces storage overhead from **200%** (3x replication) to **~50%** (e.g., Reed-Solomon `RS-6-3`) for cold/warm data. |

#### YARN Architecture Components
* **ResourceManager (Master — 1 per cluster):** Consists of **Scheduler** (allocates resources via Capacity or Fair Scheduler) and **ApplicationsManager** (accepts job submissions and negotiates first container for ApplicationMaster).
* **NodeManager (Worker — 1 per node):** Monitors container resource usage (CPU, RAM) on a machine and reports to ResourceManager.
* **ApplicationMaster (1 per application/job):** Negotiates resource **Containers** from ResourceManager and works with NodeManagers to execute and monitor tasks.
* **MapReduce Optimization — Combiner ("Mini-Reducer"):** Runs **locally on the Mapper node** before network transfer to aggregate intermediate `(key, value)` pairs and minimize network **Shuffle** traffic (valid only for associative and commutative operations like `SUM`, `MAX`, `MIN`, **not** raw `AVERAGE`).

### 2. Apache Spark
* **Definition:** Unified, **in-memory** distributed data processing engine written in **Scala** (runs on the **JVM**) supporting **Batch, Real-Time Streaming, SQL, Machine Learning, and Graph processing** up to **100x faster** in memory (and 10x faster on disk) than Hadoop MapReduce.
* **Spark Architecture:**
  * **Driver Program (`SparkContext` / `SparkSession`):** Converts user program into a **DAG (Directed Acyclic Graph)**, splits the DAG into **Stages** at shuffle boundaries, and schedules **Tasks** on Executors.
  * **Cluster Manager:** Allocates resources (**Standalone**, **Apache YARN**, **Kubernetes**, or Apache Mesos).
  * **Executors:** Worker JVM processes that run **Tasks** (1 task per data partition) and cache partitions in memory/disk.

#### Spark Core Data Abstractions (RDD vs. DataFrame vs. Dataset)
| Feature | **RDD (Resilient Distributed Dataset)** | **DataFrame** | **Dataset** |
| :--- | :--- | :--- | :--- |
| **Introduced In** | Spark 1.0 | Spark 1.3 | Spark 1.6 |
| **Schema Awareness** | Unstructured / No tabular schema | Named columns with schema (`Dataset[Row]`) | Strongly typed JVM objects + schema |
| **Compile-time Type Safety** | **Yes** | **No** (runtime schema check) | **Yes** |
| **Catalyst Optimizer & Tungsten** | **No** (manual optimization) | **Yes** (Logical $\rightarrow$ Physical query plan + off-heap binary memory) | **Yes** |
| **Language Support** | Scala, Java, Python, R | Scala, Java, Python (`PySpark`), R (`SparkR`) | **Scala and Java ONLY** (not available in Python/R due to dynamic typing) |

#### Spark Transformations vs. Actions & Lazy Evaluation
* **Lazy Evaluation:** Spark does **not** execute transformations immediately; it builds a lineage **DAG** and executes only when an **Action** is called.
* **Transformations (Return a new RDD/DataFrame — Lazy):**
  * **Narrow Transformations (No Shuffle — 1-to-1 partition mapping, runs in a single Stage):** `map()`, `flatMap()`, `filter()`, `union()`, `sample()`, `coalesce(decrease_partitions)`.
  * **Wide Transformations (Requires Network Shuffle — creates a new Stage boundary):** `groupByKey()`, `reduceByKey()`, `join()`, `distinct()`, `repartition()`, `sortByKey()`.
  * *(Exam Tip: Always prefer **`reduceByKey()`** over **`groupByKey()`** because `reduceByKey()` performs map-side local aggregation before shuffling over the network!)*
* **Actions (Trigger DAG Execution & Return Results to Driver or Storage):** `collect()`, `count()`, `first()`, `take(n)`, `reduce()`, `foreach()`, `saveAsTextFile()`, `write.parquet()`.
* **Spark Ecosystem Libraries:**
  * **Spark SQL:** SQL queries and DataFrame API powered by **Catalyst Optimizer** and **Project Tungsten** (whole-stage code generation).
  * **Spark Streaming (DStreams — micro-batch) & Structured Streaming:** Scalable stream processing built on the Spark SQL engine with end-to-end **exactly-once** fault-tolerance guarantees.
  * **MLlib:** Distributed machine learning library (`pyspark.ml` DataFrame-based pipelines and `pyspark.mllib` legacy RDD-based API).
  * **GraphX:** Distributed graph computation (e.g., PageRank, connected components).

### 3. Apache MXNet (Explicitly Highlighted in Syllabus)
* **Definition:** Open-source, highly **scalable deep learning framework** supported by **Apache** (originally selected by AWS as its premier deep learning engine) designed for both **efficiency (static symbolic graphs)** and **flexibility (imperative dynamic programming)** with **robust multi-language support**.
* **Key Architectural Features of Apache MXNet (High-Yield Exam Facts):**
  1. **Hybrid Programming Model (`HybridBlock` & `.hybridize()`):**
     * Combines **Imperative Programming** (`NDArray` — like NumPy/PyTorch, easy to debug line-by-line) and **Symbolic Programming** (`Symbol` — like TF 1.x static computation graphs, optimized for memory reuse and C++ execution speed).
     * Through the **Gluon API**, developers write imperative Python code using `gluon.HybridBlock` and call **`net.hybridize()`** with a single line to compile and fuse the network into a high-speed static symbolic graph for production deployment!
  2. **Robust Multi-Language Support:** First-class bindings for **Python, R, Scala, Julia, Clojure, C++, Java, Perl, and JavaScript** (enabling training in Python and low-latency inference in C++/Java/Scala).
  3. **Core MXNet Components:**
     * **`NDArray` (`mxnet.nd`):** Imperative multidimensional array tensor on CPU (`mx.cpu()`) or GPU (`mx.gpu()`) with **asynchronous execution** via MXNet's dependency engine.
     * **`Symbol` (`mxnet.sym`):** Declarative symbolic computation graph representation.
     * **`Gluon` (`mxnet.gluon`):** High-level imperative & hybrid neural network API (`nn.Block`, `nn.HybridBlock`, `gluon.Trainer`, `gluon.data.DataLoader`).
     * **`Autograd` (`mxnet.autograd`):** Automatic differentiation using `with autograd.record():` followed by `loss.backward()`.
     * **`KVStore` (`mxnet.kv`):** Built-in distributed **Key-Value Store** (Parameter Server architecture) enabling **near-linear scaling efficiency** across multiple GPUs and multi-node distributed clusters.

### 4. Batch Processing vs. Real-Time (Stream) Processing
| Dimension | **Batch Processing** | **Micro-Batch Processing** | **Real-Time / True Stream Processing** |
| :--- | :--- | :--- | :--- |
| **Data Scope** | Bounded, finite historical datasets stored at rest | Continuous stream grouped into small time intervals (e.g., 0.5–5 sec) | Unbounded, continuous event-by-event processing in motion |
| **Latency** | Minutes to hours (High latency) | Sub-second to a few seconds | Milliseconds (Ultra-low latency) |
| **Primary Optimization** | **High Throughput** | Balance of throughput & latency | **Low Latency** |
| **Representative Tools** | **Hadoop MapReduce**, Apache Spark (Batch) | **Spark Streaming (DStreams)**, Spark Structured Streaming (default trigger) | **Apache Flink**, **Apache Kafka Streams**, Apache Storm, Spark Continuous Processing |

* **Big Data Processing Architectures (Classic MCQ):**
  * **Lambda Architecture (Nathan Marz):** Combines **3 layers** to handle both historical accuracy and real-time speed:
    1. **Batch Layer:** Stores immutable master dataset and precomputes accurate batch views (Hadoop/Spark).
    2. **Speed Layer:** Processes real-time incremental streams for low-latency views (Spark Streaming/Flink).
    3. **Serving Layer:** Merges outputs from Batch and Speed layers for user queries. *(Drawback: Maintaining duplicate business logic across two codebases).*
  * **Kappa Architecture (Jay Kreps):** Eliminates the separate Batch Layer; treats **everything as a stream** using an immutable append-only log (**Apache Kafka**) and a **single stream processing engine** (reprocessing historical data simply replays the Kafka log from offset 0).

## 2.2 Data Transformation & Enrichment (Syllabus Section 2(i))
* **Definition:** Tools and processes that **clean, normalize, and enrich raw data** by adding new information or creating **synthetic data** to improve model training, generalization, and accuracy.

### 1. Data Cleaning & Imputation
* **Missing Data Mechanisms (Rubin's Classification):**
  * **MCAR (Missing Completely at Random):** Missingness is independent of both observed and unobserved data (safe to drop if small fraction).
  * **MAR (Missing at Random):** Missingness depends only on *observed* variables.
  * **MNAR (Missing Not at Random):** Missingness depends on the *unobserved missing value itself* (e.g., high-income respondents skipping income questions).
* **Imputation Techniques:**
  * **Univariate:** Mean (for symmetric normal numeric data), **Median** (for **skewed numeric data with outliers**), **Mode** (for categorical data), Constant indicator.
  * **Multivariate / Model-Based:** **KNN Imputer** (`KNNImputer`), **MICE (Multiple Imputation by Chained Equations / `IterativeImputer`)**, Forward-Fill (`ffill`) / Backward-Fill (`bfill`) / Linear Interpolation for **Time-Series**.
* **Outlier Detection & Treatment:**
  * **IQR Rule (Tukey's Fences):** Outliers lie below **$Q_1 - 1.5 \times \text{IQR}$** or above **$Q_3 + 1.5 \times \text{IQR}$** (where $\text{IQR} = Q_3 - Q_1$).
  * **Z-Score Rule:** $|Z| = \left|\frac{x - \mu}{\sigma}\right| > 3$ (assumes normal distribution).
  * **Winsorization (Capping):** Clips extreme values at specified percentiles (e.g., 1st and 99th percentiles) rather than deleting rows.

### 2. Data Normalization, Scaling & Transformation Formulas
| Technique | Mathematical Formula | Output Range / Properties | Best Used When... |
| :--- | :--- | :--- | :--- |
| **Min-Max Normalization** (`MinMaxScaler`) | $x' = \frac{x - x_{\min}}{x_{\max} - x_{\min}}$ | **$[0, 1]$** (or $[a, b]$) | Bounded input needed (e.g., **Neural Networks, Image Pixels, KNN**); **sensitive to outliers**. |
| **Z-Score Standardization** (`StandardScaler`) | $z = \frac{x - \mu}{\sigma}$ | **Mean $\mu = 0$, Std Dev $\sigma = 1$** (unbounded) | Features follow Gaussian distribution; required for **PCA, SVM, Logistic/Lasso/Ridge Regression, K-Means**. |
| **Robust Scaling** (`RobustScaler`) | $x' = \frac{x - \text{Median}(x)}{Q_3 - Q_1}$ | Centered at median, scaled by IQR | Dataset contains **heavy outliers** (median and IQR are robust to outliers). |
| **Log Transformation** | $x' = \log(x + 1)$ (`np.log1p`) | Compresses long right tail | Correcting **right-skewed (positively skewed)** distributions (e.g., income, house prices) and heteroscedasticity. |
| **Box-Cox / Yeo-Johnson** | Power transform parameterized by $\lambda$ | Approximates normality | **Box-Cox** requires **strictly positive ($x > 0$)** values; **Yeo-Johnson** works with **positive, zero, and negative** values. |

*(Exam Tip: Distance-based and gradient-based algorithms — **KNN, K-Means, SVM, PCA, Neural Networks, Regularized Regression** — **require** feature scaling! Tree-based algorithms — **Decision Trees, Random Forests, XGBoost, LightGBM** — are scale-invariant and **do NOT require** feature scaling!)*

### 3. Categorical Encoding & Data Enrichment
* **Categorical Encoding:**
  * **One-Hot Encoding:** Creates binary indicator columns for **nominal** (unordered) categories; drop 1 column ($k-1$ dummies) in linear models to avoid the **Dummy Variable Trap (perfect multicollinearity)**.
  * **Ordinal / Label Encoding:** Assigns integers $0, 1, \dots, k-1$ preserving order for **ordinal** features (e.g., `Low < Medium < High`).
  * **Target / Mean Encoding:** Replaces a high-cardinality categorical value with the mean of the target variable (requires **out-of-fold regularization/smoothing** to prevent target leakage).
* **Data Enrichment:** Appending external or derived attributes to raw data — e.g., **Geocoding** (address $\rightarrow$ lat/long + census demographics), **IP-to-Location**, **Temporal Feature Extraction** (day of week, holiday flags, cyclical encoding via $\sin/\cos$), **Entity Resolution / Fuzzy Matching** (linking records across disparate databases), and **Knowledge Graph lookups**.

### 4. Synthetic Data Generation for Tabular & Imbalanced Data
* **SMOTE (Synthetic Minority Over-sampling Technique):**
  * Solves **class imbalance** not by duplicating minority rows, but by interpolating **new synthetic samples along the line segments joining a minority instance $x_i$ and its $k$ nearest minority neighbors**:
    $$x_{\text{new}} = x_i + \lambda \cdot (x_{zi} - x_i), \quad \lambda \in [0, 1]$$
  * **Variants:** **Borderline-SMOTE** (synthesizes near decision boundary), **SMOTE-NC** (handles both Nominal Categorical and Continuous features), **ADASYN (Adaptive Synthetic Sampling)** (generates more synthetic data for minority examples that are harder to learn).
  * *(Critical Rule: Apply SMOTE **ONLY to the training set**, never to the validation or test set before splitting!)*
* **Deep Generative Synthetic Data:**
  * **GANs (Generative Adversarial Networks — Goodfellow et al.):** Minimax two-player game between a **Generator ($G$)** (creates realistic synthetic data from random noise $z$) and a **Discriminator ($D$)** (distinguishes real vs. fake data). **CTGAN** is specialized for tabular data with mixed continuous/discrete columns.
  * **VAEs (Variational Autoencoders):** Probabilistic encoder-decoder learning a continuous latent distribution $\mathcal{N}(\mu, \sigma^2)$ optimized via **Reconstruction Loss + KL-Divergence**.
  * **Diffusion Models (DDPM):** Iteratively adds Gaussian noise (forward process) and learns a neural network to reverse the noise step-by-step.

## 2.3 Automated Data Generation & Augmentation (Syllabus Section 2(ii))
* **Definition:** Automated techniques that create **new training data from existing datasets** using methods like **image rotation, cropping, and noise addition** (especially in AI/ML), increasing dataset diversity, acting as a **regularizer to reduce overfitting**, and enhancing **model robustness and invariance**.

### 1. Image Data Augmentation Techniques (Explicitly Named in Syllabus)
| Category | Specific Techniques | Purpose / Invariance Gained |
| :--- | :--- | :--- |
| **Geometric / Spatial Transformations** | **Rotation** (e.g., $\pm 15^\circ, 90^\circ$), **Random Cropping & Resizing**, **Horizontal/Vertical Flipping**, Translation (Shifting), Scaling (Zooming), Shearing, Affine/Perspective Warping | Teaches **spatial, orientation, scale, and translational invariance** (Note: avoid vertical flips or $180^\circ$ rotation on digit tasks like `6` vs `9` where label semantics change — **Label-Preserving Rule**). |
| **Noise Addition & Occlusion** | **Gaussian Noise** ($\mathcal{N}(0, \sigma^2)$), **Salt-and-Pepper Noise** (random white/black impulse pixels), **Speckle/Poisson Noise**, **Cutout / Random Erasing** (masks out rectangular patches) | Improves robustness against **sensor distortion, compression artifacts, and partial object occlusion**. |
| **Photometric / Color Space** | **Color Jittering** (Brightness, Contrast, Saturation, Hue shifts), Histogram Equalization (CLAHE), Grayscale conversion, Solarization, Gaussian Blur | Teaches **lighting, illumination, and camera sensor invariance**. |
| **Sample-Mixing Augmentations** | **MixUp** (convex linear combination of two images & their labels: $\tilde{x} = \lambda x_i + (1-\lambda)x_j$, $\tilde{y} = \lambda y_i + (1-\lambda)y_j$), **CutMix** (cuts a patch from image B and pastes it onto image A, mixing labels proportionally to patch area), **Mosaic** (combines 4 training images into 1 grid — popularized in **YOLOv4**) | Encourages linear behavior between classes, smooths decision boundaries, and improves small-object detection. |
| **Automated Policy Search** | **AutoAugment** (uses Reinforcement Learning to find optimal augmentation sub-policies on a dataset), **RandAugment** (replaces expensive search with just 2 hyperparameters: $N$ transformations and magnitude $M$), **TrivialAugment** | Automatically tunes augmentation sequences without manual trial-and-error. |

### 2. Text (NLP), Audio & Time-Series Data Augmentation
* **NLP / Text Augmentation:**
  * **EDA (Easy Data Augmentation):** **Synonym Replacement** (WordNet), **Random Insertion**, **Random Swap**, **Random Deletion**.
  * **Back-Translation:** Translating text from Source Language $\rightarrow$ Pivot Language (e.g., English $\rightarrow$ French $\rightarrow$ English) to generate diverse paraphrases.
  * **Contextual Word Embeddings / LLM Augmentation:** Masking tokens and filling them via **BERT** (`fill-mask`) or generating synthetic labeled examples via LLMs.
* **Audio / Speech Augmentation:** **Time Stretching**, **Pitch Shifting**, adding background room/street noise, **SpecAugment** (masks frequency channels and time steps directly on the Mel-Spectrogram).
* **Time-Series Augmentation:** **Jittering** (adding noise), **Time Warping** (smoothly distorting time intervals), **Window Slicing**, **Magnitude Warping**, **Permutation**.
* **Test-Time Augmentation (TTA):** Creating multiple augmented versions (e.g., crops/flips) of a **test image during inference** and averaging the model's predicted probabilities to boost final accuracy.

## 2.4 Integration with Cloud Services (AWS, Azure, Google Cloud — Syllabus Section 2(iii))

### Cross-Cloud Service Mapping Matrix (High-Yield Exam Table)
| Service Capability | **Amazon Web Services (AWS)** | **Microsoft Azure** | **Google Cloud Platform (GCP)** |
| :--- | :--- | :--- | :--- |
| **Object Storage (Data Lake)** | **Amazon S3** (Simple Storage Service) | **Azure Blob Storage** / **ADLS Gen2** (Data Lake Storage) | **Google Cloud Storage (GCS)** |
| **Virtual Machines (IaaS Compute)** | **Amazon EC2** | **Azure Virtual Machines** | **Google Compute Engine (GCE)** |
| **Serverless Functions (FaaS)** | **AWS Lambda** | **Azure Functions** | **Cloud Run Functions** (Cloud Functions) |
| **Managed Kubernetes** | **Amazon EKS** | **Azure Kubernetes Service (AKS)** | **Google Kubernetes Engine (GKE)** |
| **Managed Hadoop & Spark** | **Amazon EMR** (Elastic MapReduce) | **Azure HDInsight** / **Azure Databricks** | **Cloud Dataproc** |
| **Serverless ETL & Data Pipelines** | **AWS Glue** (Spark-based ETL + Data Catalog) | **Azure Data Factory (ADF)** | **Cloud Dataflow** (Apache Beam) / **Cloud Data Fusion** |
| **Real-Time Event Streaming** | **Amazon Kinesis** (Data Streams / Firehose) / **MSK** | **Azure Event Hubs** / **Azure Stream Analytics** | **Cloud Pub/Sub** |
| **Cloud Data Warehouse (MPP)** | **Amazon Redshift** | **Azure Synapse Analytics** (Microsoft Fabric) | **BigQuery** (Serverless columnar warehouse) |
| **NoSQL Key-Value / Document DB** | **Amazon DynamoDB** | **Azure Cosmos DB** | **Cloud Firestore** / **Cloud Bigtable** |
| **End-to-End Managed AI/ML Platform** | **Amazon SageMaker** (Ground Truth, Feature Store, Pipelines, Clarify) | **Azure Machine Learning** (Azure ML Studio) | **Vertex AI** (Pipelines, Feature Store, AutoML) |
| **Workflow Orchestration** | **AWS Step Functions** / **MWAA** (Managed Airflow) | **Azure Logic Apps** / **ADF Pipelines** | **Cloud Composer** (Managed Apache Airflow) |

### Cloud-Based Data Augmentation Pipelines & High Availability Concepts
* **Cloud Data Augmentation Pipeline Architecture:**
  1. **Ingestion Layer:** Raw data lands in Object Storage (**S3 / ADLS Gen2 / GCS**) or real-time streams (**Kinesis / Event Hubs / Pub/Sub**).
  2. **Processing & Augmentation Layer:** Triggered via event notifications (e.g., S3 Event $\rightarrow$ **AWS Lambda** or **Step Functions**), distributed jobs in **AWS Glue / Dataproc / Databricks / Dataflow** clean, normalize, enrich, and generate augmented datasets at scale.
  3. **Feature Store & Labeling:** Curated features registered in **SageMaker Feature Store / Vertex AI Feature Store**; human-in-the-loop active learning handled via **SageMaker Ground Truth**.
  4. **Distributed Model Training & Deployment:** Streamed directly into **SageMaker / Azure ML / Vertex AI** GPU clusters using `PipeMode` / `FastFile` and deployed behind auto-scaling HTTPS endpoints.
* **Medallion Data Lakehouse Architecture (Delta Lake / Apache Iceberg / Apache Hudi):**
  * **Bronze Layer (Raw):** Unmodified raw ingested data in native format (append-only audit history).
  * **Silver Layer (Cleaned & Enriched):** Deduplicated, schema-validated, normalized, and enriched enterprise data.
  * **Gold Layer (Curated / Feature-Ready):** Aggregated business-level tables and ML-ready feature sets.
* **High Availability (HA), Fault Tolerance & Disaster Recovery Metrics:**
  * **High Availability (HA):** System design ensuring continuous operational uptime by eliminating **Single Points of Failure (SPOF)** via redundant deployment across multiple **Availability Zones (AZs)** (isolated data centers within a Region) and **Load Balancers**.
  * **SLA (Service Level Agreement) "Nines":**
    * **99.9% ("Three Nines"):** $\approx 8.76\text{ hours}$ downtime/year.
    * **99.99% ("Four Nines"):** $\approx 52.6\text{ minutes}$ downtime/year.
    * **99.999% ("Five Nines"):** $\approx 5.26\text{ minutes}$ downtime/year.
  * **RPO (Recovery Point Objective):** Maximum acceptable **amount of data loss** measured in **time** (e.g., if RPO = 15 minutes, backups/replication must occur at least every 15 minutes).Answers: *"How much data can we afford to lose?"*
  * **RTO (Recovery Time Objective):** Maximum acceptable **duration of downtime** after an outage before business operations must be restored. Answers: *"How quickly must the system recover?"*
  * **Horizontal vs. Vertical Scaling:**
    * **Vertical Scaling (Scaling Up):** Adding more CPU/RAM to an existing single server (limited by hardware ceiling; requires downtime on restart).
    * **Horizontal Scaling (Scaling Out):** Adding more nodes/instances to a distributed pool behind a load balancer (elastic, cloud-native, no SPOF).

## 2.5 Rapid-Fire MCQ / MSQ / Fill-in-the-Blank Pointers (Module 2)
1. **Fill in the blank:** In Hadoop 2.x/3.x, the default HDFS block size is **`_____`** and the default replication factor is **`_____`**. $\rightarrow$ **128 MB**, **3**.
2. **Fill in the blank:** In Apache Spark, **`_____`** transformations (such as `map` and `filter`) do not require a network shuffle, whereas **`_____`** transformations (such as `groupByKey` and `join`) require a shuffle across partitions. $\rightarrow$ **Narrow**, **Wide**.
3. **Fill in the blank:** Apache **`_____`** is a scalable deep learning framework supported by Apache that combines imperative `NDArray` and symbolic graphs via its **`_____`** API and `.hybridize()` method. $\rightarrow$ **MXNet**, **Gluon**.
4. **Fill in the blank:** The **`_____`** big data architecture maintains both a Batch Layer and a Speed Layer, whereas the **`_____`** architecture uses a single stream-processing pipeline over an immutable log. $\rightarrow$ **Lambda**, **Kappa**.
5. **Fill in the blank:** **`_____`** generates synthetic minority class examples by interpolating between existing minority instances and their $k$-nearest neighbors. $\rightarrow$ **SMOTE (Synthetic Minority Over-sampling Technique)**.
6. **Fill in the blank:** The image augmentation technique **`_____`** cuts a rectangular patch from one training image and pastes it onto another, mixing ground-truth labels proportionally to the patch area. $\rightarrow$ **CutMix**.
7. **Fill in the blank:** **`_____`** measures the maximum acceptable data loss in time prior to a disaster, whereas **`_____`** measures the maximum acceptable downtime to restore service after a disaster. $\rightarrow$ **RPO (Recovery Point Objective)**, **RTO (Recovery Time Objective)**.
8. **Fill in the blank:** The managed Hadoop/Spark service on AWS is **`_____`**, and on Google Cloud is **`_____`**. $\rightarrow$ **Amazon EMR (Elastic MapReduce)**, **Cloud Dataproc**.
