# AI SDLC Review Assistant: In-Depth Technical Learning Manual
> **A Complete Masterclass for Freshers & Developers**  
> *Har topic ka complete theoretical explanation, project ke exact code snippets, aur line-by-line breakdown.*

---

## Table of Contents
1. [Topic 1: Python Core & Modern Advanced Features](#topic-1-python-core--modern-advanced-features)
2. [Topic 2: FastAPI & Web Backend Engineering](#topic-2-fastapi--web-backend-engineering)
3. [Topic 3: Streamlit & Reactive Frontend Engineering](#topic-3-streamlit--reactive-frontend-engineering)
4. [Topic 4: Generative AI, LLMs & Prompt Engineering](#topic-4-generative-ai-llms--prompt-engineering)
5. [Topic 5: Multi-Agent Architecture & Intent Routing](#topic-5-multi-agent-architecture--intent-routing)
6. [Topic 6: Document Processing, PyPDF2 & Dynamic ReportLab PDF](#topic-6-document-processing-pypdf2--dynamic-reportlab-pdf)
7. [Topic 7: Software Engineering & SDLC Fundamentals](#topic-7-software-engineering--sdlc-fundamentals)
8. [Topic 8: Code Review, Security Auditing & OWASP Top 10](#topic-8-code-review-security-auditing--owasp-top-10)
9. [Topic 9: Environment Configuration, DevOps & Security Hygiene](#topic-9-environment-configuration-devops--security-hygiene)

---

## Topic 1: Python Core & Modern Advanced Features

### 1. Concept Ki Deep Samajh
Python ek dynamic language hai, lekin enterprise software me bina rules ke code likhne par bugs aate hain. Is project me modern Python (Python 3.10+) ke high-level features use kiye gaye hain:
* **Type Annotations:** Function ke inputs aur outputs ke data types declare karna.
* **Asynchronous Execution (`async`/`await`):** Network calls ke dauran computer ke CPU ko freeze hone se bachana.
* **Pathlib:** Operating System independent file path management.
* **Regular Expressions (`re`):** Text ke andar se complex patterns (jaise JSON data) dhoondh kar nikalna.

---

### 2. Project Ka Exact Code Snippet

#### A. Type Hinting & Asynchronous File Upload ([app/main.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/app/main.py#L99-L111))
```python
@app.post("/review-file")
async def review_file(
    file: UploadFile = File(...)
):
    file_location = f"uploads/{file.filename}"

    with open(file_location, "wb") as f:
        content = await file.read()
        f.write(content)

    extracted_text = FileService.extract_text(file_location)
    agent = detect_agent(extracted_text)
    return execute_review(agent, extracted_text)
```

#### B. Regex JSON Extraction ([app/services/json_parser.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/app/services/json_parser.py#L5-L25))
```python
def parse_json_response(text: str):
    try:
        return json.loads(text)
    except Exception:
        match = re.search(
            r"\{.*\}",
            text,
            re.DOTALL
        )
        if match:
            try:
                return json.loads(match.group())
            except Exception:
                pass

    return {
        "raw_response": text
    }
```

---

### 3. Line-by-Line Code Breakdown
1. `async def review_file(...)`: `async` keyword Python ko batata hai ki yeh function ek **coroutine** hai. Jab yeh kisi I/O operation (jaise file read ya internet call) ka wait karega, toh yeh CPU thread ko release kar dega taaki doosre users ke requests process ho sakein.
2. `content = await file.read()`: `await` ka matlab hai "jab tak file poori memory me load nahi hoti, tab tak wait karo, lekin server ko block mat karo".
3. `re.search(r"\{.*\}", text, re.DOTALL)`:
   - `\{`: Pehla curly bracket dhoondhta hai.
   - `.*`: Uske beech ka saara data match karta hai.
   - `\}`: Aakhri curly bracket tak match karta hai.
   - `re.DOTALL`: By default, regular expressions me dot (`.`) new line (`\n`) ko match nahi karta. `re.DOTALL` flag lagane se dot newlines ko bhi match karta hai, jisse multi-line JSON bina error ke extract ho jata hai.

---

### 4. Viva / Interview Questions
* **Q: Normal `def` aur `async def` me kya farak hai?**  
  *Ans:* Normal `def` synchronous hota hai—jab tak function poora execute nahi hota, thread block rehta hai. `async def` asynchronous hota hai aur Event Loop par chalta hai; I/O bound tasks me `await` aate hi thread dusre requests handle karne chala jata hai.
* **Q: Regex me `re.DOTALL` kyu use kiya?**  
  *Ans:* LLM ka JSON response multiple lines me hota hai. Bina `re.DOTALL` ke regex pehli line par hi ruk jata aur pura JSON capture nahi kar pata.

---

## Topic 2: FastAPI & Web Backend Engineering

### 1. Concept Ki Deep Samajh
* **FastAPI:** Python ka sabse modern REST API framework jo Starlette aur Pydantic par bana hai.
* **REST (Representational State Transfer):** Client (Streamlit) aur Server (FastAPI) ke beech data exchange karne ka standard rule. Client HTTP verb bhejta hai (`GET` ya `POST`), aur server JSON format me reply deta hai.
* **ASGI (Asynchronous Server Gateway Interface):** Flask aur Django puraane WSGI standard par chalte the jo synchronous tha. FastAPI ASGI standard (Uvicorn) par chalta hai, jo high-concurrency requests ko handle karta hai.

---

### 2. Project Ka Exact Code Snippet

#### Request Model & Health Endpoint ([app/main.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/app/main.py#L20-L45))
```python
app = FastAPI(
    title="AI SDLC Review Assistant",
    version="2.0.0"
)

class ReviewRequest(BaseModel):
    query: str

@app.get("/")
def health():
    return {
        "status": "running",
        "application": "AI SDLC Review Assistant",
        "version": "2.0.0"
    }

@app.post("/review")
def review_request(request: ReviewRequest):
    agent = detect_agent(request.query)
    return execute_review(agent, request.query)
```

---

### 3. Line-by-Line Code Breakdown
1. `app = FastAPI(...)`: FastAPI application ka main instance create karta hai. Yeh automatically Swagger UI (`/docs`) aur Redoc (`/redoc`) generate karta hai.
2. `class ReviewRequest(BaseModel)`: Pydantic model define karta hai. Agar user POST request me `{"query": "my prompt"}` bhejega, toh yeh ensure karega ki `query` string hi ho. Agar koi galat data bhejega, toh FastAPI automatically `422 Unprocessable Entity` return karega.
3. `@app.post("/review")`: Route decorator jo URL path `/review` ko `review_request` function se bind karta hai.

---

### 4. Viva / Interview Questions
* **Q: FastAPI Flask se behtar kyu hai?**  
  *Ans:* FastAPI natively asynchronous hai (`async/await`), isme Pydantic ke through automatic data validation hoti hai, aur yeh automatically interactive API documentation (`/docs`) generate karta hai.
* **Q: Pydantic ka project me kya role hai?**  
  *Ans:* Pydantic incoming JSON request payload ko validate karta hai aur data types enforce karta hai, jisse runtime pe type-errors nahi aate.

---

## Topic 3: Streamlit & Reactive Frontend Engineering

### 1. Concept Ki Deep Samajh
* **Reactive Model:** Streamlit ka architecture traditional web frameworks (React, Angular) se alag hota hai. Har bar jab koi user button click karta hai, dropdown select karta hai, ya file upload karta hai, **Streamlit pura Python script upar se niche dobara execute karta hai**.
* **State Management:** Kyunki script baar-baar execute hota hai, standard Python variables har click par reset ho jate hain. Puraane results ya search history ko bachane ke liye **Session State (`st.session_state`)** use kiya jata hai.

---

### 2. Project Ka Exact Code Snippet

#### Session State & Tab 1 Execution ([frontend/streamlit_app.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/frontend/streamlit_app.py#L26-L375))
```python
# Session State Initialization
if "history" not in st.session_state:
    st.session_state.history = []

# Tab 1: Asking Question
with tab1:
    query = st.text_area("Enter your question", height=150)

    if st.button("Review Query", use_container_width=True):
        if not query.strip():
            st.warning("Please enter a question.")
        else:
            with st.spinner("Reviewing..."):
                response = requests.post(
                    f"{API_URL}/review",
                    json={"query": query},
                    timeout=300
                )
                data = response.json()

            st.session_state.history.append({
                "type": "question",
                "query": query,
                "agent": data.get("agent"),
                "reviewType": data.get("reviewType")
            })

            display_result(data)
```

---

### 3. Line-by-Line Code Breakdown
1. `if "history" not in st.session_state:`: Check karta hai ki browser session me `history` list already exist karti hai ya nahi. Agar nahi karti, toh empty list initialize karta hai.
2. `with st.spinner("Reviewing..."):`: Jab tak FastAPI aur LLM se response nahi aata, yeh UI par ek animated loading indicator dikhata hai.
3. `requests.post(f"{API_URL}/review", json={"query": query})`: Streamlit frontend Python code se FastAPI backend ke port 8000 par HTTP POST request bhejta hai.
4. `st.session_state.history.append(...)`: Current review ko session state me add karta hai taaki user purani reviews ko track kar sake.

---

### 4. Viva / Interview Questions
* **Q: Streamlit me Session State ka kya kaam hai?**  
  *Ans:* Streamlit har user interaction par pure script ko rerun karta hai. Agar session state use na karein, toh har click par variables destroy ho jayenge. Session state data ko browser tab ke band hone tak memory me persist karta hai.

---

## Topic 4: Generative AI, LLMs & Prompt Engineering

### 1. Concept Ki Deep Samajh
* **LLM (Large Language Model):** Neural network architectures (Transformers) jo massive source code aur text data par train huye hain.
* **Role / Persona Prompting:** LLM ko ek specific role (jaise Senior Security Auditor) dene se model ka attention vector us domain ke specific patterns aur vocabulary par align ho jata hai.
* **Temperature:** Token generation ke waqt probability distribution ko flatten ya sharpen karne ka parameter.
  * $T = 0.8$: Creative writing, casual conversation.
  * $T = 0.2$: Strict, factual, deterministic evaluation (code security aur architecture ke liye mandatory).

---

### 2. Project Ka Exact Code Snippet

#### LLM Service Implementation ([app/services/llm_service.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/app/services/llm_service.py#L8-L46))
```python
class LLMService:
    def __init__(self):
        self.api_key = os.getenv("API_KEY")
        self.base_url = os.getenv("BASE_URL")
        self.model_name = os.getenv("MODEL_NAME")

    def generate(self, prompt: str):
        url = f"{self.base_url}/chat/completions"

        headers = {
            "Authorization": f"Bearer {self.api_key}",
            "Content-Type": "application/json"
        }

        payload = {
            "model": self.model_name,
            "messages": [
                {"role": "user", "content": prompt}
            ],
            "temperature": 0.2
        }

        response = requests.post(url=url, headers=headers, json=payload, timeout=120)
        response.raise_for_status()
        data = response.json()
        return data["choices"][0]["message"]["content"]
```

#### Architecture Agent Persona Template ([app/prompts/architecture_prompt.txt](file:///c:/Users/sk725/Downloads/Project-main/Project-main/app/prompts/architecture_prompt.txt))
```text
You are a Principal Enterprise Solution Architect.

Analyze the provided software requirement.

Provide:
1. Technical Requirements
2. Recommended Architecture
3. Microservice Boundaries
4. Database Recommendation
5. Risks
6. ADR Recommendations

Return structured markdown.

Requirement:
{query}
```

---

### 3. Line-by-Line Code Breakdown
1. `url = f"{self.base_url}/chat/completions"`: OpenAI standard format ka endpoint. Yeh kisi bhi OpenAI-compatible cloud gateway ya local LLM se connect ho sakta hai.
2. `"temperature": 0.2`: Hallucinations ko minimize karta hai. Agar temperature 0.9 hota, toh AI apne man se fake libraries ya galat security rules invent kar sakta tha.
3. `prompt.replace("{query}", query)`: Runtime par user dwara diye gaye requirement text ya source code ko prompt template ke placeholder `{query}` me inject karta hai.
4. `response.raise_for_status()`: Agar API key expire ho ya server down ho (HTTP 401, 500), toh yeh turant Python exception throw karta hai taaki silent failure na ho.

---

### 4. Viva / Interview Questions
* **Q: Temperature 0.2 kyu rakha gaya hai?**  
  *Ans:* Software reviews aur vulnerability detection me determinism aur accuracy sabse important hoti hai. Low temperature (0.2) randomness aur hallucinations ko rokti hai.

---

## Topic 5: Multi-Agent Architecture & Intent Routing

### 1. Concept Ki Deep Samajh
* **Monolithic Prompt Ki Problem:** Agar aap ek hi prompt me bolo: *"Code review bhi karo, database design bhi karo, React accessibility bhi check karo, aur unit test bhi likho"*, toh prompt bohot bada ho jata hai (**Context Dilution**). Model conflicting instructions ke beech confuse ho jata hai.
* **Solution (Multi-Agent Dispatching):** 5 alag-alag domain agents banaye gaye hain. Har agent ka apna specialized persona hai.
* **Intent Router:** Input text ko scan karta hai aur sahi agent ko select karta hai.

---

### 2. Project Ka Exact Code Snippet

#### Keyword-Based Intent Router ([app/router/intent_router.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/app/router/intent_router.py#L1-L50))
```python
def detect_agent(content: str) -> str:
    q = content.lower()

    if any(word in q for word in [
        "microservice", "architecture", "requirement", "adr", "database design"
    ]):
        return "architecture"

    if any(word in q for word in [
        "openapi", "swagger", "endpoint", "rest api", "/products"
    ]):
        return "backend"

    if any(word in q for word in [
        "react", "angular", "wcag", "component", "frontend"
    ]):
        return "frontend"

    if any(word in q for word in [
        "public class", "private", "security", "cyclomatic", "@service", "@repository"
    ]):
        return "code_review"

    if any(word in q for word in [
        "junit", "test case", "@test", "coverage"
    ]):
        return "testing"

    return "architecture" # Default fallback
```

---

### 3. Line-by-Line Code Breakdown
1. `q = content.lower()`: Text ko case-insensitive banata hai taaki "Microservice" aur "microservice" dono match ho sakein.
2. `any(word in q for word in [...])`: Python ka high-performance generator expression jo list ke kisi bhi ek keyword ke milte hi `True` return kar deta hai.
3. `return "architecture"` (Last line): Fallback safety net. Agar kisi query me koi standard keyword na mile, toh system crash nahi hota; yeh use architectural analysis ke liye route kar deta hai.

---

### 4. Viva / Interview Questions
* **Q: Router me AI use karne ki jagah keyword matching kyu use kiya?**  
  *Ans:* Keyword matching me **0 millisecond latency** hoti hai aur **₹0 token cost** lagti hai. Har routing ke liye alag LLM call karna slow aur mehenga hota hai.

---

## Topic 6: Document Processing, PyPDF2 & Dynamic ReportLab PDF

### 1. Concept Ki Deep Samajh
* **Text vs Binary Files:** Source code files (`.py`, `.java`, `.yaml`) plain characters hote hain jinhe seedhe text read kiya ja sakta hai. PDF files binary streams hoti hain jinhe decode karne ke liye specialized parser chahiye.
* **PyPDF2:** PDF ke binary stream ko decode karke pages ke text ko extract karta hai.
* **ReportLab Canvas:** Dynamic PDF generation engine jo memory buffer (`io.BytesIO`) par graphics aur text render karta hai.

---

### 2. Project Ka Exact Code Snippet

#### A. PDF & Text Extractor ([app/services/file_service.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/app/services/file_service.py#L8-L47))
```python
class FileService:
    @staticmethod
    def extract_text(file_path: str):
        path = Path(file_path)
        extension = path.suffix.lower()

        if extension == ".pdf":
            reader = PdfReader(file_path)
            text = ""
            for page in reader.pages:
                text += page.extract_text() or ""
            return text

        elif extension in [".txt", ".py", ".java", ".js", ".jsx", ".ts", ".tsx", ".json", ".yaml", ".yml"]:
            return path.read_text(encoding="utf-8", errors="ignore")

        return path.read_text(encoding="utf-8", errors="ignore")
```

#### B. Dynamic PDF Report Generation ([frontend/streamlit_app.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/frontend/streamlit_app.py#L55-L138))
```python
def generate_pdf(data):
    buffer = BytesIO() # RAM me virtual file buffer
    pdf = canvas.Canvas(buffer)
    pdf.setTitle("AI SDLC Review Report")

    pdf.setFont("Helvetica-Bold", 18)
    pdf.drawString(50, 800, "AI SDLC REVIEW REPORT")

    pdf.setFont("Helvetica", 12)
    pdf.drawString(50, 750, f"Review Type: {data.get('reviewType', '')}")
    pdf.drawString(50, 730, f"Agent: {data.get('agent', '')}")
    pdf.line(50, 715, 550, 715)

    response_text = str(data.get("response", ""))
    y = 690

    for line in response_text.split("\n"):
        if not line.strip():
            y -= 10
            continue
        pdf.drawString(50, y, line[:120])
        y -= 15
        if y <= 40: # Page end hone par naya page create karna
            pdf.showPage()
            y = 800

    pdf.save()
    buffer.seek(0)
    return buffer
```

---

### 3. Line-by-Line Code Breakdown
1. `reader = PdfReader(file_path)`: PDF file ko memory stream me load karta hai.
2. `page.extract_text() or ""`: Agar page par koi text na ho (jaise blank page), toh yeh `None` ki jagah empty string return karta hai taaki `TypeError` na aaye.
3. `buffer = BytesIO()`: Sabse important design pattern! PDF ko hard disk par save karne ki jagah seedhe RAM ke andar byte buffer me create kiya jata hai. Isse disk full hone ka khatra nahi rehta.
4. `pdf.drawString(50, y, line[:120])`: Coordinate system (X=50, Y=y) par text likhta hai. Y-axis niche se upar chalti hai (800 top hai, 40 bottom hai).

---

### 4. Viva / Interview Questions
* **Q: BytesIO kyu use kiya gaya?**  
  *Ans:* Hard disk par temporary files create karne se disk space bharta hai aur concurrency me file lock ke issues aate hain. `BytesIO` in-memory stream create karta hai jo RAM se seedhe user ke browser me download ho jata hai.

---

## Topic 7: Software Engineering & SDLC Fundamentals

### 1. Concept Ki Deep Samajh
Yeh is project ka **Core Business Domain** hai. Ek software engineer ko in 5 domains ki samajh honi chahiye:
* **Microservices vs Monolith:** Monolith me sab kuch ek hi code repository me hota hai. Microservices me har feature (User, Order, Payment) ek independent deployable service hoti hai jo network calls se baat karti hai.
* **Polyglot Persistence:** Har microservice ko uske kaam ke hisab se alag database dena (e.g. Order Service ke liye PostgreSQL, Product Catalog ke liye MongoDB, Caching ke liye Redis).
* **ADR (Architectural Decision Records):** Ek standard documentation format jo batata hai ki humne koi technical decision kyu liya.
* **WCAG 2.1 AA (Web Content Accessibility Guidelines):** International standard jo enforce karta hai ki website color-blind ya physically disabled logon ke liye accessible ho.

---

### 2. Project Ka Exact Code Snippet

#### Architectural Test Case Input ([architecture.txt](file:///c:/Users/sk725/Downloads/Project-main/Project-main/architecture.txt))
```text
Design a microservice architecture
for an ecommerce application.

Requirements:
- Product Service
- Order Service
- Inventory Service
- Payment Service
- Notification Service
```

#### OpenAPI 3.0 Contract Sample ([sample_openapi.yaml](file:///c:/Users/sk725/Downloads/Project-main/Project-main/sample_openapi.yaml))
```yaml
openapi: 3.0.0
info:
  title: Product API
  version: 1.0.0
paths:
  /products:
    get:
      summary: Get Products
    post:
      summary: Create Product
```

---

### 3. Line-by-Line Code Breakdown
1. `architecture.txt`: Jab yeh file upload hoti hai, Intent Router `"microservice"` word dekh kar Architecture Agent ko trigger karta hai.
2. Architecture Agent is requirement ko padh kar microservice boundaries, asynchronous event streaming (Kafka/RabbitMQ), aur database isolation suggest karta hai.
3. `sample_openapi.yaml`: Backend Agent is contract ko evaluate karta hai ki kya endpoints REST conventions follow karte hain aur proper status codes (`200 OK`, `201 Created`) define hain ya nahi.

---

## Topic 8: Code Review, Security Auditing & OWASP Top 10

### 1. Concept Ki Deep Samajh
Code Review Agent software security aur maintainability flaws ko pakadta hai:
* **CWE-798 (Use of Hard-coded Credentials):** Code ke andar password ya secret keys likhna.
* **CWE-89 (SQL Injection):** User input ko bina sanitize kiye direct SQL string me jodhna.
* **Improper Logging:** Console print statements (`System.out.println`) ka production me use hona.

---

### 2. Project Ka Exact Code Snippet

#### Flawed Java Class Used for Testing ([OrderService.java](file:///c:/Users/sk725/Downloads/Project-main/Project-main/OrderService.java))
```java
package com.demo.order;

public class OrderService {

    private String apiKey = "secret123"; // ❌ FLAW 1: Hardcoded Secret

    public void createOrder(String userId) {

        // ❌ FLAW 2: SQL Injection Vulnerability
        String query =
                "SELECT * FROM USERS WHERE ID='"
                + userId + "'";

        System.out.println(query); // ❌ FLAW 3: Improper Logging

        System.out.println(
                "Order created"
        );
    }
}
```

---

### 3. Detailed Security Vulnerability Analysis
1. **Flaw 1 (Hardcoded Secret):**
   - *Risk:* `private String apiKey = "secret123";` agar GitHub par push hua, toh bots ise seconds me scan kar lenge.
   - *Fix:* Ise `.env` ya AWS Secrets Manager me daal kar runtime par read karna chahiye.
2. **Flaw 2 (SQL Injection):**
   - *Risk:* String concatenation (`+ userId +`) allow karta hai ki hacker input me likhe: `' OR '1'='1`. Isse SQL command ban jayega: `SELECT * FROM USERS WHERE ID='' OR '1'='1'`. Query hamesha `TRUE` evaluate hogi aur database saare users ka data attacker ko bhej dega!
   - *Fix (Prepared Statement):*
     ```java
     PreparedStatement ps = connection.prepareStatement("SELECT * FROM USERS WHERE ID = ?");
     ps.setString(1, userId);
     ```
3. **Flaw 3 (Improper Logging):**
   - *Risk:* `System.out.println` synchronous I/O operation hai jo server performance ko degrade karta hai aur isme log levels (INFO, WARN, ERROR) nahi hote.
   - *Fix:* Enterprise logger use karna (SLF4J / Logback): `logger.info("Order created for user: {}", userId);`.

---

## Topic 9: Environment Configuration, DevOps & Security Hygiene

### 1. Concept Ki Deep Samajh
* **Virtual Environment (`venv`):** Python project dependencies ko isolated sandbox me rakhta hai taaki ek project ki library dusre project se conflict na kare.
* **Environment Variables (`.env`):** Enterprise security ka first rule hai ki code aur configuration ko hamesha alag rakhna chahiye (**12-Factor App methodology**).

---

### 2. Project Ka Exact Code Snippet

#### Secret Configuration ([.env](file:///c:/Users/sk725/Downloads/Project-main/Project-main/.env))
```ini
API_KEY=xdgxqA1cJQ94I36H7uCb4mrDVyCBuOu67n4JaLS4
BASE_URL=https://openai.generative.engine.capgemini.com/v1
MODEL_NAME=openai.gpt-5-mini
```

#### Loading Configuration ([app/services/llm_service.py](file:///c:/Users/sk725/Downloads/Project-main/Project-main/app/services/llm_service.py#L3-L13))
```python
from dotenv import load_dotenv

load_dotenv(".env")

class LLMService:
    def __init__(self):
        self.api_key = os.getenv("API_KEY")
        self.base_url = os.getenv("BASE_URL")
        self.model_name = os.getenv("MODEL_NAME")
```

---

### 3. Line-by-Line Code Breakdown
1. `load_dotenv(".env")`: Disk se `.env` file ko khoj kar uske key-value pairs ko operating system ke environment variables me inject kar deta hai.
2. `os.getenv("API_KEY")`: Memory se key ko retrieve karta hai. Isse source code me kahin bhi API key plaintext me nahi dikhti.

---

## 🎯 Final Summary & Revision Checklist

| Topic # | Name | Key Concept to Remember |
| :---: | :--- | :--- |
| **1** | **Python Core** | `async/await` non-blocking I/O ke liye, `re.DOTALL` multi-line JSON ke liye. |
| **2** | **FastAPI** | ASGI server, Pydantic validation, `/docs` interactive documentation. |
| **3** | **Streamlit** | Top-to-bottom rerun model, `st.session_state` persistence. |
| **4** | **Generative AI** | `temperature: 0.2` strict determinism ke liye, persona prompting. |
| **5** | **Multi-Agent** | 5 specialized agents context dilution ko avoid karne ke liye. |
| **6** | **Document Handling** | `PyPDF2` page extraction, `ReportLab` in-memory (`BytesIO`) PDF rendering. |
| **7** | **SDLC Domain** | Microservices decomposition, REST standards, WCAG 2.1 AA accessibility. |
| **8** | **Code Security** | SQL Injection (`PreparedStatements`), Hardcoded credentials remediation. |
| **9** | **DevOps & Config** | `.env` credentials isolation, `venv` dependency sandboxing. |
