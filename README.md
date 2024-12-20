# RAG-Based Summarization for Automotive Issues

This project involves creating a Retrieval-Augmented Generation (RAG) system for summarizing automotive issues using the NHTSA Recalls dataset. The implementation also explores enhancements to RAG using Graph-RAG to address holistic queries.

---

## Project Goals

1. **Develop an LLM-based summarization agent**: Retrieve and summarize relevant documents from the NHTSA Recalls dataset using RAG for automotive issues.
2. **Implement Graph-RAG**: Enhance RAG to provide insights for holistic queries, such as "most frequent recalls" or "top 5 recalls" for specific make, model, and year combinations.

---

## Dataset

### Source
[NHTSA Recalls Dataset](https://www.nhtsa.gov/nhtsa-datasets-and-apis)

### Download
Download the `FLAT_RCL.zip` file from the above link and extract the data. Relevant columns used:
- `make`
- `model`
- `year`
- `defect summary`
- `consequence summary`
- `corrective summary`

### Preprocessing
1. Extracted data for only `Ford` and `Toyota` makes.
2. Merged summaries (`defect`, `consequence`, `corrective`) into a single document.
3. Processed and vectorized the data for downstream tasks.

---

## Tasks and Implementation

### Task 1: Basic RAG Implementation

#### Steps
1. **Dataset Preparation**:
   - Converted the extracted dataset into CSV format.
   - Preprocessed the data for vector embedding creation.

2. **RAG Pipeline**:
   - Used an embedding model of choice to compute vector embeddings.
   - Integrated LLaMA 3.1 8B for summarization using groq.
   - retrieve data using similarity 
   - Summarize the retrieved documents.

#### Result
The RAG pipeline successfully retrieved and summarized documents but lacked the ability to provide a holistic view of the dataset for queries requiring aggregated insights.

---

### Task 2: Enhancing RAG with Graph-RAG

#### Why Basic RAG Fails
- Basic RAG works well for local queries where relevant documents are limited.
- For holistic queries (e.g., "most frequent recalls"), basic RAG fails to aggregate and analyze dataset-wide trends.

#### Enhancements with Graph-RAG
1. **Graph Construction**:
   - Generated a Neo4j graph database from the preprocessed CSV dataset.
     ```
     MERGE (manufacturer:CarManufacturer {make: $make})
     MERGE (model:Model {model: $model, year: $year})
     MERGE (issue:Issue {description: $issue})
     MERGE (risk:Risk {potentialRisk: $risk})
     MERGE (action:ActionTaken {action: $action})
     MERGE (manufacturer)-[:MAKE]->(model)
     MERGE (model)-[:HAS_ISSUE]->(issue)
     MERGE (issue)-[:POSES_RISK]->(risk)
     MERGE (issue)-[:HAS_ACTION]->(action)
     ```
2. **Graph-RAG Pipeline**:
   - Combined graph traversal with RAG for retrieving relevant documents and summarizing aggregated insights.

3. **Implementation Steps**:
   - Exported the CSV to Neo4j.
   - Queried the graph for holistic insights (e.g., "top 5 recalls for Ford Escape 2001").
   - Integrated results into the RAG pipeline for summarization.

#### Result
The Graph-RAG approach successfully addressed holistic queries, providing both local and aggregated insights with improved performance.

---

## Tools and Technologies

1. **Google Colab**
   - Used for GPU resources.
   - Deployed LLaMA 3.1 8B locally using `usloth` for low-resource environments.

2. **Neo4j**
   - Created and queried the graph database for holistic insights.

3. **Libraries and Frameworks**:
   - Python (pandas, numpy, etc.)
   - Vector embeddings library
   - Neo4j Python driver
   - LLaMA 3.1 8B model
---

## Results
- **RAG Output**: Successfully retrieved and summarized documents for local queries.
- **Graph-RAG Output**: Enhanced summarization for holistic queries, providing aggregated insights.

---

## Repository Structure
```
├── Graph_RAG.ipynb                          # Graph-RAG implementation using Neo4j
├── NORMAL_RAG.ipynb                         # Basic RAG implementation
├── README.md                                # Project documentation
├── data_preprocessing_(extracting_structured_csv_tables_from_txt_file).ipynb
│                                            # Data preprocessing 
├── local_llm_on_colab_gpu.ipynb             # Deploying LLaMA 3.1 8B locally on GPU
```

---

## Future Work
- combine both normal rag and graph rag and build agentic pipeline to generate accurate results 
- enhance retiever of graph rag and enhance cypher query
- Explore alternative summarization models for better performance.

---

## Contact
For questions or feedback, reach out to:
- **Email**: [anupunjalok@gmail.com](mailto:anupunjalok@gmail.com)
- **Phone**: 6206571219

---

Thank you for exploring this project!
