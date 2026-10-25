# Module 1: Programming Languages, AI/ML Libraries & SAP ERP Development
**Syllabus Coverage:** Part-B, Section 1 (i) & (ii) — Python, R, SQL, TensorFlow, PyTorch, scikit-learn, Keras, ABAP, SAPUI5/Fiori (JavaScript), SQL & Java for SAP Interfaces, SAP HANA SQLScript, OData Protocol, BAPIs & IDocs.

## 1.1 Python for Advanced Data Analysis & Scientific Computing

### Core Language Properties & Execution Model
* **Paradigm:** Multi-paradigm (**Object-Oriented**, **Imperative**, **Functional**, **Procedural**), dynamically typed, strongly typed (no implicit string-to-int coercion without casting), interpreted bytecode language.
* **Memory Management:** Automatic **Reference Counting** (`sys.getrefcount()`) combined with a **Generational Cyclic Garbage Collector** (`gc` module with 3 generations: `0`, `1`, `2`) to detect reference cycles.
* **GIL (Global Interpreter Lock):** A mutex in **CPython** that allows only **one native thread** to execute Python bytecode at a time.
  * **CPU-bound tasks:** Bypass GIL using `multiprocessing`, **Cython**, or **Numba**.
  * **I/O-bound tasks:** Handled efficiently via `threading` or `asyncio` (`async`/`await` event loop).
* **Namespace Lookup Order (LEGB Rule):** **L**ocal $\rightarrow$ **E**nclosing (nonlocal) $\rightarrow$ **G**lobal $\rightarrow$ **B**uilt-in.

### Built-in Data Structures & Complexity Cheat Sheet
| Data Type | Mutability | Ordered? | Duplicates? | Hashable? | Lookup Time | Key Methods / Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`list`** | Mutable | Yes | Yes | No | $O(n)$ | `.append()` $O(1)$, `.pop()` $O(1)$, `.insert(0, x)` $O(n)$, Timsort `.sort()` $O(n \log n)$ |
| **`tuple`** | Immutable | Yes | Yes | Yes (if elements hashable) | $O(n)$ | `.count()`, `.index()`; faster & smaller memory footprint than `list` |
| **`dict`** | Mutable | Yes (insertion order since **Python 3.7**) | Keys: No; Values: Yes | No | $O(1)$ avg | Hash table implementation; `.get(k, default)`, `.setdefault()`, `.items()` |
| **`set`** | Mutable | No | No | No | $O(1)$ avg | `.add()`, `.union()` (`\|`), `.intersection()` (`&`), `.difference()` (`-`), `.symmetric_difference()` (`^`) |
| **`frozenset`** | Immutable | No | No | Yes | $O(1)$ avg | Can be used as a `dict` key or element of another `set` |
| **`str`** | Immutable | Yes | Yes | Yes | $O(n)$ | Unicode UTF-8 sequence; `.join()`, `.split()`, `.strip()`, f-strings (`f"{val:.2f}"`) |

### High-Yield Python Concepts for MCQs & Fill-in-the-Blanks
* **Pass-by-Object-Reference (Pass-by-Assignment):** Mutable objects (`list`, `dict`, `set`) modified in-place inside a function affect the caller; immutable objects (`int`, `float`, `str`, `tuple`) rebind the local variable name.
* **Shallow vs. Deep Copy (`copy` module):**
  * `copy.copy(x)` (**Shallow Copy**): Creates a new container object, but inserts **references** to the nested child objects.
  * `copy.deepcopy(x)` (**Deep Copy**): Recursively creates new copies of the container and **all nested child objects**.
* **Default Mutable Argument Trap:** Defining `def fn(a=[]):` evaluates the default list **once at function definition time** (`fn.__defaults__`), persisting state across calls. Fix: use `def fn(a=None):`.
* **Iterators vs. Generators:**
  * **Iterator:** Any object implementing `__iter__()` and `__next__()` (raises `StopIteration` when exhausted).
  * **Generator:** Function using the `yield` keyword or generator expression `(x*x for x in range(n))`; evaluates **lazily** on demand with $O(1)$ memory overhead.
* **Decorators & Context Managers:**
  * **Decorator (`@decorator`):** Higher-order function wrapping another function; use `@functools.wraps(func)` to preserve `__name__` and `__doc__`.
  * **Context Manager (`with` statement):** Implements `__enter__()` and `__exit__(exc_type, exc_val, exc_tb)` (or `@contextlib.contextmanager`) for deterministic resource cleanup.
* **Special Dunder Methods:** `__init__` (initializer), `__new__` (constructor allocating memory), `__str__` (user-friendly string), `__repr__` (unambiguous developer string), `__call__` (makes instance callable like a function), `__len__`, `__getitem__`.
* **Functional Tools:** `map(fn, iter)`, `filter(pred, iter)`, `functools.reduce(fn, iter)`, `lambda` anonymous single-expression functions, `zip()` (stops at shortest iterable; `itertools.zip_longest()` pads with `fillvalue`).

### Python Data Analysis Ecosystem (NumPy, Pandas, SciPy)
| Library | Core Object | Key Functions / Attributes for MCQs |
| :--- | :--- | :--- |
| **NumPy** | **`ndarray`** (homogeneous contiguous C/Fortran memory array) | **Broadcasting** (aligns trailing dimensions; compatible when equal or one is `1`), `.reshape()`, `.ravel()` (view if possible) vs `.flatten()` (always copy), `np.linalg.svd()`, `np.dot()` / `@` operator, vectorized **ufuncs** |
| **Pandas** | **`Series`** (1D labeled) & **`DataFrame`** (2D tabular) | **`.loc[]`** (label-based, inclusive end slice) vs **`.iloc[]`** (integer position-based, exclusive end slice); `.groupby().agg()`, `.pivot_table()`, `.merge()` (SQL-style join), `.concat()`, `.isna()`, `.fillna()`, `.dropna()`, `.apply()` |
| **SciPy** | Scientific modules built on NumPy | `scipy.stats` (distributions, hypothesis tests), `scipy.optimize` (curve fitting, root finding), `scipy.sparse` (CSR/CSC matrices), `scipy.integrate`, `scipy.signal` |

## 1.2 R Programming for Statistical Computing

### Core R Data Structures (By Dimensionality & Homogeneity)
| Dimensions | Homogeneous (Single Data Type) | Heterogeneous (Multiple Data Types) |
| :--- | :--- | :--- |
| **1-D** | **Atomic Vector** (`c(1, 2, 3)` — `logical`, `integer`, `double`/`numeric`, `character`, `complex`, `raw`) | **List** (`list(a=1, b="text", c=TRUE)`) |
| **2-D** | **Matrix** (`matrix(data, nrow, ncol)`) | **Data Frame** (`data.frame()` / `tibble`) |
| **N-D** | **Array** (`array(data, dim=c(x,y,z))`) | — |

### High-Yield R Concepts & Statistical Functions
* **Indexing:** R uses **1-based indexing** (unlike Python's 0-based). Negative indices in R **exclude** elements (e.g., `v[-1]` returns all elements except the first).
* **Type Coercion Hierarchy (Implicit):** `logical` $\rightarrow$ `integer` $\rightarrow$ `numeric` (`double`) $\rightarrow$ `complex` $\rightarrow$ `character` (most flexible).
* **Factors (`factor()`):** Used for **categorical data** (nominal or ordinal via `ordered = TRUE`); stored internally as an **integer vector** with a `levels` attribute.
* **Subsetting Operators:**
  * `[` returns an object of the **same class** (e.g., sublist from a list); supports multiple elements.
  * `[[` extracts a **single element** from a list or data frame (unwraps the container).
  * `$` extracts a single named element/column by name (supports partial matching by default).
* **Vector Recycling Rule:** When applying binary operations to vectors of unequal length, R automatically **repeats (recycles)** the shorter vector to match the longer vector (emits a warning if longer length is not a multiple of shorter length).
* **The `apply` Family (Vectorized Loop Replacements):**
  * `apply(X, MARGIN, FUN)`: Applies `FUN` over array/matrix margins (`MARGIN = 1` for **rows**, `MARGIN = 2` for **columns**).
  * `lapply(X, FUN)`: Applies `FUN` to each element and **always returns a `list`**.
  * `sapply(X, FUN)`: Wrapper around `lapply` that **simplifies** the output to a vector or matrix if possible.
  * `vapply(X, FUN, FUN.VALUE)`: Type-safe version of `sapply` requiring an explicit return template.
  * `tapply(X, INDEX, FUN)`: Applies `FUN` to subsets of vector `X` grouped by factor `INDEX`.
  * `mapply(FUN, ...)`: Multivariate version of `sapply`.
* **Built-in Statistical Modeling & Hypothesis Testing Functions:**
  * **Linear Regression (OLS):** `lm(y ~ x1 + x2, data = df)` (Formula operators: `+` add term, `:` interaction only, `*` main effects + interaction, `- 1` remove intercept).
  * **Generalized Linear Models:** `glm(y ~ x, family = binomial(link = "logit"), data = df)` (Logistic regression uses `family = binomial`; Poisson regression uses `family = poisson`).
  * **Hypothesis Tests:** `t.test()` (Student's t-test), `aov()` / `anova()` (Analysis of Variance), `chisq.test()` (Chi-square test of independence), `shapiro.test()` (Shapiro-Wilk normality test), `ks.test()` (Kolmogorov-Smirnov test), `cor.test()` (Pearson/Spearman/Kendall correlation).
  * **Probability Distribution Prefixes:**
    * **`d`** = Probability Density/Mass Function (`dnorm`, `dbinom`, `dpois`)
    * **`p`** = Cumulative Distribution Function / CDF (`pnorm`, `pbinom`)
    * **`q`** = Quantile Function / Inverse CDF (`qnorm`, `qbinom`)
    * **`r`** = Random Number Generator (`rnorm`, `rbinom`)
* **Tidyverse Ecosystem:**
  * **`dplyr`** (Data manipulation verbs: `filter()`, `select()`, `mutate()`, `arrange()`, `summarise()`, `group_by()`, pipe operator `%>%` or native `|>`).
  * **`tidyr`** (Reshaping data: `pivot_longer()`, `pivot_wider()`, `separate()`, `unite()`, `drop_na()`).
  * **`ggplot2`** (Grammar of Graphics: **Data** + **Aesthetics** `aes()` + **Geometries** `geom_point()`, `geom_bar()`, `geom_boxplot()` + **Facets** `facet_wrap()`).
  * **`caret` / `tidymodels`** (Unified machine learning workflow and cross-validation in R).

## 1.3 SQL & Relational Database Management

### SQL Command Sublanguages
| Sublanguage | Full Name | Commands | Key Exam Facts |
| :--- | :--- | :--- | :--- |
| **DDL** | Data Definition Language | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` | Auto-commits in most RDBMS; modifies schema in Data Dictionary |
| **DML** | Data Manipulation Language | `INSERT`, `UPDATE`, `DELETE`, `MERGE` | Operates on row data; can be rolled back inside a transaction |
| **DQL** | Data Query Language | `SELECT` | Retrieves records; does not mutate database state |
| **DCL** | Data Control Language | `GRANT`, `REVOKE` | Manages privileges, permissions, and security access |
| **TCL** | Transaction Control Language | `COMMIT`, `ROLLBACK`, `SAVEPOINT`, `SET TRANSACTION` | Manages atomic transaction boundaries and isolation levels |

### `DELETE` vs. `TRUNCATE` vs. `DROP` (Classic MCQ Comparison)
| Feature | `DELETE` | `TRUNCATE` | `DROP` |
| :--- | :--- | :--- | :--- |
| **SQL Category** | **DML** | **DDL** | **DDL** |
| **`WHERE` Clause Allowed?** | **Yes** (filters specific rows) | **No** (removes all rows) | **No** (removes entire table structure + data) |
| **Row-by-Row Logging?** | Yes (slower; fires DML triggers) | Minimal page deallocation logging (faster; does not fire row triggers) | Drops schema definition, indexes, constraints, triggers |
| **Identity Reset?** | No (retains high-water mark) | Yes (resets auto-increment counter to seed) | Table ceases to exist |

### SQL Logical Query Processing Order (Execution Order vs. Written Order)
* **Written Order:** `SELECT` $\rightarrow$ `FROM` $\rightarrow$ `JOIN` $\rightarrow$ `WHERE` $\rightarrow$ `GROUP BY` $\rightarrow$ `HAVING` $\rightarrow$ `WINDOW` $\rightarrow$ `ORDER BY` $\rightarrow$ `LIMIT` / `OFFSET`.
* **Actual Execution Order (High-Yield MCQ):**
  1. **`FROM` & `JOIN` / `ON`** (Cartesian product + join predicate filtering)
  2. **`WHERE`** (Row-level filtering before grouping; **cannot** use aggregate functions or `SELECT` aliases here)
  3. **`GROUP BY`** (Buckets rows into groups)
  4. **`HAVING`** (Group-level filtering after aggregation; **can** use aggregate functions like `COUNT()`, `SUM()`, `AVG()`)
  5. **`SELECT`** (Evaluates expressions, window functions, and aliases; `DISTINCT` applied here)
  6. **`ORDER BY`** (Sorts final result set; **can** reference `SELECT` aliases)
  7. **`LIMIT` / `FETCH FIRST` / `TOP`** (Restricts output row count)

### SQL Joins & Set Operations
* **Joins:**
  * **`INNER JOIN`:** Returns rows with matching keys in **both** tables.
  * **`LEFT OUTER JOIN`:** Returns all rows from the left table + matched rows from the right table (`NULL` if no match).
  * **`RIGHT OUTER JOIN`:** Returns all rows from the right table + matched rows from the left table.
  * **`FULL OUTER JOIN`:** Returns all rows when there is a match in either left or right table.
  * **`CROSS JOIN`:** Cartesian product ($M \times N$ rows); no `ON` condition.
  * **`SELF JOIN`:** A table joined with itself using table aliases (e.g., employee-manager hierarchy).
* **Set Operators:**
  * **`UNION`:** Combines results and **removes duplicates** (requires sorting/hashing).
  * **`UNION ALL`:** Combines results and **retains duplicates** (faster, no deduplication).
  * **`INTERSECT`:** Returns common rows present in both queries.
  * **`EXCEPT` (or `MINUS` in Oracle):** Returns rows in the first query that are absent in the second query.

### SQL Window Functions (`OVER (PARTITION BY ... ORDER BY ...)`)
* **Ranking Functions:**
  * `ROW_NUMBER()`: Assigns unique sequential integers (`1, 2, 3, 4`) even for ties.
  * `RANK()`: Assigns same rank for ties, **with gaps** (`1, 2, 2, 4`).
  * `DENSE_RANK()`: Assigns same rank for ties, **without gaps** (`1, 2, 2, 3`).
  * `NTILE(n)`: Divides ordered partition into $n$ approximately equal buckets (quartiles, deciles).
* **Value/Offset Functions:**
  * `LAG(col, offset, default)`: Accesses value from a **previous** row in the window without a self-join.
  * `LEAD(col, offset, default)`: Accesses value from a **subsequent** row in the window.
  * `FIRST_VALUE(col)` / `LAST_VALUE(col)` (Note: `LAST_VALUE` default frame is `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`; often needs `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`).

### Database Normalization, ACID Properties & Isolation Levels
* **Normal Forms:**
  * **1NF:** Atomic (indivisible) column values; no repeating groups.
  * **2NF:** 1NF + **No Partial Dependency** (every non-prime attribute is fully functionally dependent on the *entire* composite candidate key).
  * **3NF:** 2NF + **No Transitive Dependency** ($A \rightarrow B$ and $B \rightarrow C$ where $C$ is non-prime).
  * **BCNF (Boyce-Codd Normal Form):** Strict 3NF; for every non-trivial functional dependency $X \rightarrow Y$, **$X$ must be a superkey**.
  * **4NF:** BCNF + No **Multi-Valued Dependencies** ($X \twoheadrightarrow Y$).
  * **5NF (PJNF):** 4NF + No **Join Dependencies** (lossless join decomposition).
* **ACID Properties & Underlying DBMS Mechanisms:**
  * **Atomicity** ("All or Nothing") $\rightarrow$ Enforced by **Undo Logs / Rollback Segments / Shadow Paging**.
  * **Consistency** (Valid state transitions preserving constraints) $\rightarrow$ Enforced by **Integrity Constraints, Triggers, and Application Logic**.
  * **Isolation** (Concurrent transactions do not interfere) $\rightarrow$ Enforced by **Concurrency Control (2PL - Two-Phase Locking, MVCC - Multi-Version Concurrency Control, Timestamp Ordering)**.
  * **Durability** (Committed changes survive crashes) $\rightarrow$ Enforced by **Write-Ahead Logging (WAL) / Redo Logs**.
* **ANSI SQL Transaction Isolation Levels:**
| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read |
| :--- | :--- | :--- | :--- |
| **Read Uncommitted** (Lowest) | Possible | Possible | Possible |
| **Read Committed** (Default in PostgreSQL, Oracle, SQL Server) | **Prevented** | Possible | Possible |
| **Repeatable Read** (Default in MySQL InnoDB) | **Prevented** | **Prevented** | Possible (Prevented in InnoDB via Next-Key Locks) |
| **Serializable** (Highest) | **Prevented** | **Prevented** | **Prevented** |

## 1.4 AI & Machine Learning Libraries (TensorFlow, PyTorch, scikit-learn, Keras)

### Comparative Architecture Matrix
| Feature | **scikit-learn** | **TensorFlow** | **Keras** | **PyTorch** |
| :--- | :--- | :--- | :--- | :--- |
| **Primary Domain** | Classical ML (Tabular data, Regression, Classification, Clustering, Preprocessing) | End-to-end Deep Learning & Production Deployment (Cloud, Mobile, Edge, Web) | High-level Deep Learning API (`tf.keras` & Multi-backend Keras 3: JAX, TF, PyTorch) | Deep Learning Research & Production (Dynamic Graph, TorchScript, `torch.compile`) |
| **Core Data Structure** | NumPy `ndarray` / SciPy Sparse Matrix / Pandas `DataFrame` | `tf.Tensor` (Immutable) & `tf.Variable` (Mutable trainable state) | Backend Tensor (`KerasTensor`) | `torch.Tensor` (Mutable or out-of-place; `.to('cuda')`) |
| **Computation Graph** | Eager CPU execution (via NumPy/Cython/Joblib) | **Eager by default in TF 2.x**; Static Graph via `@tf.function` (**AutoGraph** + **XLA**) | Declarative layer/model graph compiled via `.compile()` | **Dynamic Computation Graph (Define-by-Run)** via `torch.autograd`; compiled via `torch.compile` (TorchDynamo) |
| **Gradient Mechanism** | Analytical / numerical solvers (L-BFGS, SAG, SAGA, Coordinate Descent) | **`tf.GradientTape()`** context manager | Handled automatically inside `model.fit()` or overridden in `train_step()` | **`loss.backward()`** + **`optimizer.step()`** + **`optimizer.zero_grad()`** |
| **Model Deployment** | `joblib.dump()` / `pickle` / **ONNX** (`skl2onnx`) | **SavedModel** (`saved_model.pb`), **TF Serving**, **TF Lite** (Mobile/IoT), **TensorFlow.js** | `.keras` (v3 archive), `.h5` (legacy HDF5), or TF `SavedModel` | **`state_dict`** (`.pt`/`.pth`), **TorchScript** (`torch.jit.trace` / `script`), **TorchServe**, **ONNX** |

### 1. scikit-learn (`sklearn`) Deep Dive
* **Unified API Design (3 Core Interfaces):**
  1. **Estimator:** Any object that learns from data via **`.fit(X, y)`** (sets learned attributes ending with a trailing underscore, e.g., `coef_`, `intercept_`, `cluster_centers_`).
  2. **Transformer:** Estimator that transforms data via **`.transform(X)`** and **`.fit_transform(X)`** (e.g., `StandardScaler`, `MinMaxScaler`, `OneHotEncoder`, `SimpleImputer`, `PCA`).
  3. **Predictor:** Estimator that makes predictions via **`.predict(X)`**, **`.predict_proba(X)`** (class probabilities), or **`.decision_function(X)`** (distance to hyperplane), and evaluates via **`.score(X, y)`**.
* **Preventing Data Leakage (`Pipeline` & `ColumnTransformer`):**
  * **`sklearn.pipeline.Pipeline`:** Chains preprocessing transformers and a final predictor so `.fit()` is called **only on the training folds** during cross-validation (`GridSearchCV`, `RandomizedSearchCV`, `StratifiedKFold`).
  * **`ColumnTransformer`:** Applies distinct preprocessing pipelines to numerical vs. categorical columns in parallel.
* **Key Algorithms in `sklearn`:**
  * **Supervised:** `LinearRegression`, `Ridge` ($L_2$ penalty), `Lasso` ($L_1$ penalty — induces sparsity/feature selection), `ElasticNet` ($L_1 + L_2$), `LogisticRegression`, `SVC`/`SVR` (Support Vector Machines with RBF/Poly/Linear kernels), `DecisionTreeClassifier`, `RandomForestClassifier` (Bagging), `GradientBoostingClassifier`, `HistGradientBoostingClassifier`, `AdaBoostClassifier`, `KNeighborsClassifier`, `GaussianNB` / `MultinomialNB`.
  * **Unsupervised:** `KMeans`, `DBSCAN` (density-based, finds arbitrary shapes & noise without pre-specifying $k$), `AgglomerativeClustering`, `PCA` (Principal Component Analysis), `TSNE` (manifold visualization), `IsolationForest` (anomaly detection).
  * **Evaluation Metrics (`sklearn.metrics`):**
    * Classification: **Accuracy**, **Precision** ($\frac{TP}{TP+FP}$), **Recall / Sensitivity / TPR** ($\frac{TP}{TP+FN}$), **F1-Score** ($\frac{2 \cdot P \cdot R}{P+R}$), **ROC-AUC** (TPR vs FPR), **PR-AUC**, `confusion_matrix`, `log_loss`.
    * Regression: **MSE**, **RMSE**, **MAE**, **$R^2$ (Coefficient of Determination)**, **Adjusted $R^2$**.
    * Clustering: **Silhouette Score** ($[-1, +1]$), **Davies-Bouldin Index**, **Adjusted Rand Index (ARI)**.

### 2. TensorFlow (`tf`) Deep Dive
* **TF 1.x vs. TF 2.x:** TF 1.x used declarative static graphs (`tf.Session().run()`); **TF 2.x** enables **Eager Execution by default** and integrates `tf.keras` as its official high-level API.
* **Key Components:**
  * **`tf.data.Dataset` API:** High-performance input pipelines using `.map()`, `.batch()`, `.shuffle(buffer_size)`, `.prefetch(tf.data.AUTOTUNE)`, and `.cache()` to prevent GPU starvation.
  * **`tf.GradientTape`:** Records operations on watched `tf.Variable` tensors for automatic differentiation (`tape.gradient(loss, trainable_vars)`).
  * **`tf.distribute.Strategy`:** Distributed training APIs:
    * `MirroredStrategy`: Synchronous data-parallel training across **multiple GPUs on a single machine** (uses NVIDIA NCCL AllReduce).
    * `MultiWorkerMirroredStrategy`: Synchronous training across **multiple machines (nodes)**.
    * `TPUStrategy`: Training on Google **Tensor Processing Units (TPUs)**.
    * `ParameterServerStrategy`: Asynchronous multi-worker training with dedicated parameter servers and workers.
  * **TensorBoard:** Visualization toolkit for tracking loss/accuracy curves, computation graphs, histograms, and embeddings.
  * **XLA (Accelerated Linear Algebra):** Domain-specific compiler (`jit_compile=True`) that fuses operators into optimized machine code for CPU/GPU/TPU.

### 3. Keras Deep Dive
* **3 Ways to Build Models in Keras (High-Yield MCQ):**
  1. **Sequential API (`keras.Sequential`):** Linear stack of layers (single-input, single-output).
  2. **Functional API (`keras.Model(inputs=..., outputs=...)`):** Directed Acyclic Graph (DAG) of layers; supports **multi-input, multi-output, shared layers, and residual/skip connections** (e.g., ResNet, Inception).
  3. **Model Subclassing (`class MyModel(keras.Model)`):** Defines layers in `__init__()` and custom forward-pass control flow in **`call(self, inputs, training=False)`**; offers maximum flexibility for research.
* **Key Keras Lifecycle Methods & Callbacks:**
  * `model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])`
  * `model.fit(X_train, y_train, epochs=50, batch_size=32, validation_split=0.2, callbacks=[...])`
  * **Callbacks:** `EarlyStopping(monitor='val_loss', patience=5, restore_best_weights=True)`, `ModelCheckpoint`, `ReduceLROnPlateau`, `TensorBoard`.
* **Loss Function Selection Cheat Sheet:**
  * **Binary Classification (1 sigmoid output):** `binary_crossentropy`.
  * **Multi-class Classification (One-Hot encoded targets, softmax output):** `categorical_crossentropy`.
  * **Multi-class Classification (Integer-encoded targets $0, 1, \dots, K-1$, softmax output):** `sparse_categorical_crossentropy`.
  * **Regression:** `mean_squared_error` (MSE), `mean_absolute_error` (MAE), `Huber` loss (less sensitive to outliers).

### 4. PyTorch (`torch`) Deep Dive
* **Core Modules:**
  * `torch.nn.Module`: Base class for all neural network modules; custom models subclass `nn.Module` and implement **`forward(self, x)`**.
  * `torch.autograd`: Tape-based automatic differentiation engine tracking tensors with **`requires_grad=True`**.
  * `torch.utils.data.Dataset` (implements `__len__` and `__getitem__`) and **`DataLoader`** (handles batching, `shuffle=True`, multiprocess `num_workers`, and `pin_memory=True` for fast CPU-to-GPU transfer).
* **Standard PyTorch Training Loop vs. Evaluation Mode (Frequent Exam Trap):**
  * **Training Step (5 Mandatory Lines):**
    1. `optimizer.zero_grad()` (clears accumulated gradients from previous step — **PyTorch accumulates gradients by default!**)
    2. `outputs = model(inputs)` (forward pass)
    3. `loss = criterion(outputs, targets)` (compute loss)
    4. `loss.backward()` (backward pass via autograd)
    5. `optimizer.step()` (update weights)
  * **Evaluation / Inference Mode (2 Mandatory Mechanisms):**
    * **`model.eval()`:** Switches **Dropout** (disables dropping neurons) and **BatchNorm** (uses running mean/variance instead of batch statistics) to evaluation behavior.
    * **`with torch.no_grad():`** (or `@torch.no_grad()`): Disables gradient tracking in `autograd`, reducing memory consumption and speeding up inference.
* **Distributed Training:** `torch.nn.DataParallel` (single-process multi-thread, subject to Python GIL bottleneck) vs. **`torch.nn.parallel.DistributedDataParallel` (DDP)** (multi-process, one process per GPU, uses NCCL ring-AllReduce — recommended standard) and **FSDP (Fully Sharded Data Parallel)** for large models.

## 1.5 SAP ERP Backend & Frontend Development (ABAP, SAPUI5/Fiori, Java, HANA SQLScript, OData, BAPIs & IDocs)

### 1. ABAP (Advanced Business Application Programming)
* **Architecture (3-Tier Client/Server):**
  1. **Presentation Layer:** SAP GUI / Web Browser (Fiori).
  2. **Application Layer:** ABAP Application Server (**AS ABAP**) with Dispatcher and Work Processes (**DIA** Dialog, **BTC** Background, **UPD** Update, **ENQ** Enqueue/Lock management, **SPO** Spool).
  3. **Database Layer:** Underlying DBMS (SAP HANA, Oracle, DB2, SQL Server).
* **ABAP Data Dictionary (DDIC — Transaction `SE11`):**
  * **Domain:** Defines **technical attributes** (data type, length, decimal places, value range/fixed values).
  * **Data Element:** Defines **semantic attributes** (field labels, documentation, F1 help) and references a **Domain**.
  * **Tables:**
    * **Transparent Table:** **1-to-1** mapping between DDIC definition and physical database table.
    * **Pooled Table:** **Many-to-1** mapping; multiple small DDIC tables stored inside a single physical **Table Pool** (typically system/configuration data in classic ERP).
    * **Cluster Table:** **Many-to-1** mapping; multiple related DDIC tables sharing primary keys stored compressed in a physical **Table Cluster** (e.g., classic `BSEG` inside `RFBLG` before S/4HANA; converted to transparent tables in SAP HANA).
  * **Structure:** Record definition with **no physical database data**; exists only at runtime in ABAP programs.
  * **Views (`SE11`):** Database View (`INNER JOIN`), Projection View (single table subset of fields), Help View (`OUTER JOIN` for search helps), Maintenance View (`SM30`).
  * **Lock Objects:** Prefix **`E`** (`EZ...` or `EY...`); automatically generates two function modules: **`ENQUEUE_<lock_object>`** (set lock) and **`DEQUEUE_<lock_object>`** (release lock).
* **Internal Tables (In-Memory Runtime Tables):**
  1. **Standard Table:** Linear index access; `READ TABLE` is $O(n)$ (or $O(\log n)$ with `BINARY SEARCH` after `SORT`).
  2. **Sorted Table:** Always maintained sorted by key; `READ TABLE` by key uses **Binary Search** $O(\log n)$; no explicit `SORT` allowed.
  3. **Hashed Table:** Accessed **exclusively via unique key** using a hash algorithm; **constant access time $O(1)$** regardless of table size; cannot be accessed by row index.
* **ABAP Modularization Techniques:**
  * **Source Code Modularization:** **Include Programs** (not executable standalone) and **Macros** (`DEFINE ... END-OF-DEFINITION`).
  * **Processing Block Modularization:** **Subroutines** (`FORM ... ENDFORM`, called via `PERFORM`), **Function Modules** (`FUNCTION ... ENDFUNCTION`, stored in Function Groups in **`SE37`**, called via `CALL FUNCTION`), and **Methods** (Object-Oriented ABAP classes in **`SE24`**).
* **Classical vs. Interactive Report Events (Execution Order):**
  1. **`LOAD-OF-PROGRAM`** (Triggered first when program loads into memory)
  2. **`INITIALIZATION`** (Sets default values on selection screen before display)
  3. **`AT SELECTION-SCREEN OUTPUT`** (PBO of selection screen; modifies screen attributes dynamically)
  4. **`AT SELECTION-SCREEN`** (PAI validation of user inputs)
  5. **`START-OF-SELECTION`** (Default event where main data extraction SQL executes)
  6. **`END-OF-SELECTION`** (After logical database finishes reading)
  7. **`TOP-OF-PAGE`** / **`END-OF-PAGE`** (List headers and footers)
  8. **Interactive Events:** **`AT LINE-SELECTION`** (double-click / F2 on list line; system variable `SY-LSIND` holds list level $0 \dots 20$), **`AT USER-COMMAND`**, **`TOP-OF-PAGE DURING LINE-SELECTION`**.
* **ALV (ABAP List Viewer):** Modern OO class **`CL_SALV_TABLE`** or control wrapper **`CL_GUI_ALV_GRID`** (or function module `REUSE_ALV_GRID_DISPLAY`).
* **Customer Enhancements & Modifications:**
  * **User Exits:** Subroutine (`PERFORM USEREXIT_...`) mainly in SD module; requires SAP Access Key (technically a modification in `MV45AFZZ`).
  * **Customer Exits (`SMOD` / `CMOD`):** Function module exits (`CALL CUSTOMER-FUNCTION '001'`), menu exits, screen exits; no Access Key required.
  * **BAdI (Business Add-Ins — `SE18` Definition / `SE19` Implementation):** Object-Oriented enhancement technique supporting **multiple active implementations** and filter-dependent execution.
  * **Enhancement Framework:** Implicit Enhancement Points (automatically present at start/end of subroutines, methods, function modules) and Explicit Enhancement Points/Sections (**Enhancement Spots**).

### 2. Essential SAP Transaction Codes (T-Codes) for MCQs
| T-Code | Purpose / Component | T-Code | Purpose / Component |
| :--- | :--- | :--- | :--- |
| **`SE11`** | ABAP Data Dictionary (Tables, Views, Domains) | **`SE16` / `SE16N`** | Data Browser (View table contents) |
| **`SE38`** | ABAP Editor (Create/execute reports) | **`SE80`** | Object Navigator (Integrated development workbench) |
| **`SE37`** | Function Builder (Create/test Function Modules & RFCs) | **`SE24`** | Class Builder (Global OO ABAP Classes & Interfaces) |
| **`SM30`** | Table Maintenance Generator View | **`ST22`** | ABAP Runtime Error / Short Dump Analysis |
| **`ST05`** | Performance Trace (SQL Trace, Buffer Trace, RFC Trace) | **`SAT` / `SE30`** | ABAP Runtime Analysis / Profiler |
| **`SM59`** | RFC Destinations Configuration & Connection Test | **`BAPI`** | BAPI Explorer (Browse Business Object Repository & BAPIs) |
| **`SEGW`** | SAP Gateway Service Builder (Create OData projects) | **`/IWFND/MAINT_SERVICE`** | Activate & Maintain OData Services + SAP Gateway Client |
| **`WE02` / `WE05`** | Display IDoc Lists & Monitor IDoc Statuses | **`WE19`** | Test Tool for IDoc Processing (Debugging/Inbound test) |
| **`WE20`** | Partner Profiles Configuration (Inbound/Outbound IDoc parameters) | **`WE21`** | Port Definition in IDoc Processing (tRFC, File, XML) |
| **`WE30`** | IDoc Type Development (Basic & Extension types) | **`WE31`** | IDoc Segment Editor (Create fields inside segments) |
| **`WE81`** | Logical Message Types Definition | **`WE82`** | Link Message Type (`WE81`) to Basic IDoc Type (`WE30`) |
| **`BD87`** | Status Monitor for ALE Messages (Reprocess failed IDocs) | **`SALE` / `BD64`** | ALE Implementation Guide / Distribution Model Maintenance |

### 3. SAPUI5 & SAP Fiori (JavaScript Front-End Development)
* **Relationship:**
  * **SAP Fiori:** The **User Experience (UX) Design System** and guidelines (Role-based, Adaptive/Responsive, Simple, Coherent, Delightful).
  * **SAPUI5 (SAP User Interface for HTML5):** The **JavaScript/HTML5/CSS3 technical framework** (and its open-source sibling **OpenUI5**) used to build Fiori apps.
* **SAP Fiori 3 App Types:**
  1. **Transactional Apps:** Task-based operations (Create/Change/Approve leave requests, purchase orders); can run on any DB or HANA.
  2. **Analytical Apps:** Real-time visual overview of KPIs, charts, and complex aggregations (**requires SAP HANA** + CDS views).
  3. **Fact Sheet / Object Page Apps:** 360-degree view of master data/business objects with contextual navigation and enterprise search (**requires SAP HANA**).
* **SAPUI5 MVC Architecture:**
  * **Model:** Holds data and manages server communication. Four built-in model types:
    1. **`ODataModel`** (`v2.ODataModel` / `v4.ODataModel` — **Server-side** model supporting two-way/one-way binding, batching `$batch`, filtering).
    2. **`JSONModel`** (**Client-side** model for small datasets/local state).
    3. **`XMLModel`** (**Client-side** model for XML data).
    4. **`ResourceModel`** (**Client-side** model for internationalization **`i18n.properties`** bundles — supports **One-Time** binding only).
  * **View:** Defines UI layout (`XML` View is SAP recommended best practice; also supports `JS`, `JSON`, and `HTML` views).
  * **Controller:** JavaScript file handling user events and lifecycle hooks:
    * **`onInit()`:** Called **once** when the view is instantiated (ideal for model initialization and route binding).
    * **`onBeforeRendering()`:** Called **every time** before the view is rendered/re-rendered.
    * **`onAfterRendering()`:** Called **every time** after DOM rendering completes (ideal for DOM manipulation).
    * **`onExit()`:** Called **once** when the view is destroyed (clean up event listeners/timers).
* **SAPUI5 Data Binding Modes & Types:**
  * **Binding Modes:** **One-Way** (Model $\rightarrow$ View), **Two-Way** (Model $\leftrightarrow$ View), **One-Time** (Model $\rightarrow$ View once at initialization, e.g., `i18n`).
  * **Binding Types:** **Property Binding** (`text="{/customerName}"`), **Element/Context Binding** (`bindElement("/Customers('1001')")`), **Aggregation/List Binding** (`items="{/Orders}"`).
* ** Key Configuration File:** **`manifest.json`** (**App Descriptor**) — declares app ID, data sources (OData URIs), models, routing configuration, dependencies, and Fiori Launchpad inbound tiles.
* **SAP Fiori Elements:** Metadata-driven framework generating standard UI floorplans automatically from **OData + UI Annotations** (List Report, Object Page, Overview Page, Analytical List Page, Worklist) with minimal custom JavaScript.

### 4. SQL & Java for SAP Interface Development
* **Open SQL (ABAP SQL) vs. Native SQL:**
  * **Open SQL:** Database-independent SQL subset integrated into ABAP; translated by the **Database Interface (DBI)** into native DB commands and supports SAP Table Buffering.
  * **Native SQL (`EXEC SQL ... ENDEXEC` or ADBC `CL_SQL_STATEMENT`):** Database-specific SQL sent directly to the DBMS bypassing SAP table buffers.
* **Java Connectivity in SAP:**
  * **SAP JCo (SAP Java Connector):** Middleware library enabling bidirectional communication between **Java applications** and **SAP ABAP systems** via **RFC (Remote Function Call)** protocol (supports both Inbound Java $\rightarrow$ ABAP calls and Outbound ABAP $\rightarrow$ Java calls).
  * **SAP HANA JDBC Driver (`ngdbc.jar`):** Standard Java Database Connectivity driver (`com.sap.db.jdbc.Driver`) for connecting Java/Spring Boot services directly to SAP HANA databases (Default port pattern: **`3<instance_nr>15`** for single-container or **`3<instance_nr>13` / `3<instance_nr>41`** for multi-tenant tenant DBs).
  * **SAP NetWeaver Application Server Java (AS Java) & SAP BTP (Business Technology Platform):** Supports **SAP Cloud SDK for Java** and **CAP (Cloud Application Programming Model)** using Java/Spring Boot or Node.js.
  * **SAP PI/PO (Process Integration / Process Orchestration) & SAP CPI (Cloud Integration):** Uses Java user-defined functions (UDFs), Java mappings, and adapter modules to transform and route messages across heterogeneous enterprise systems.

### 5. SAP HANA SQLScript
* **Core Philosophy — "Code-to-Data" Paradigm (Code Pushdown):** Instead of transferring millions of rows from the database to the ABAP application server for looping/aggregation ("Data-to-Code"), complex data-intensive logic is pushed down to execute **directly inside the SAP HANA in-memory columnar engine**.
* **What is SQLScript?** SAP HANA's extended procedural and declarative SQL scripting language used to write **Stored Procedures**, **User-Defined Functions (Scalar UDF & Table UDF)**, and **AMDP (ABAP Managed Database Procedures)**.
* **Key SQLScript Concepts for MCQs:**
  * **Declarative vs. Imperative Logic:**
    * **Declarative (Table Variables):** Queries assigned to intermediate table variables (`lt_sales = SELECT ... FROM ...;`) are compiled into a **Dataflow Graph** and executed **in parallel** by HANA engines.
    * **Imperative (Control Flow):** `IF...ELSE`, `WHILE`, `FOR` loops, cursors, and scalar assignments **break parallelism** and force sequential execution (avoid cursors when set-based SQL is possible).
  * **Procedure Syntax (`CREATE PROCEDURE`):**
    * Parameters: **`IN`** (input-only), **`OUT`** (output-only), **`INOUT`** (scalar input/output).
    * **`LANGUAGE SQLSCRIPT`** and **`SQL SECURITY INVOKER`** (or `DEFINER`).
    * **`READS SQL DATA`:** Marks a procedure as **read-only** (no DDL/DML allowed), enabling **parallel execution** and allowing it to be called from parallelized contexts.
  * **Anonymous Blocks:** `DO BEGIN ... END;` for executing ad-hoc SQLScript without creating a persistent database procedure.
  * **AMDP (ABAP Managed Database Procedures):**
    * Developed in **Eclipse ADT (ABAP Development Tools)** inside a global ABAP class implementing marker interface **`IF_AMDP_marker_HDB`**.
    * Method implementation uses **`BY DATABASE PROCEDURE FOR HDB LANGUAGE SQLSCRIPT OPTIONS READ-ONLY USING <table_list>`**.
    * Lifecycle managed completely via standard ABAP transport requests (creates the HANA procedure upon first execution/activation).
  * **CDS Views (Core Data Services) vs. AMDP:**
    * **ABAP CDS Views:** Declarative, open-SQL based data modeling (single result set); always first choice for Code Pushdown.
    * **AMDP / SQLScript:** Used when logic requires **multiple output tables**, complex procedural branching, or HANA-specific functions not supported in CDS.
  * **CE Functions (Calculation Engine Functions):** Built-in functions like `CE_COLUMN_TABLE`, `CE_JOIN`, `CE_PROJECTION`, `CE_AGGREGATION` that historically interacted directly with the Calculation Engine ( note: never mix CE functions and standard SQL inside the same procedure as it causes engine context-switching overhead; modern HANA recommends pure SQL over CE functions).

### 6. OData Protocol (Open Data Protocol) in SAP
* **Definition:** An OASIS standard (**REST-based**) protocol (`OData V2` and `OData V4`) for building and consuming queryable, interoperable data APIs over **HTTP/HTTPS** using **AtomPub (XML)** or **JSON** payloads. Called the **"SQL for the Web"**.
* **OData Service Structure & Metadata:**
  * **Service Document (`/sap/opu/odata/sap/<SERVICE_NAME>/`):** Lists exposed **Entity Sets** (collections).
  * **Service Metadata Document (`$metadata`):** XML (**EDMX** / **CSDL** - Conceptual Schema Definition Language) describing **Entity Types** (structures with key properties), **Entity Sets** (table-like collections), **Associations/Navigation Properties** (relationships), and **Function Imports** (custom operations not fitting CRUD).
* **OData CRUD Mapping to HTTP Methods & ABAP DPC_EXT Methods:**
| CRUD Operation | HTTP Method | OData URI Pattern | ABAP Gateway `DPC_EXT` Method Implemented |
| :--- | :--- | :--- | :--- |
| **Query (Read List)** | **`GET`** | `/EntitySet` | `<ENTITY>SET_GET_ENTITYSET` |
| **Read (Single Record)** | **`GET`** | `/EntitySet('Key1')` | `<ENTITY>SET_GET_ENTITY` |
| **Create** | **`POST`** | `/EntitySet` (+ JSON/XML body) | `<ENTITY>SET_CREATE_ENTITY` |
| **Update (Full Replace)** | **`PUT`** | `/EntitySet('Key1')` | `<ENTITY>SET_UPDATE_ENTITY` |
| **Update (Partial)** | **`PATCH` / `MERGE`** | `/EntitySet('Key1')` | `<ENTITY>SET_UPDATE_ENTITY` |
| **Delete** | **`DELETE`** | `/EntitySet('Key1')` | `<ENTITY>SET_DELETE_ENTITY` |

* **OData System Query Options (All prefixed with `$` in OData V2):**
  * **`$filter`:** Filters collection rows (`?$filter=Country eq 'IN' and Amount gt 1000` — operators: `eq`, `ne`, `gt`, `ge`, `lt`, `le`, `and`, `or`, `not`, `substringof`).
  * **`$select`:** Projects specific fields/columns (`?$select=OrderID,CustomerID`).
  * **`$expand`:** Eagerly loads related child entities in a single HTTP request via Navigation Properties (`?$expand=ToOrderItems`).
  * **`$orderby`:** Sorts results (`?$orderby=CreatedAt desc`).
  * **`$top` & `$skip`:** Client-driven pagination (`?$top=20&$skip=40` fetches records 41–60).
  * **`$count` / `$inlinecount=allpages`:** Returns total matching record count alongside paged data.
  * **`$format`:** Specifies response format (`?$format=json` or `?$format=xml`).
  * **`$batch`:** Bundles **multiple HTTP requests** (GETs and ChangeSet POST/PUT/DELETEs) into a single `POST /.../$batch` multipart/mixed payload.
* **SAP Gateway Runtime Classes (Generated by T-Code `SEGW`):**
  * **`MPC` (Model Provider Class)** & **`MPC_EXT`:** Defines the OData metadata model in code.
  * **`DPC` (Data Provider Class)** & **`DPC_EXT`:** Contains the runtime data retrieval and CRUD business logic (developers redefine methods in **`DPC_EXT`**).

### 7. BAPIs (Business Application Programming Interfaces) & IDocs (Intermediate Documents)

#### BAPIs (Business Application Programming Interfaces)
* **Definition:** Standardized, object-oriented programming interfaces (methods of **SAP Business Objects** stored in the **BOR — Business Object Repository**, T-code **`BAPI`** / **`SWO1`**) that allow external applications (Java, .NET, Web) and internal ABAP programs to access SAP business processes and data.
* **Key Technical Characteristics of a BAPI (High-Yield Exam Facts):**
  1. Implemented technically as **Remote-Enabled Function Modules (RFC Function Modules)** in **`SE37`** with naming convention **`BAPI_<BusinessObject>_<Method>`** (e.g., `BAPI_SALESORDER_CREATEFROMDAT2`, `BAPI_PO_CREATE1`, `BAPI_USER_GET_DETAIL`).
  2. **Always Synchronous** by default (returns results immediately to caller).
  3. **No UI Dialogs/Screens:** BAPIs must **never** trigger popups, `MESSAGE` error dialogs, or `CALL SCREEN` because they run in background/RFC contexts.
  4. **Standardized Error Handling (`RETURN` Parameter):** Uses structure **`BAPIRET2`** (or `BAPIRETURN1`). Message types inside `RETURN-TYPE`:
     * **`S`** = Success
     * **`I`** = Information
     * **`W`** = Warning
     * **`E`** = Error (Business validation failed; transaction not saved)
     * **`A`** = Abort / Termination (Fatal error)
     * *(Exam Tip: A BAPI is considered failed if `RETURN` contains any message of type **`E`** or **`A`**).*
  5. **Transaction Control:** Standard BAPIs do **not** execute `COMMIT WORK` internally. The caller must explicitly call **`BAPI_TRANSACTION_COMMIT`** (with `WAIT = 'X'` for synchronous update) to persist changes, or **`BAPI_TRANSACTION_ROLLBACK`** to discard changes inside the LUW (Logical Unit of Work).

#### IDocs (Intermediate Documents)
* **Definition:** Standard SAP data container format used for **asynchronous** exchange of business transaction data between SAP systems (**ALE — Application Link Enabling**) or between SAP and non-SAP external partners (**EDI — Electronic Data Interchange**).
* **IDoc Structure (3 Parts):**
  1. **Control Record (1 per IDoc — Table `EDIDC`):** Stores routing and metadata: IDoc Number, Direction (`1` = **Outbound**, `2` = **Inbound**), Sender/Receiver Partner Numbers, **Message Type** (semantic meaning, e.g., `ORDERS`, `MATMAS`, `DEBMAS`), and **Basic IDoc Type** (technical structure, e.g., `ORDERS05`, `MATMAS05`).
  2. **Data Records (Multiple per IDoc — Table `EDID4`):** Contains actual payload organized into hierarchical **Segments** (`E1...` for SAP standard segments, `Z1...` for custom extension segments) up to 1000 bytes of character data (`SDATA`) per segment.
  3. **Status Records (Multiple per IDoc — Table `EDIDS`):** Chronological audit log of every processing milestone the IDoc passed through.
* **IDoc Status Codes (Critical MCQ Fact):**
  * **Outbound IDoc Statuses range from `01` to `49`:**
    * **`01`:** IDoc created
    * **`03`:** Data passed to port OK (Successfully sent from SAP)
    * **`29`:** Error in ALE service
    * **`30`:** IDoc ready for dispatch (ALE service)
  * **Inbound IDoc Statuses range from `50` to `75`:**
    * **`50`:** IDoc added (received by SAP)
    * **`51`:** Application document **not** posted (Inbound **Error** — reprocess via **`BD87`**)
    * **`53`:** Application document **posted** (Inbound **Success**)
    * **`64`:** IDoc ready to be transferred to application (queued; process via program `RBDAPP01` or `BD87`)
    * **`68`:** Error — no further processing (marked for deletion/cancelled)

### BAPI vs. IDoc Comparison Table
| Attribute | **BAPI** | **IDoc** |
| :--- | :--- | :--- |
| **Communication Mode** | Primarily **Synchronous** (Request/Response) | **Asynchronous** (Fire-and-forget / Queued) |
| **Underlying Technology** | **RFC** (Remote Function Call) & **BOR** (`SWO1`) | **ALE** (SAP-to-SAP) & **EDI** (SAP-to-Non-SAP) |
| **Error Handling** | Immediate return via **`BAPIRET2`** (`RETURN` table) | Status records (**`EDIDS`**: `51` error, `53` success) monitored in **`WE02`/`BD87`** |
| **Best Use Case** | Real-time UI queries, immediate validation, synchronous order creation | Bulk master data replication (`MATMAS`), EDI B2B invoices/ASN, decoupled batch integration |

## 1.6 Rapid-Fire MCQ / MSQ / Fill-in-the-Blank Pointers (Module 1)
1. **Fill in the blank:** In Python, the **`_____`** lock prevents multiple native threads from executing Python bytecodes simultaneously in CPython. $\rightarrow$ **Global Interpreter Lock (GIL)**.
2. **Fill in the blank:** In Pandas, **`_____`** performs label-based indexing (inclusive of the stop bound), whereas **`_____`** performs integer position-based indexing (exclusive of the stop bound). $\rightarrow$ **`.loc[]`**, **`.iloc[]`**.
3. **Fill in the blank:** In R, the function **`_____`** applies a function over a list and always returns a list, whereas **`_____`** simplifies the output to a vector or matrix. $\rightarrow$ **`lapply()`**, **`sapply()`**.
4. **Fill in the blank:** In SQL, **`_____`** filters individual rows before grouping, while **`_____`** filters aggregated groups after `GROUP BY`. $\rightarrow$ **`WHERE`**, **`HAVING`**.
5. **Fill in the blank:** The SQL window function **`_____`** assigns tied rows the same rank without leaving gaps in subsequent rank numbers. $\rightarrow$ **`DENSE_RANK()`**.
6. **Fill in the blank:** In PyTorch, calling **`_____`** disables Dropout and sets BatchNorm to use running statistics during inference, while **`_____`** disables gradient calculation in autograd. $\rightarrow$ **`model.eval()`**, **`torch.no_grad()`**.
7. **Fill in the blank:** In SAP ABAP, a **`_____`** internal table provides constant $O(1)$ lookup time regardless of the number of rows. $\rightarrow$ **Hashed**.
8. **Fill in the blank:** In SAPUI5, the **`_____`** model is a server-side model supporting two-way data binding, while **`_____`** is the App Descriptor file. $\rightarrow$ **`ODataModel`**, **`manifest.json`**.
9. **Fill in the blank:** After calling a modifying BAPI in ABAP, you must call **`_____`** to save the changes to the database. $\rightarrow$ **`BAPI_TRANSACTION_COMMIT`**.
10. **Fill in the blank:** In SAP IDocs, status code **`_____`** indicates an outbound IDoc was successfully passed to the port, **`_____`** indicates an inbound application error, and **`_____`** indicates an inbound document was successfully posted. $\rightarrow$ **`03`**, **`51`**, **`53`**.
