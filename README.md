# AI SQL Query Generator
This demonstration application presents a Query RAG framework for AI-powered SQL generation over uploaded CSV datasets. Users provide natural-language questions, and the system automatically profiles the data, extracts schema-aware metadata, and retrieves only the most relevant context needed to ground SQL synthesis. The design explicitly addresses current RAG limitations, including retrieval noise, weak ranking over structured data, and the tradeoff between long-context prompting and generation reliability. 

Instead of overloading the model context window, the pipeline performs selective retrieval and staged reasoning across MCP-like components for schema interpretation, query planning, SQL generation, validation, and execution. The result is a more controllable and efficient natural-language analytics workflow that reduces hallucinations, improves SQL fidelity, and demonstrates why retrieval orchestration remains essential even in the era of large context-window models.


## Components

📤 Ingestion pipeline for CSV uploads with drag-and-drop interface, enabling user-provided tabular datasets to be parsed, validated, and staged for downstream processing.
📊 Automated schema inference and PostgreSQL table materialization, including column type detection, structural profiling, and dynamic table creation based on uploaded dataset characteristics.
🤖 LLM-driven natural language–to–SQL translation, transforming user intent into executable SQL queries through prompt-engineered semantic parsing over relational metadata.
🔍 Query reasoning, validation, and execution safety checks, with pre-execution analysis to detect malformed logic, incompatible joins, invalid column references, and other structural inconsistencies.
💡 Iterative error recovery and SQL regeneration loop, leveraging model-assisted debugging to reinterpret failed queries, correct syntax/semantic issues, and resubmit improved SQL candidates.
🎯 Context-aware response generation grounded in dataset schema and query results, ensuring outputs are informed by available tables, attribute relationships, and returned records rather than generic text generation.

## Architecture

https://lucid.app/lucidchart/20f33f65-6336-4d5d-86c4-db985ac62f31/edit?invitationId=inv_32aa0584-951d-4180-9cc2-137e61241edd&page=0_0#
## Nuances between Vector Search and Database search
<img width="611" height="433" alt="Screenshot 2026-03-19 202419" src="https://github.com/user-attachments/assets/4d437879-0c6c-4100-a882-8c9765c84d41" />\

## The application consists of two main components:

## Frontend (`ui/src/App.tsx`)

- React-based frontend built with **TypeScript** and **shadcn/ui**
- Provides an interactive interface for uploading CSV datasets and querying them using natural language
- Supports dataset submission, schema-aware querying, and response visualization

## Backend (`server/src`)

- Backend service implemented with **Express.js** and **TypeScript**
- Integrated with **PostgreSQL** for structured storage and querying of uploaded tabular data
- Orchestrates a multi-stage AI query pipeline consisting of:
  1. **Query triage and intent classification**
  2. **Schema retrieval and table profiling**
  3. **Natural language to SQL generation**
  4. **Execution-aware result formatting**
  5. **Answer validation and consistency checks**

- The backend functions as a lightweight **RAG pipeline for structured data**
- Instead of retrieving unstructured documents, it retrieves relevant **schema metadata**, **table summaries**, and **column-level context** before generating SQL
- In more advanced implementations, **sentence encoding / semantic embeddings** can be used to represent user queries, schema descriptions, and field annotations in vector space, enabling similarity-based retrieval of the most relevant database context

## Table Analyzer (`server/src/tableAnalyzer.ts`)

The table analyzer performs intelligent profiling and contextual enrichment of uploaded datasets:

- Samples records from uploaded tables
- Infers column characteristics such as:
  - data type
  - cardinality
  - null density
  - value distribution
- Produces descriptive statistics, including **min/max ranges** for numeric and temporal fields
- Generates AI-assisted semantic descriptions for columns and fields
- Supplies contextual metadata to improve retrieval quality and SQL generation accuracy

From a data science perspective, this component acts as a **semantic interpretation layer** over raw tabular data, enriching schema information with interpretable signals that improve downstream reasoning.

## Setup

### 1. Install dependencies

**Frontend**
```bash
cd ui
npm install
```

**Backend**
```bash
cd server
npm install
```

### 2. Configure PostgreSQL and environment variables

Copy `.env.sample` to `.env` and update the values:

```env
DB_USER=postgres
DB_HOST=localhost
DB_NAME=sqlgen
DB_PASSWORD=admin
DB_PORT=5432
PORT=3000
OPENAI_API_KEY=your_openai_api_key
```

### 3. Start the development servers

**Frontend**
```bash
cd ui
npm run dev
```

**Backend**
```bash
cd server
npm run dev
```

## How It Works

### 1. CSV Upload and Table Materialization

- Upload a CSV file through drag-and-drop or file selection
- The backend automatically infers column types and creates a corresponding **PostgreSQL table**
- The resulting schema is profiled and stored as structured context for future query-time retrieval

### 2. Query Understanding and Retrieval-Augmented SQL Generation

- A user submits a natural-language analytical request
- The system first classifies the query as **general**, **data-grounded**, or **out-of-scope**
- For data-grounded questions, the pipeline retrieves the most relevant schema artifacts, including:
  - table names
  - column descriptions
  - data types
  - sampled content summaries
  - profiling statistics

- In more advanced implementations, these artifacts can be indexed using **sentence encoders** so retrieval is based on semantic similarity rather than keyword overlap alone
- Retrieved context is appended to the prompt, allowing the LLM to generate SQL grounded in the actual database structure
- Query results are then transformed into a natural-language answer and passed through validation checks to reduce hallucinations and logical drift

### 3. Error Recovery and Regeneration

- If SQL execution fails, the system enters an iterative retry loop
- Previous execution errors are fed back into the model as corrective context
- The model regenerates a refined SQL query using both the original question and the observed database error
- This produces a more fault-tolerant natural-language querying experience

## Example Usage

### Upload a CSV file

```text
Drag and drop your CSV file into the upload area
Enter a table name for your dataset
Click "Upload CSV"
```

### Query the dataset

```text
Show me total sales by region for last month
What was the average order value per customer?
Which products had the highest growth rate?
```

## Technical Details

This application uses a schema-aware, retrieval-augmented prompt engineering strategy for structured data querying:

- **Intent classification** to route requests appropriately
- **Schema retrieval** to identify relevant tables, columns, and metadata
- **Sentence encoding / semantic matching** to improve relevance between user intent and schema context
- **PostgreSQL-aware SQL generation** aligned with relational best practices
- **Execution-grounded validation** to improve answer fidelity
- **Context-aware error recovery** for failed or incomplete SQL generations

In essence, the system demonstrates how **RAG models operate in structured-data environments**: rather than retrieving only free-text documents, the retrieval layer surfaces the most relevant schema context and data semantics, while the generation layer converts that context into executable SQL and interpretable analytical responses.
