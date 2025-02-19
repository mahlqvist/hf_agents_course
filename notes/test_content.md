# Extract Text and Imgages From PDF

Great question! When using **PyPDF** or **LangChain’s PDFLoader** to extract text from a PDF, images are typically **ignored** by default. This means that if the book contains important diagrams, code snippets as images, or other visual information, that context would be **lost** for the AI agent.

### How It Works:
1. **Text Extraction**:  
   - PyPDF and similar tools extract only **text-based content** from the PDF.
   - They do **not** process images, tables, or complex formatting unless explicitly handled.

2. **What Happens to Images?**  
   - If the book has **important images with explanatory content**, that data **won’t be included** in the vector database.
   - The AI would only "know" the surrounding text, potentially missing crucial context.

### Workarounds to Include Images:
If you want to **retain visual context**, here are some approaches:

1. **OCR (Optical Character Recognition)**  
   - Use **Tesseract OCR** or **PaddleOCR** to extract text from images within the PDF.
   - Combine this extracted text with the main text data.

2. **Multimodal AI (Text + Vision)**  
   - Use models like **BLIP-2** or **Donut** (from Hugging Face) to generate descriptions of images and store them in your vector database.

3. **Hybrid Approach**  
   - Extract text normally with PyPDF.
   - Identify images separately and run OCR or image captioning.
   - Store both text and image descriptions in your vector database for richer retrieval.

### Does This Matter for an AI Agent?  
Yes, depending on your use case. If the book relies heavily on **diagrams, charts, or images** (like programming books often do), missing them can lead to **incomplete understanding** by the AI agent. A hybrid method (text + OCR or captions) would be ideal in such cases.

Would you like help with implementing any of these techniques? 🚀

That sounds like a solid approach! Since you're already using **LangChain** and **PyPDFLoader**, we can extend your function to extract **both text and images**. Here's a structured hybrid approach:

### Steps:
1. **Extract text using PyPDFLoader** (already in your function).
2. **Extract images and apply OCR** (using `pdf2image` + `Tesseract OCR`).
3. **Store both text and extracted image text** in your vector database.

---

### Install Dependencies
You'll need these packages:
```bash
pip install pdf2image pytesseract poppler-utils
```
- `pdf2image`: Converts PDF pages to images.
- `pytesseract`: OCR engine to extract text from images.
- `poppler-utils`: Required by `pdf2image`.

---

### Hybrid PDF Processor Function
Here's an improved function that **extracts both text and images**:

```python
from langchain_community.document_loaders import PyPDFLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from pdf2image import convert_from_path
import pytesseract
import os

def get_pdf_chunks(pdf_file_name):
    # Load text using PyPDFLoader
    pdf_loader = PyPDFLoader(pdf_file_name)
    pages = pdf_loader.load()

    # Extract text from pages
    pdf_document = " ".join(page.page_content for page in pages)

    # OCR Processing for Images
    images = convert_from_path(pdf_file_name)  # Convert PDF pages to images
    ocr_texts = [pytesseract.image_to_string(img) for img in images]  # Extract text from images

    # Combine extracted text and OCR text
    full_text = pdf_document + "\n".join(ocr_texts)

    # Chunk the combined text
    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=800,
        chunk_overlap=0,
        length_function=len,
        separators=["\n\n", "\n", ".", "?", "!", " ", ""]
    )

    pdf_chunks = text_splitter.split_text(full_text)

    return pdf_chunks
```

---

### Why This Works:
✅ Extracts **text** from the PDF using **PyPDFLoader**  
✅ Extracts **images** and **runs OCR** on them using **Tesseract**  
✅ **Merges** text and OCR results before chunking  
✅ Uses LangChain’s **RecursiveCharacterTextSplitter** for clean chunking  

---

### Next Steps:
- Store the `pdf_chunks` in **ChromaDB** or another vector database.
- Use **LangChain’s retriever** to query the database.
- If you want to **improve OCR accuracy**, consider fine-tuning **Tesseract** with `pytesseract.image_to_string(img, config="--psm 6")`.

Would you like help integrating this into your existing LangChain workflow? 🚀

<hr>

To extract text, images, code snippets, and tables from a PDF and prepare them for a vector database, follow this structured approach:

### 1. **Extraction Tools & Libraries**
- **Text & Images**: Use **PyMuPDF (fitz)** for efficient text extraction and image retrieval. It provides layout details (e.g., coordinates) to preserve order.
- **Tables**: Use **pdfplumber** or **Camelot** for accurate table detection and extraction.
- **Code Snippets**: Combine regex patterns (e.g., `r'```python(.*?)```'`) with font/style analysis (if PDF metadata includes monospace fonts).

### 2. **Processing Workflow**
- **Text**:
  - Clean extracted text (remove extra spaces, fix encoding).
  - Use **NLTK** or **spaCy** for sentence splitting to maintain context.
- **Images**:
  - Save images to a directory with filenames like `page_{num}_image_{idx}.png`.
  - Record their positions (page number, nearby text) for context linking.
- **Tables**:
  - Convert tables to Markdown or CSV format for structured storage.
  - Example: Use `pdfplumber` to extract table data and `tabulate` to format.
- **Code**:
  - Identify code blocks via regex or font detection.
  - Preserve formatting and add syntax highlighting metadata (e.g., `language: python`).

### 3. **Chunking Strategy**
- **Context-Aware Grouping**:
  - Group elements (text, image references, code, tables) by their natural order on the page.
  - Example: A paragraph followed by an image becomes a chunk with text and an image reference.
- **Token Limits**:
  - Split large text sections into chunks ≤ 512 tokens, with 10-15% overlap for context continuity.
- **Metadata**:
  - Include `type` (text/code/table/image), `page`, `source_book`, and `image_path`.

### 4. **Vector Database Storage**
- **Text Embeddings**:
  - Use models like **BERT** or **sentence-transformers** to generate embeddings for text/code/table chunks.
- **Image Handling**:
  - Store images in cloud storage (e.g., AWS S3) or a local directory.
  - Optionally, generate image embeddings using **CLIP** or **ResNet** and link them to text chunks via metadata.
- **Database Schema**:
  - Each entry includes:
    - `content`: Text, code, or table data.
    - `embedding`: Vector representation.
    - `metadata`: Type, page, image paths, and other context.

### 5. **AI Agent Retrieval**
- **Multi-Modal Queries**:
  - For text-based queries, search the vector database using text embeddings.
  - For image-related queries, use image embeddings or metadata references.
- **Response Generation**:
  - Retrieve relevant chunks and resolve image paths to include images in outputs.

### Example Code Outline

```python
import fitz  # PyMuPDF
import pdfplumber
import re

def extract_pdf_content(pdf_path, image_dir):
    doc = fitz.open(pdf_path)
    content = []
    
    for page_num, page in enumerate(doc):
        # Extract text with coordinates
        text = page.get_text("dict")
        blocks = [b for b in text["blocks"] if b["type"] == 0]
        
        # Extract images
        image_list = page.get_images(full=True)
        for img_idx, img in enumerate(image_list):
            image = doc.extract_image(img[0])
            img_path = f"{image_dir}/page_{page_num}_image_{img_idx}.{image['ext']}"
            with open(img_path, "wb") as f:
                f.write(image["image"])
        
        # Extract tables with pdfplumber
        with pdfplumber.open(pdf_path) as pdf:
            pdf_page = pdf.pages[page_num]
            tables = pdf_page.extract_tables()
            for table in tables:
                # Convert table to Markdown
                markdown_table = "\n".join(["|".join(row) for row in table])
                content.append({"type": "table", "content": markdown_table, "page": page_num})
        
        # Identify code blocks using regex
        text_content = page.get_text()
        code_blocks = re.findall(r'```python(.*?)```', text_content, re.DOTALL)
        for code in code_blocks:
            content.append({"type": "code", "content": code.strip(), "page": page_num})
    
    return content

# Chunking and storing in a vector database
def chunk_and_store(content, chunk_size=512, overlap=50):
    # Implement context-aware chunking with metadata
    pass
```

### Tools & Libraries
- **Text/Image Extraction**: PyMuPDF (`fitz`), `pdfplumber`
- **Table Extraction**: `Camelot`, `tabulate`
- **Chunking**: `langchain.text_splitter`
- **Vector DB**: Pinecone, Chroma, FAISS
- **Embeddings**: `sentence-transformers`, OpenAI Embeddings
- **Image Models**: CLIP, ResNet (via `torchvision`)

### Final Tips
- **Test Extractions**: Validate extractions on sample pages before full processing.
- **Multi-Modal Models**: Use OpenAI GPT-4 Vision or LLaVA if the agent needs to interpret images directly.
- **OCR Fallback**: Use `pytesseract` for image-based PDFs if text extraction fails.