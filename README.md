# HSN-Code-Validator
HSN Code Validator Description


STEP 1: INSTALL REQUIRED TOOLS
Open your terminal or command prompt.
Install the Google ADK and other dependencies:
pip install google-adk pandas openpyxl
•	google-adk – Agent Development Kit for creating Gemini-based assistants.
•	pandas – To read Excel files if needed.
•	openpyxl – Allows reading .xlsx Excel format.
________________________________________
STEP 2: CREATE PROJECT FOLDER STRUCTURE
Create folders and files as shown below:
project/
│
├── hsn_agent/
│   ├── __init__.py
│   ├── agent.py
│   └── .env
│
└── HSN_SAC.xlsx 
________________________________________
STEP 3: GET YOUR GOOGLE GEMINI API KEY
1.	Go to: https://makersuite.google.com/app
2.	Sign in with your Google account.
3.	Click on your profile picture (top-right corner).
4.	Choose "API Keys" → then click "Create API Key".
5.	Copy the key.
________________________________________
STEP 4: SETUP .env FILE
In the file hsn_agent/.env, paste this:
GOOGLE_GENAI_USE_VERTEXAI=FALSE
GOOGLE_API_KEY=your_actual_api_key_here
________________________________________
STEP 5: CODE THE MODULES
5.1 __init__.py
This file ensures Python treats hsn_agent/ as a module:
from . import agent
________________________________________
5.2 agent.py (Hardcoded HSN Data Version)


import pandas as pd
from google.adk.agents import Agent

def determine_gst_rate(hsn_code: str) -> str:
    """Determine GST rate based on HSN code ranges."""
    try:
    
        hsn_code = hsn_code.zfill(4)
        hsn_num = int(hsn_code[:4])  
        
        if hsn_num < 1000:
            return "0%"
        elif 1001 <= hsn_num <= 2200:  
            return "5%"
        elif 2201 <= hsn_num <= 2400: 
            return "28%"
        elif 2401 <= hsn_num <= 4000: 
            return "18%"
        elif 4001 <= hsn_num <= 5000:  
            return "5%"
        elif 5001 <= hsn_num <= 7000: 
            return "12%"
        elif 7001 <= hsn_num <= 9000: 
            return "18%"
        elif hsn_num >= 9001: 
            return "18%"
        else:
            return "18%"  
    except:
        return "18%"  

def load_hsn_data_from_excel(file_path: str) -> dict:
    try:
        df = pd.read_excel(file_path)
        
        hsn_col = next(col for col in df.columns if 'hsn' in col.lower())
        desc_col = next(col for col in df.columns if 'desc' in col.lower())
        

        hsn_data = {}
        for _, row in df.iterrows():
            hsn_code = str(row[hsn_col]).strip()
            if hsn_code:  
                hsn_data[hsn_code] = {
                    "description": row[desc_col],
                    "gst": determine_gst_rate(hsn_code)
                }
        return hsn_data
    except Exception as e:
        print(f"Error loading HSN data: {e}")
        print("Please ensure your Excel file has columns containing 'HSN' and 'Description'")
        return {}

HSN_DATA = load_hsn_data_from_excel(r"D:\Task\HSN_SAC.xlsx")

def get_hsn_info(hsn_code: str) -> dict:
    hsn_code = str(hsn_code).strip().zfill(4)  
    
    info = HSN_DATA.get(hsn_code)
    if info:
        return {
            "status": "success",
            "report": (
                f"HSN Code {hsn_code}: {info['description']}. "
                f"Applicable GST: {info['gst']} (estimated based on HSN range)."
            )
        }
    else:
        
        similar_codes = [code for code in HSN_DATA.keys() if code.endswith(hsn_code)]
        if similar_codes:
            info = HSN_DATA[similar_codes[0]]
            return {
                "status": "success",
                "report": (
                    f"HSN Code {similar_codes[0]}: {info['description']}. "
                    f"Applicable GST: {info['gst']} (estimated based on HSN range). "
                    f"(Note: Found similar code {similar_codes[0]} for your input {hsn_code})"
                )
            }
        return {
            "status": "error",
            "error_message": f"No data found for HSN code '{hsn_code}'. Please verify the code."
        }

root_agent = Agent(
    name="hsn_lookup_agent",
    model="gemini-2.0-flash",
    description="Agent to provide information about HSN codes and tax rates.",
    instruction="You are a helpful assistant who provides GST details for HSN codes.",
    tools=[get_hsn_info],
)
________________________________________
STEP 6: RUN THE AGENT LOCALLY
From the project root folder, run:
adk web
This will start a local development server at:
http://localhost:8000
________________________________________
✅ STEP 7: TEST THE AGENT
1.	Open browser → Go to http://localhost:8000
2.	You will see your agent: hsn_lookup_agent
3.	Ask questions like:
o	"What is the GST for HSN 8471?"
o	"Tell me about HSN 1001"
o	"GST rate for code 8703"
