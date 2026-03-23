## Vector DB Use Case
No, a traditional keyword-based database search would **not** suffice for this system. 

Keyword search relies on exact string matches. e.g. If a lawyer searches "What are the termination clauses?", a traditional database looks for the specific words "termination" and "clauses." However, legal documents often use varied phrasing, such as "this agreement shall conclude," "cancellation terms," or "severance of obligations." A keyword search would miss these highly relevant sections entirely because the exact vocabulary doesn't overlap. It cannot understand synonyms, context, or the user's actual intent. A vector database solves this problem by enabling **semantic search**—searching by meaning rather than exact words. Here is the role it plays in a legal AI system:

1. **Storing Meaning:** The 500-page contracts are broken down into smaller chunks (like paragraphs) and processed through an AI embedding model. This model converts the text into numerical vectors (arrays of numbers) that capture the underlying context and meaning of the text. These vectors are stored in the vector database.
2. **Processing the Query:** When a lawyer asks a plain English question, that query is instantly converted into its own mathematical vector.
3. **Similarity Matching:** The vector database calculates the distance (typically using cosine similarity) between the query vector and the document vectors. 

By finding the vectors mathematically closest to the query, the database retrieves the exact paragraphs discussing "cancellation terms" when asked about "termination clauses," even if they share zero identical words. 

