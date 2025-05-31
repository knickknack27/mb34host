# Raj AI: A Real-Estate Voice Assistant by Dhruv Kant Ladia

## Project Overview

MB-Voice-SM is a voice-based real-estate assistant for Magic Bricks. Users can interact with the assistant using voice in Hinglish to inquire about properties. The assistant understands the user's query, retrieves relevant information from a knowledge base, and responds in a conversational Hinglish voice.

## Features

-   **Voice-to-Voice Interaction**: End-to-end voice-based conversation.
-   **Hinglish Language Support**: Understands and responds in Hinglish (Hindi-English mix).
-   **Retrieval Augmented Generation (RAG)**: Uses a knowledge base (`data.txt`) to provide fact-based answers about real estate listings.
-   **Conversational AI**: Powered by a Large Language Model (LLM) for natural conversation flow.
-   **Web Interface**: Simple frontend to interact with the voice assistant.
-   **Silence Detection**: Ignores audio inputs that are too short or silent.

## Technology Stack

-   **Backend**: Python, Flask
-   **Frontend**: HTML, CSS, JavaScript
-   **Speech-to-Text (ASR)**: Sarvam AI
-   **Text-to-Speech (TTS)**: Sarvam AI
-   **Translation**: Sarvam AI (English to Hinglish and vice-versa)
-   **Language Model (LLM)**: Google Gemini Pro via OpenRouter API
-   **Embeddings for RAG**: `sentence-transformers` (`all-mpnet-base-v2`)
-   **Audio Processing**: `pydub`
-   **Environment Management**: `python-dotenv`

## How it Works

The project implements a voice-to-voice RAG pipeline:

1.  **User Voice Input**: The user records their query through the web interface.
2.  **Audio Processing & Silence Detection**: The backend receives the audio. `pydub` is used to check if the audio is too short or too silent.
3.  **Speech-to-Text (ASR)**: If the audio is valid, it's sent to Sarvam AI for transcription from Hinglish voice to text.
4.  **Translation to English**: The transcribed Hinglish text is translated to English using Sarvam AI. This is done because the LLM and RAG system primarily work with English.
5.  **Retrieval Augmented Generation (RAG)**:
    *   The `data.txt` file (containing real estate listings in JSON format) is pre-processed at startup. It's chunked, and embeddings are generated for each chunk using `sentence-transformers`.
    *   The translated English user query is used to retrieve the most relevant chunks (context) from the `data.txt` embeddings.
6.  **LLM Interaction**: The translated English user query, conversation history, the system prompt (defining the assistant's persona and task), and the retrieved RAG context are sent to the `google/gemini-2.0-flash-001` model via the OpenRouter API.
7.  **Response Generation**: The LLM generates a response in English.
8.  **Translation to Hinglish**: The LLM's English response is translated to Hinglish using Sarvam AI.
9.  **Text-to-Speech (TTS)**: The Hinglish text response is converted into speech (base64 encoded WAV audio) using Sarvam AI.
10. **Response to User**: The audio response is sent back to the frontend and played to the user. The frontend also displays the user's transcript and the assistant's reply.

## Setup and Running the Project

### Prerequisites

-   Python 3.7+
-   pip (Python package installer)
-   Git (for cloning the repository)

### 1. Clone the Repository

```bash
git clone <repository-url>
cd mb-voice-sm
```
*(Replace `<repository-url>` with the actual URL of this repository)*

### 2. Create and Activate a Virtual Environment

It's highly recommended to use a virtual environment:

```bash
# For Windows
python -m venv venv
venv\Scripts\activate

# For macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies

Install all the required Python packages:

```bash
pip install -r requirements.txt
```

### 4. Set Up Environment Variables

The project requires API keys for Sarvam AI and OpenRouter.

1.  Create a file named `.env` in the root directory of the project (alongside `requirements.txt`).
2.  Add your API keys to the `.env` file:

    ```env
    SARVAM_API_KEY="YOUR_SARVAM_AI_API_KEY"
    OPENROUTER_API_KEY="YOUR_OPENROUTER_API_KEY"
    ```

    Replace `YOUR_SARVAM_AI_API_KEY` and `YOUR_OPENROUTER_API_KEY` with your actual keys.

### 5. Running the Flask Backend

Navigate to the `backend` directory and run the Flask application:

```bash
cd backend
python application.py
```

By default, the application will run on `http://127.0.0.1:5000/`.

### 6. Accessing the Frontend

Open your web browser and go to `http://127.0.0.1:5000/`. You should see the voice assistant interface.

## Project Structure

```
mb-voice-sm/
├── backend/
│   ├── static/
│   │   ├── index.html       # Frontend HTML
│   │   ├── script.js        # Frontend JavaScript
│   │   └── style.css        # Frontend CSS
│   ├── __pycache__/
│   ├── application.py     # Main Flask application (API endpoints, core logic)
│   ├── rag_helper.py      # Helper functions for RAG (data loading, embedding, retrieval)
│   └── output.log         # Log file for backend operations
├── .env                   # Environment variables (API keys) - YOU NEED TO CREATE THIS
├── data.txt               # Knowledge base for RAG (JSON format expected by rag_helper.py)
├── requirements.txt       # Python dependencies
└── README.md              # This file
```
*(Note: `real_estate_cleaned_generalized.json` is also present but `data.txt` seems to be the one actively used by the RAG system as per `application.py`.)*

## Key Files and Components

-   **`backend/application.py`**: The heart of the backend. It defines the Flask API, handles audio input, orchestrates calls to ASR, Translation, LLM, and TTS services, manages conversation history, and serves the frontend.
-   **`backend/rag_helper.py`**: Manages the Retrieval Augmented Generation process. It loads data from `data.txt`, creates text embeddings, and provides a function to retrieve relevant context for a given query.
-   **`data.txt`**: The knowledge base file used by the RAG system. It should be a JSON file where each entry has a "content" field with text that can be chunked and embedded. `rag_helper.py` expects this structure.
-   **`backend/static/index.html` & `backend/static/script.js`**: Provide the user interface for interacting with the voice assistant.
-   **`.env` (to be created)**: Stores sensitive API keys.
-   **`SYSTEM_PROMPT_BASE` (in `application.py`)**: Defines the persona, language, style, and instructions for the LLM (Raj, the Magic Bricks assistant).

## API Keys

You need to obtain API keys from:

-   **Sarvam AI**: For ASR, TTS, and Translation services. Store this as `SARVAM_API_KEY` in your `.env` file.
-   **OpenRouter**: To use the Google Gemini Pro LLM. Store this as `OPENROUTER_API_KEY` in your `.env` file.

## Customization

-   **Knowledge Base**: To change the information the assistant uses, modify or replace `data.txt`. Ensure it's a JSON file and that `rag_helper.py` can parse its structure (specifically looking for a "content" field in each JSON object, where the values within "content" are the texts to be embedded).
-   **Assistant Persona & Behavior**: Modify the `SYSTEM_PROMPT_BASE` string within `backend/application.py` to change the assistant's name, language style, response guidelines, etc.
-   **LLM Model**: The LLM model can be changed in `backend/application.py` within the `llm_payload` in the `transcribe_and_chat` function (currently `google/gemini-2.0-flash-001`). You might need to adjust the prompt or API parameters if you switch models.
-   **TTS Voice/Language**: The TTS voice (`speaker`) and language (`target_language_code`) can be changed in the `get_tts_audio_base64` function in `backend/application.py`.
-   **ASR Language**: The ASR language (`language_code`) can be changed in the `asr_payload` within the `transcribe_and_chat` function in `backend/application.py`. 
