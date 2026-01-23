# MongoDB Atlas Vector Search Index Setup

This guide walks you through creating vector search indexes in MongoDB Atlas for cosine similarity search.

## Prerequisites

- MongoDB Atlas account with an M0 (free tier) or higher cluster
- Data ingested into MongoDB (run `ingestion.py` from any RAG step first)
- Project Search Index Editor role or higher

## Step-by-Step: Create a Vector Search Index

### 1. Open Atlas Search

1. Log in to [MongoDB Atlas](https://cloud.mongodb.com)
2. Select your project and cluster
3. Click the **Search & Vector Search** tab in the left sidebar

### 2. Create the Index

1. Click **Create Search Index**
2. Select **Vector Search**
3. Click **JSON Editor** under Configuration Method
4. Click **Next**

### 3. Configure the Index

1. **Index Name**: Enter the index name (see configurations below)
2. **Database**: Select `rag_playbook`
3. **Collection**: Select the appropriate collection
4. Replace the default JSON with the configuration for your RAG step

### 4. Review and Create

1. Click **Next** to review
2. Click **Create Search Index**
3. Wait for status to change from "Building" to **Active** (typically 1-2 minutes)

---

## Index Configurations

### Naive RAG (`01-naive-rag`)

| Setting | Value |
|---------|-------|
| Index Name | `naive` |
| Database | `rag_playbook` |
| Collection | `naive_rag` |

```json
{
  "fields": [
    {
      "type": "vector",
      "path": "embedding",
      "numDimensions": 1536,
      "similarity": "cosine"
    }
  ]
}
```

### Metadata-Filtered RAG (`02-metadata-filtered`)

| Setting | Value |
|---------|-------|
| Index Name | `metadata_filtered_index` |
| Database | `rag_playbook` |
| Collection | `metadata_filtered_rag` |

```json
{
  "fields": [
    {
      "type": "vector",
      "path": "embedding",
      "numDimensions": 1536,
      "similarity": "cosine"
    },
    {
      "type": "filter",
      "path": "year"
    },
    {
      "type": "filter",
      "path": "decade"
    },
    {
      "type": "filter",
      "path": "source_file"
    },
    {
      "type": "filter",
      "path": "has_financials"
    },
    {
      "type": "filter",
      "path": "topic_buckets"
    },
    {
      "type": "filter",
      "path": "companies_mentioned"
    }
  ]
}
```

---

## Verify the Index

1. Confirm the index status shows **Active** in Atlas Search
2. Test with a retrieval query:
   ```bash
   python 01-naive-rag/retrieval.py
   ```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Index stuck on "Building" | Wait up to 5 minutes. For large collections, indexing takes longer. |
| "Index not found" error | Verify the index name in your code matches exactly what you created in Atlas. |
| No results returned | Check that `numDimensions` (1536) matches your embedding model (`text-embedding-3-small`). |
| Collection not found | Run `ingestion.py` first to create the collection and ingest documents. |

---

## Reference

- Embedding model: `text-embedding-3-small` (1536 dimensions)
- Similarity metric: `cosine`
- [MongoDB Atlas Vector Search Docs](https://www.mongodb.com/docs/atlas/atlas-vector-search/)

