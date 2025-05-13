
# HSN Lookup Agent

An AI-powered assistant that provides GST details based on HSN codes using Google Gemini through the Agent Development Kit (ADK). This tool reads HSN data from an Excel file and responds with GST rates and product descriptions.


## 📚 Table of Contents

- [HSN Lookup Agent](#hsn-lookup-agent)
- [Features](#-features)
- [Project Structure](#-project-structure)
- [Installation](#-installation)
- [Getting Gemini API Key](#-getting-gemini-api-key)
- [Environment Setup](#-environment-setup)
- [Code Overview](#-code-overview)
  - [__init__.py](#initpy)
  - [agent.py](#agentpy)
- [Running the Agent](#-running-the-agent)
- [Testing](#-testing)
- [Dependencies](#-dependencies)

---

## 🚀 Features

- Look up HSN codes and get GST rates
- Gemini-based natural language interface
- Loads HSN data from an Excel sheet
- Lightweight and easy to run locally

---

## 📁 Project Structure

```
project/
│
├── hsn_agent/
│   ├── __init__.py
│   ├── agent.py
│   └── .env
│
└── HSN_SAC.xlsx
```

---

## 🛠 Installation

Install the required dependencies:

```bash
pip install google-adk pandas openpyxl
```

---

## 🔑 Getting Gemini API Key

1. Visit [MakerSuite](https://makersuite.google.com/app)
2. Sign in with your Google account
3. Click your profile picture → API Keys → Create API Key
4. Copy the generated key

---

## ⚙️ Environment Setup

Create a `.env` file at `hsn_agent/.env` and add:

```env
GOOGLE_GENAI_USE_VERTEXAI=FALSE
GOOGLE_API_KEY=your_actual_api_key_here
```

Replace `your_actual_api_key_here` with your Gemini API key.

---

## 📄 Code Overview

### `__init__.py`

Marks the `hsn_agent` folder as a Python module:

```python
from . import agent
```

---

### `agent.py`

#### 🔍 Core Functions:

- `load_hsn_data_from_excel(file_path)`: Reads HSN codes and descriptions from Excel
- `determine_gst_rate(hsn_code)`: Estimates GST rate based on code ranges
- `get_hsn_info(hsn_code)`: Returns a formatted report of GST info

#### 🤖 Gemini Agent:

```python
from google.adk.agents import Agent

root_agent = Agent(
    name="hsn_lookup_agent",
    model="gemini-2.0-flash",
    description="Agent to provide information about HSN codes and tax rates.",
    instruction="You are a helpful assistant who provides GST details for HSN codes.",
    tools=[get_hsn_info],
)
```

---

## ▶️ Running the Agent

From your project root, run:

```bash
adk web
```

Visit: [http://localhost:8000](http://localhost:8000)

---

## 🧪 Testing

Try asking:

- "What is the GST for HSN 8471?"
- "Tell me about HSN 1001"
- "GST rate for code 8703"

---

## 📦 Dependencies

- `google-adk` — Gemini ADK
- `pandas` — Excel data processing
- `openpyxl` — For `.xlsx` file support
