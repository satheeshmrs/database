# 🤝 Integrating Pinecone with Azure Functions for Complaint Handling

This document explains how to **store complaint details in Pinecone**
and **retrieve similar past complaints + solutions** using **Azure
Functions**. It includes schema design, function code flows, and an
architecture diagram.

------------------------------------------------------------------------

## 📂 Data Model in Pinecone

### Index

-   **Name**: `complaints`
-   **Dimension**: matches embedding model (e.g., 1536 for
    `text-embedding-ada-002`)
-   **Metric**: cosine
-   **Namespace**: `dealership-complaints`

### Vector Example

``` json
{
  "id": "complaint_TCK-12345",
  "values": [/* embedding floats */],
  "metadata": {
    "ticket_id": "TCK-12345",
    "customer_id": "CUS-999",
    "call_id": "CALL-abc",
    "dealership_id": "D-001",
    "car_model": "Audi A4",
    "category": "AC",
    "sentiment": -0.72,
    "complaint_text": "AC not cooling after service...",
    "solution_text": "Inspect compressor, check refrigerant leak...",
    "status": "Closed",
    "timestamp_unix": 1726252800
  }
}
```

------------------------------------------------------------------------

## 🔄 Flow of Operations

``` mermaid
flowchart TD
    A[Customer Complaint Transcript] --> B[Azure Function: Ingest]
    B --> C[Azure OpenAI: Generate Embedding]
    C --> D[Pinecone Index: Upsert Vector + Metadata]

    E[New Complaint Query] --> F[Azure Function: Retrieve]
    F --> G[Azure OpenAI: Embed Query]
    G --> H[Pinecone Query: Similar Complaints]
    H --> I[Top Matches with Complaint + Solution]
    I --> J[Agent/CRM: Ticket + Suggested Fix]
```

------------------------------------------------------------------------

## ⚙️ Azure Function -- Ingest (Store in Pinecone)

-   Input: Complaint transcript + metadata (ticket ID, dealership, car
    model, etc.)
-   Process:
    1.  Generate embedding via Azure OpenAI.
    2.  Upsert into Pinecone with `complaint_text` and `solution_text`
        stored in metadata.
-   Benefit: Case and solution are stored together for future semantic
    retrieval.

Example payload:

``` json
{
  "ticket_id": "TCK-12345",
  "complaint_text": "AC not cooling after last service.",
  "solution_text": "Check compressor clutch & refrigerant levels.",
  "dealership_id": "D-001",
  "car_model": "Audi A4",
  "category": "AC",
  "sentiment": -0.83,
  "status": "Closed",
  "call_id": "CALL-abc",
  "customer_id": "CUS-999",
  "timestamp_unix": 1726860000
}
```

------------------------------------------------------------------------

## ⚙️ Azure Function -- Retrieve (Search in Pinecone)

-   Input: New complaint text (query).
-   Process:
    1.  Create embedding with the same model.
    2.  Query Pinecone with `top_k` nearest neighbors.
    3.  Apply filters (e.g., dealership, car model, last 180 days).
    4.  Return the **most relevant and most recent** complaints with
        their solutions.

Example query payload:

``` json
{
  "query_text": "AC not cooling after service; hissing noise",
  "dealership_id": "D-001",
  "car_model": "Audi A4",
  "lookback_days": 180,
  "top_k": 3
}
```

Example response:

``` json
{
  "ok": true,
  "results": [
    {
      "score": 0.86,
      "ticket_id": "TCK-12345",
      "car_model": "Audi A4",
      "category": "AC",
      "status": "Closed",
      "complaint_text": "AC not cooling after last service visit. Hissing noise.",
      "solution_text": "Check compressor clutch & recharge refrigerant."
    }
  ]
}
```

------------------------------------------------------------------------

## ✅ Key Benefits

-   **Automated complaint handling**: Similar issues are instantly
    identified.\
-   **Faster resolutions**: Solutions from past tickets are reused.\
-   **Hybrid filtering**: Restrict by dealership, model, or recent time
    windows.\
-   **Continuous learning**: Each resolved ticket enriches the Pinecone
    index.

------------------------------------------------------------------------

## 🏆 Summary

1.  **Ingest Pipeline**: Complaint → Embed → Store in Pinecone with
    metadata.\
2.  **Retrieve Pipeline**: Query → Embed → Search Pinecone → Return
    similar complaint + solution.\
3.  **Integration**: Azure Functions serve as the glue between call
    recordings, AI processing, Pinecone, and CRM ticketing.
