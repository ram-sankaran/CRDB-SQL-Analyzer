# Required Python packages for CockroachDB SQL Statement Analyzer

# Flask: Web framework for the user interface
flask

# Requests: HTTP client used to call the local LLM (Ollama)
requests

# Markdown: Converts LLM output (recommendations) into rich HTML
markdown

# Installation Instructions
# ----------------------------
# 1. Create and activate a virtual environment (optional but recommended):
     python3 -m venv venv
     source venv/bin/activate  (Linux/macOS)
    .\venv\Scripts\activate   (Windows)
#
# 2. Install dependencies:
    pip install -r requirements.txt

# 3. Run the Flask app:
    python3 analyze_sql_bundle.py
#
# 4. Access it in your browser:
    http://localhost:5050

#  Make sure Ollama and mistral LLM are  running locally:
   ollama serve
   ollama run Mistral
