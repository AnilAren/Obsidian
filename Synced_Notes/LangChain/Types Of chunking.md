# 🧩 Chunking Strategies in RAG

> “Chunking” = how you split raw documents into retrievable, semantically meaningful units before embedding.

Different chunking strategies affect **retrieval accuracy**, **embedding quality**, and **LLM context coherence**.

---

## 1️⃣ Page-based Chunking

**How:**  
Each PDF page → one chunk.

**Use when:**
- Document has **logical page boundaries** (manuals, reports, research papers).
- You want a **quick baseline**.
- You’ll use **metadata (page numbers)** for grounding in answers.

**Pros:**
- Very fast (no NLP needed).
- Keeps page-level context intact (figures, tables, captions).
- Good starting point for retrieval experiments.

**Cons:**

- Page length varies (some too small/too large).
- Headers/footers repeat (noisy).
- May cut off sentences across pages.

**Good for:** Reports, policy docs, research PDFs, textbooks with well-structured pages.

---

## 2️⃣ Fixed-size Chunking (Character-based)

**How:**  
Split text every _N characters_ (e.g., 1000 chars per chunk) with optional overlap (e.g., 200 chars).

**Use when:**
- You need a **simple, language-agnostic** chunking method (no NLP overhead).
- Content is dense and well-formatted (like JSON, logs, or code).

**Pros:**
- Easy to implement.
- Consistent chunk size → uniform embedding quality.
- Works for any file type (even raw text or code).

**Cons:**
- May split in middle of sentences.
- Overlap is mandatory for continuity.

**Good for:** Structured data dumps, code bases, logs, web crawl text.

---

## 3️⃣ Fixed-token Sliding Window

**How:**  
Split every _N tokens_ with an _M-token overlap_.  
Example: `chunk_size=300`, `overlap=50`.

**Use when:**
- You want consistent **semantic scope per embedding**.
- You’ll use **LLM embeddings** (tokens are model’s real units).
- Your downstream task is **QA-style** retrieval (context continuity matters).

**Pros:**
- Prevents context loss between chunks.
- Works great for dense text and code.
- Reproducible, adjustable by token limits.

**Cons:**
- Slightly more compute and storage.
- Redundant overlap embeddings (duplication).

**Good for:** Tutorials, developer docs, FAQs, research text.  
**👉 This is the default strategy for RAG pipelines in production.**

---

## 4️⃣ Sentence-based Chunking

**How:**  
Split by sentence boundaries (using NLP tokenizer like spaCy or NLTK).

**Use when:**
- Document has **short, self-contained sentences**.
- You want **fine-grained retrievability** (fact-level retrieval).
- You plan to combine chunks dynamically before answering.

**Pros:**
- Clean, linguistically meaningful splits.
- Easy to recombine at query time.

**Cons:**
- Many very short chunks → too many embeddings.
- Lacks context (sentences may depend on previous ones).

**Good for:** FAQs, summaries, structured policies, chat transcripts.

---

## 5️⃣ Paragraph-based Chunking

**How:**  
Split on double newlines or paragraph markers (`\n\n`).

**Use when:**
- Document is narrative or explanatory.
- Each paragraph represents a complete idea.

**Pros:**
- Natural semantic units.
- Easier for reader LLM to reconstruct meaning.

**Cons:**
- Paragraph length varies (may be short or long).
- Need to merge tiny ones.

**Good for:** Textbooks, blog posts, help docs, articles.  
**👉 Often the best “semantic baseline” for non-technical docs.**

---

## 6️⃣ Heading-based / Semantic Section Chunking

**How:**  
Split using document structure — headers (`H1`, `H2`), bolded titles, numbered sections, etc.

**Use when:**
- Document has **clear sections** (`Introduction`, `Method`, `Results`, etc.).
- You want each chunk to represent a full topic.

**Pros:**
- Highly meaningful, coherent chunks.
- Works well for retrieval and grounding.

**Cons:**
- Requires parsing structure (HTML, Markdown, or PDF TOC).
- Chunk lengths vary greatly.

**Good for:** Technical manuals, API docs, structured reports, whitepapers.

---

## 7️⃣ Semantic (Embedding-based) Chunking

**How:**  
Split text dynamically by **semantic similarity** between adjacent sentences — group together sentences that are semantically cohesive.

**Use when:**
- You need optimal **semantic boundaries** instead of size-based ones.
- You have high-quality embeddings and compute to spare.
    
**Pros:**
- Very contextually coherent.
- Handles uneven text gracefully.

**Cons:**
- Complex to compute.
- Harder to reproduce.
- Needs pre-embedding step to decide boundaries.

**Good for:** Research papers, multi-topic pages, academic text, Wikipedia-like content.

---

## 8️⃣ Hybrid / Adaptive Chunking

**How:**  
Combine strategies:
- Start with paragraph or heading chunks.
- If too long → re-chunk using token-based sliding windows.
- If too short → merge adjacent chunks.

**Use when:**
- You have documents with **mixed structure**.
- You need robust fallback for arbitrary text quality.

**Pros:**
- Best of both worlds — consistency + semantics.
- Good for large, messy document sets (enterprise use).

**Cons:**
- More code to maintain.
- Requires careful thresholds.

**Good for:** Enterprise RAG pipelines, multi-format ingestion pipelines (PDF, HTML, TXT, CSV).

---

## 9️⃣ Table-Aware Chunking (special case)

**How:**  
Extract tables separately, flatten as structured text (CSV-like).

**Use when:**
- Document includes data tables (reports, benchmarks).
- You plan numeric reasoning or retrieval on table content.

**Pros:**
- Keeps tabular data intact.
- Allows specialized retrievers later (structured queries).

**Cons:**
- Harder to embed properly.
- Needs parsing logic (camelot, pdfplumber, etc.).

**Good for:** Financial reports, research papers, analytics documents.

---

## 🔚 Summary Table — When to Use What

|Strategy|Ideal Documents|Key Benefit|Key Risk|
|---|---|---|---|
|Page-based|Reports, PDFs|Fast baseline|Inconsistent chunk sizes|
|Fixed-size (chars)|Logs, code|Simple, language-agnostic|Breaks sentences|
|Sliding-window|Tutorials, mixed text|Context continuity|Duplication overhead|
|Sentence-based|FAQs, summaries|Granular retrieval|Too short context|
|Paragraph-based|Articles, textbooks|Natural boundaries|Inconsistent lengths|
|Heading-based|Structured docs|Semantic coherence|Requires parsing|
|Semantic|Research, multi-topic|Context-aware chunks|Heavy computation|
|Hybrid|Enterprise pipelines|Balanced & robust|Complex to tune|
|Table-aware|Reports w/ tables|Preserves structure|Parsing complexity|

---

## ⚡ My Rule of Thumb for Real Projects

|Doc Type|Recommended Chunking|
|---|---|
|Code-heavy tutorials|Sliding window|
|Policy / Research reports|Page or heading-based|
|FAQs / Guides|Paragraph or sentence|
|Manuals / API docs|Heading-based or hybrid|
|Mixed PDFs (enterprise data)|Hybrid adaptive|
|Financial / tabular PDFs|Table-aware + page|

---

Would you like me to now help you **choose the right combination of 2–3 chunking methods** to experiment with on your 3 PDFs (so you can compare retrieval quality later)?  
We’ll pick the _most meaningful ones_ for your dataset.