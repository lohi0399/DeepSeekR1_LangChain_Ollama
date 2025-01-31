### **1️⃣ Initialize the LLM Engine**  
```python
llm_engine = ChatOllama(
    model=selected_model,
    base_url="http://localhost:11434",
    temperature=0.3
)
```
✅ **What Happens?**  
- Loads the selected **DeepSeek model** via **Ollama**.  
- Connects to the **local Ollama server** running at `localhost:11434`.  
- Sets the **temperature to 0.3** for balanced response randomness.  

---

### **2️⃣ Define System Prompt for AI Behavior**
```python
system_prompt = SystemMessagePromptTemplate.from_template(
    "You are an expert AI coding assistant. Provide concise, correct solutions "
    "with strategic print statements for debugging. Always respond in English."
)
```
✅ **What Happens?**  
- The AI is instructed to **act as a coding assistant**.  
- Ensures **responses are accurate, concise, and in English**.  

---

### **3️⃣ Initialize Chat History in Session State**
```python
if "message_log" not in st.session_state:
    st.session_state.message_log = [
        {"role": "ai", "content": "Hi! I'm DeepSeek. How can I help you code today? 💻"}
    ]
```
✅ **What Happens?**  
- If chat history (`message_log`) **does not exist**, it is initialized.  
- The AI **greets the user** at the start of the session.  

---

### **4️⃣ Display Chat History in the Chat Container**
```python
chat_container = st.container()

with chat_container:
    for message in st.session_state.message_log:
        with st.chat_message(message["role"]):
            st.markdown(message["content"])
```
✅ **What Happens?**  
- **Creates a chat container**.  
- Iterates over **previous chat messages** stored in session state.  
- **Displays each message** (AI & user) using `st.chat_message()`.  

---

### **5️⃣ Wait for User Input**
```python
user_query = st.chat_input("Type your coding question here...")
```
✅ **What Happens?**  
- **Displays a text input box** at the bottom.  
- **Waits for the user** to enter a question.  
- Stores the user input in **`user_query`**.  

---

### **6️⃣ Build Prompt Chain (Maintains Chat Context)**
```python
def build_prompt_chain():
    prompt_sequence = [system_prompt]
    for msg in st.session_state.message_log:
        if msg["role"] == "user":
            prompt_sequence.append(HumanMessagePromptTemplate.from_template(msg["content"]))
        elif msg["role"] == "ai":
            prompt_sequence.append(AIMessagePromptTemplate.from_template(msg["content"]))
    return ChatPromptTemplate.from_messages(prompt_sequence)
```
✅ **What Happens?**  
- **Starts with the system prompt** (AI behavior instructions).  
- **Appends past user messages** (from chat history).  
- **Appends past AI responses** to maintain context.  
- Returns a **structured chat prompt chain**.  

---

### **7️⃣ Process User Input (If Any)**
```python
if user_query:
    # Add user message to chat history
    st.session_state.message_log.append({"role": "user", "content": user_query})
```
✅ **What Happens?**  
- If the **user submits a message**, it is **added to chat history**.  
- This ensures the AI **remembers past interactions**.  

---

### **8️⃣ Generate AI Response**
```python
def generate_ai_response(prompt_chain):
    processing_pipeline = prompt_chain | llm_engine | StrOutputParser()
    return processing_pipeline.invoke({})
```
✅ **What Happens?**  
- **Passes the structured prompt chain to the AI model**.  
- **Processes the response through Ollama**.  
- **Parses the output into plain text** (removing JSON/metadata).  

---

### **9️⃣ Show AI Processing Indicator**
```python
with st.spinner("🧠 Processing..."):
    prompt_chain = build_prompt_chain()
    ai_response = generate_ai_response(prompt_chain)
```
✅ **What Happens?**  
- Displays a **loading spinner** while the AI generates a response.  
- Calls **`build_prompt_chain()`** to structure the prompt.  
- Calls **`generate_ai_response(prompt_chain)`** to get AI output.  

---

### **🔟 Store AI Response in Chat History**
```python
st.session_state.message_log.append({"role": "ai", "content": ai_response})
```
✅ **What Happens?**  
- The **AI response is appended to chat history**.  
- Ensures **conversation flow is maintained** across multiple exchanges.  

---

### **1️⃣1️⃣ Rerun the App to Refresh Chat Display**
```python
st.rerun()
```
✅ **What Happens?**  
- **Forces Streamlit to refresh**.  
- Ensures **new messages (user + AI) appear instantly**.  

---

## **📌 Full Sequence of Execution**
1️⃣ **Initialize the LLM engine (`ChatOllama`)**  
2️⃣ **Define the system prompt** (AI behavior instructions)  
3️⃣ **Initialize chat history (`st.session_state.message_log`)**  
4️⃣ **Display chat history in chat container**  
5️⃣ **Wait for user input (`st.chat_input()`)**  
6️⃣ **Build the structured prompt chain (contextual history)**  
7️⃣ **Append the user’s message to chat history**  
8️⃣ **Pass the conversation to the AI (`generate_ai_response()`)**  
9️⃣ **Show "Processing..." spinner while the AI thinks**  
🔟 **Append the AI response to chat history**  
1️⃣1️⃣ **Rerun Streamlit (`st.rerun()`) to update UI**  
