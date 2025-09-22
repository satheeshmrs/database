For your case:

Input type:

Call recordings → converted to text transcripts (via Azure Speech-to-Text).

Knowledge resources → stored as PDF documents (service manuals, FAQ, warranty docs).

👉 Since both are text, you don’t need multiple embedding models (like separate ones for images, video, etc.).
A single text embedding model (e.g., text-embedding-ada-002 or llama-text-embed-v2) is enough.

📂 What About Knowledge Resources in PDF?

PDFs → can be ingested, parsed into chunks (e.g., per paragraph or section).

Each chunk → converted into embedding vectors using the same model.

Stored in your vector DB (Pinecone / Azure Cognitive Search).

During retrieval:

Query embedding (from complaint text) is compared to both past complaints and knowledge base chunks (PDFs).

This lets the system suggest solutions from history and from manuals.

🏆 Recommendation for Your Case

✅ Use just one embedding model for everything:

Customer complaint transcripts.

Extracted PDF knowledge base.

This gives you:

Consistency: Same embedding space → vectors are comparable.

Simplicity: One pipeline, easier maintenance.

Scalability: Works whether you add more transcripts, FAQs, or PDFs later.

⚖️ Summary

One text embedding model is enough (you don’t need multiple).

Use it for both complaint transcripts and PDF knowledge base chunks.

Store them together in your vector DB → enabling hybrid retrieval:

“Find similar complaints”

“Find related solutions/manual entries”
