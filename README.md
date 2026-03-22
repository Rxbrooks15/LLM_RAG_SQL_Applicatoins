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

The application consists of two main components:

### Frontend (`ui/src/App.tsx`)
- React-based UI utilizing TypeScript and shadcn/ui components
- Enables users to upload CSV files and query the data using natural language

### Backend (`server/src`)
- Express.js server with TypeScript
- PostgreSQL database integration
- Multi-step AI query processing pipeline:
  1. Query triage and classification
  2. Schema analysis and table profiling
  3. SQL generation
  4. Result formatting
  5. Answer validation

### Table Analyzer (`server/src/tableAnalyzer.ts`)
The table analyzer component performs intelligent data profiling:
- Samples data from uploaded tables
- Analyzes column types, distinct values, and null ratios
- Generates statistical summaries (min/max for numeric/dates)
- Creates AI-powered descriptions of each field
- Provides context for more accurate query generation

## Setup

1. Install dependencies:

# Frontend
```
cd ui
npm install
```

# Backend
1. Install dependencies:
```
cd server
npm install
```

2. Set up your PostgreSQL database and configure environment variables:
   Copy the `.env.sample` file to `.env` and update the values:

```env
DB_USER=postgres
DB_HOST=localhost
DB_NAME=sqlgen
DB_PASSWORD=admin
DB_PORT=5432
PORT=3000
OPENAI_API_KEY=your_openai_api_key
```

3. Start the development servers:

# Frontend
```
cd ui
npm run dev
```

# Backend
```
cd server
npm run dev
```

## How It Works

1. **CSV Upload**
   - Upload a CSV file through drag-and-drop or file selection
   - The server automatically detects column types and creates a PostgreSQL table
   - Table schema is analyzed and stored for future queries

2. **Query Processing**
   - User enters a natural language question
   - Query is classified as general, data-specific, or out-of-scope
   - For data queries:
     - Available schema is analyzed for relevance
     - SQL query is generated using AI
     - Results are formatted into natural language
     - Response is validated for accuracy

3. **Error Handling**
   - Multiple retry attempts for failed queries
   - Context-aware error messages
   - Query regeneration with previous error context

## Example Usage

1. Upload a CSV file:
   ```
   Drag and drop your CSV file into the upload area
   Enter a table name for your data
   Click "Upload CSV"
   ```

2. Query your data:
   ```
   "Show me the total sales by region for last month"
   "What was the average order value per customer?"
   "Which products had the highest growth rate?"
   ```

## Technical Details

The application uses a sophisticated prompt engineering approach to generate accurate SQL queries:

- Query classification to determine appropriate response type
- Schema analysis to identify relevant tables and relationships
- PostgreSQL-specific query generation with best practices
- Multi-step validation to ensure accurate responses
- Error recovery with context-aware query regeneration

## Contributing

This is a proof of concept and is not intended for production use. This repository is for educational purposes and will not be maintained. Please feel free to fork and maintain your own version!

## License

MIT License - feel free to use this code for your own projects!
