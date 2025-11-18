
# How do you handle sensitive data/pdf?

- tokenization - You **replace** the sensitive value with a **token**, and you keep a secure mapping.

```
987654321 → TOKEN_12345
```

### 🔹 What it means:

- The system can **recover** the real value later.
- LLM sees only the token.
- Backend can convert token → real value anytime.
- Best for financial data.


- redact - You **remove** the sensitive data and replace it with a generic placeholder.
```
Account 987654321 → Account [ACCOUNT_ID]
```

### What it means:
- LLM cannot get the real value back.
- There is **no way to recover** the original number from the redacted text.
- Good for privacy, but you **lose the specific identifier** in the LLM workflow.

# How do we store private or financial data and how does LLM work behind the scenes?


# **1. Fetch the Raw Data**

- Retrieve the documents, messages, or user queries that contain sensitive values.
- This raw data never goes directly to the LLM or the embeddings pipeline.

# **2. Tokenize Sensitive Values**

Sensitive values are replaced with secure tokens.

`987654321 → TOKEN_123`

### **User Question**

```
Show me the total amount for account 987654321
```

### **After Tokenization**

```
Show me the total amount for account TOKEN_12345
```



# **3. Create Embeddings Using Only Tokenized Content**

- The **tokenized content** (not raw values) is passed to the embeddings model.
- The embeddings are then stored in the **vector database**.
- Embeddings **never contain** real financial identifiers.

### **Why this matters**

- Embeddings can be used safely for similarity search / RAG without leaking raw values.

---

# **4. Use Enterprise-Level Security**

- The LLM operates inside an enterprise sandboxed environment.
- No raw data leaves your infrastructure.
- Access control + encryption + isolation ensure compliance (SOC2, ISO, FINRA/PCI if needed).
- Only tokenized content is routed through the AI layer.
### **Tokenization details**

- A secure **token ↔ real_value** mapping is stored in a database.
- Tokens are deterministic or consistent so that the same account always maps to the same token.
- New accounts get new tokens automatically.

# **5. LLM Generates Logic (Not Data)**

### **LLM does NOT fetch data**

- It never runs SQL.
- It never connects to your database.
- It only generates **instructions** for what the backend should do.

---

# **4.1 Fetch the Relevant Chunks (RAG Step)**

user sends query and we tokenize it

The system performs retrieval based on the **tokenized query**:

1. Use tokenized query
2. Perform vector search on the **vector database**
3. Retrieve **textual chunks / context**
4. These chunks help the LLM understand business rules, domain logic, definitions, etc.
5. **These chunks do NOT include table schema**
    - (Important: retrievers return _content_, not schema.)

**RAG will never provide table structure.**  
So schema must come from a different step.


## **4.2 Backend Adds the Table Schema (Manually / Programmatically)**

Since the retriever does **not** return schema, the backend must explicitly provide it.

Here’s how:

---

### **1. Backend identifies relevant tables**

Based on: keyword or column name matching

- Query intent
- Predefined rules
- Metadata
- Table-selection logic

Example:  
Query mentions “amount” → use **transactions** table.  
Query mentions “balance” → use **accounts** table.

**LLM does NOT make this decision.**

---

### **2. Backend extracts the schema snippet**

Backend prepares only the required schema fields:

`Table: transactions - account_id (INT) - amount (DECIMAL) - timestamp (DATETIME)`

or in JSON:

`{   "table_name": "transactions",   "columns": [     { "name": "account_id", "type": "INT" },     { "name": "amount", "type": "DECIMAL" },     { "name": "timestamp", "type": "DATETIME" }   ] }`

---

### **3. Backend injects schema into the final LLM prompt**

LLM receives a single combined prompt:

- Tokenized user query
- Retrieved RAG chunks
- Relevant table schemas
- System instructions

Example:

```
You MUST generate logic using only the schema provided below.  
Table: transactions - account_id (INT) - amount (DECIMAL) - timestamp (DATETIME)  User Query (tokenized): "Show me the total amount for account TOKEN_12345"  Context: <retrieved RAG chunks here>
```




## **5.2 What the LLM Outputs**

The LLM returns logic such as:

- Which table to query
- What filters to apply
- What aggregate function to use
- What fields to return
- How to format the final answer

### **Example logic**

`Use account_id = TOKEN_12345 Perform SUM of the 'amount' column Return the result to user`

---

## **5.3 Backend Executes the Real Query**

`SELECT SUM(amount) FROM transactions WHERE account_id = 987654321;`

### **LLM Output ≠ Data**

LLM output = **instructions + explanation only**, never the real numbers.


Steps:

1. fetch the data 
2. Tokenize account → TOKEN_123
3. create embeedings for the tokenzed content and store it in relevant vector database
4. select enterprise level for security 
5. LLM generates the **logic**, not the data
	1. LLM **does NOT fetch data**.  
	It only creates **instructions** for your backend.
	### Example user question
	“Show me the total amount for account 987654321”
	### After tokenization
	“Show me the total amount for account TOKEN_12345”
	### LLM generates **logic**, such as:
	- Which table to query
	- What filter to apply
	- What aggregate to use
	- What fields to return
	- How to format the answer
	
	### **Example logic (very simple)**
	
	```
	Use account_id = TOKEN_12345 
	Perform SUM of the 'amount' column 
	Return the result to user
	```
	
	### **Backend executes it**
	
	`SELECT SUM(amount) FROM transactions WHERE account_id = 987654321;`
	
	### LLM output ≠ data
	
	LLM output = **instructions + explanation**

	- How do we get the SQL statemnt
		- either LLM give you the SQL directly 
		- it gives you the JSON 
	-  how do we know which table we will work 
		- The LLM knows table names because we include a schema snippet in the prompt. It can only use what we provide — so SQL becomes safe and controllable.
	- we dont provide all the tabled we only provide the relevant tables,We have a table-selection layer that picks only the relevant tables based on the query intent, then sends only those table schemas to the LLM.
	- How does LLM know which table to pick?
		- The LLM does not pick tables. Our backend picks relevant tables based on query intent and provides only those table schemas in the prompt. The LLM simply uses what we give it.


6. Backend fetches real data from DB:
    `SELECT balance FROM accounts WHERE id=987654321`
		

7. De-tokenize → answer user

---

No embedding needed for the actual numbers in finance as 

- we do tokenization/redact and if its a data that needs to be looked up like financial data like  the account number are to be stored somewhere why?
		- the data is tokenized before it is given to the embeddings and we store this token - value mapping in Db 
		- when user asks for any question wrt any account, we tokenized the account number again from the db , asper the mapping we have done before 
			- if data is fund we use same toekn or create a new one ( this cound be newly added account )
- 