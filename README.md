# Clinical Comprehensive Pipeline (CCP) Accelerator

A web-based application that processes medical transcripts from ambient listening software into structured clinical data using Amazon Bedrock Nova Pro and IMO Health APIs.

## Features

The application implements a 3-step pipeline (normalization/refinement steps have been removed):

1. **Transcript Input** - Enter or upload medical transcripts from ambient listening software
2. **SOAP Note Generation** - Uses Amazon Bedrock Nova Pro AI model to convert transcripts into structured SOAP format (Subjective, Objective, Assessment, Plan)
3. **Entity Extraction** - Extracts medical entities (problems, procedures, medications, labs) from Assessment and Plan sections using IMO Entity Extraction API

## Setup

### Prerequisites

- Python 3.8 or higher
- AWS Account with Bedrock access (for Nova Pro model)
- AWS credentials configured (via AWS CLI or environment variables)
- IMO Health API credentials (optional - demo mode available)

### Installation

1. Navigate to the project directory:
```bash
cd Ambient-AI-Solution
```

2. Install required packages:
```bash
pip install -r requirements.txt
```

3. Configure AWS credentials (choose one method):

   **Option A: AWS CLI**
   ```bash
   aws configure
   ```

   **Option B: Environment Variables**
   ```bash
   export AWS_ACCESS_KEY_ID=your_access_key
   export AWS_SECRET_ACCESS_KEY=your_secret_key
   export AWS_DEFAULT_REGION=us-east-1
   ```

   **Option C: Update config.py**
   ```python
   aws_access_key_id = "your_access_key"
   aws_secret_access_key = "your_secret_key"
   aws_region = "us-east-1"
   ```

4. Enable Bedrock Nova Pro model in your AWS account:
   - Go to AWS Bedrock Console
   - Navigate to Model Access
   - Request access to "Amazon Nova Pro" model
   - Wait for approval (usually instant)

5. Configure IMO API credentials (optional):
   - Open `config.py`
   - Add your IMO Health API credentials
   - Set `demo_mode = False` to use live APIs

### Running the Application

1. Start the Flask server:
```bash
python app.py
```

2. Open your browser and navigate to:
```
http://localhost:5001
```

## Project Structure

```
Ambient-AI-Solution/
├── app.py                      # Flask backend with API endpoints
├── soap_generator.py           # SOAP note generation module
├── nlp_processor.py           # IMO API integration for NLP
├── config.py                  # Configuration and API credentials
├── templates/
│   └── index.html            # Frontend UI
├── static/                    # Static files (CSS, JS, images)
└── sample_data/
    └── sample_transcript.txt  # Sample medical transcript
```

## API Endpoints

### POST /generate_soap
Generate a SOAP note from a medical transcript.
- **Input**: `{ "transcript": "medical transcript text..." }`
- **Output**: `{ "soap_note": {...}, "success": true }`

### POST /extract_entities
Extract entities from SOAP note using IMO Entity Extraction API.
- **Input**: `{ "soap_note": {...} }`
- **Output**: `{ "entities": {...}, "entity_counts": {...}, "success": true }`

### Note: Normalization & Refinement removed
The normalization and clinician-driven refinement steps have been removed from this version of the pipeline. The API endpoints `/normalize_entities` and `/refine_entities` are intentionally disabled and will return HTTP 410 (Gone).

### GET /load_sample_transcript
Load a sample medical transcript for testing.
- **Output**: `{ "transcript": "...", "success": true }`

## Demo Mode

The application includes a demo mode that works without IMO API credentials. It uses keyword-based entity extraction and mock normalization to demonstrate the pipeline flow.

To enable demo mode:
- Keep default values in `config.py`
- Or set `demo_mode = True`

## IMO Health API Integration

When configured with valid API credentials, the application integrates with:

1. **IMO Entity Extraction API**
   - URL: `https://api.imohealth.com/entityextraction/pipelines/imo-clinical-comprehensive`
   - Extracts medical entities from clinical text

2. **IMO Precision Normalize API**
   - URL: `https://api.imohealth.com/precision-normalize/v1`
   - Normalizes entities to IMO terminology

## Usage

1. **Enter Transcript**: Paste a medical transcript or click "Load Sample Transcript"
2. **Generate SOAP**: Click "Generate SOAP Note" to structure the transcript
3. **Extract Entities**: Click "Extract Entities" to identify medical concepts
4. **Normalize**: Click "Normalize with IMO" to standardize entity codes
5. **Refine**: If entities need refinement, click "Refine Entities" to add specificity

## Technologies Used

- **Backend**: Flask (Python)
- **Frontend**: HTML, CSS, JavaScript
- **AI Model**: Amazon Bedrock Nova Pro (for SOAP note generation)
- **APIs**: IMO Health Entity Extraction & Precision Normalize
- **NLP**: Medical entity recognition and normalization
- **Cloud**: AWS Bedrock

## How It Works

### SOAP Note Generation with Bedrock Nova Pro

When you click "Generate SOAP Note", the application:

1. Sends the medical transcript to Amazon Bedrock Nova Pro model
2. Uses the system prompt: *"I am an expert SOAP note generator. Given a medical transcript of inpatient or outpatient visits I can create a SOAP note."*
3. The AI model analyzes the conversation and generates structured sections:
   - **Subjective**: Patient's reported symptoms, history, and concerns
   - **Objective**: Physical examination findings, vital signs, test results
   - **Assessment**: Clinical diagnosis and evaluation
   - **Plan**: Treatment plan and recommendations
4. Returns a professionally formatted SOAP note

### Fallback Mechanism

If AWS Bedrock is unavailable, the system automatically falls back to rule-based SOAP generation to ensure uninterrupted service.

## License

This project is part of the Solution Engineering toolkit.

## Support

For questions or issues, please contact the development team.
# CCP-Solution-Accelerator
