LearnSphere AI Tutor 🎓
A Personal AI Learning Companion for the Defang Hackathon.

LearnSphere is an intelligent, interactive web application designed to act as a personal tutor. It leverages the power of AI agents through CrewAI to provide two distinct modes of learning assistance, all within a friendly Streamlit interface.

✨ Features
Dual-Agent System:

Expert Tutor: Ask any academic question and receive clear, detailed explanations, just like you would from a world-class professor.

Resource Analyst: Provide a URL, and the AI will analyze the webpage's content to answer your questions based exclusively on that source material.

Interactive Chat Interface: A familiar, easy-to-use chat layout for a seamless user experience.

Secure & Configurable: Uses a .env file to manage API keys, a best practice for security and deployment.

Humanized Personas: Agents are designed with unique backstories and roles to make the interaction more engaging and natural.

Robust URL Detection: Uses regular expressions to reliably detect links within your prompts.

🚀 Tech Stack
Backend: Python

AI Framework: CrewAI

Web Framework: Streamlit

Core Tools: crewai-tools for web search capabilities.

Configuration: python-dotenv

⚙ Setup and Installation
Follow these steps to get your local environment set up and running.

1. Clone the Repository
git clone <your-repo-url>
cd <your-repo-folder>

2. Create a Virtual Environment
It's highly recommended to use a virtual environment to manage project dependencies.

# For macOS/Linux
python3 -m venv venv
source venv/bin/activate

# For Windows
python -m venv venv
venv\Scripts\activate

3. Install Dependencies
Install all the necessary packages from the requirements.txt file.

pip install -r requirements.txt

4. Set Up Environment Variables
You need an API key from Serper to enable the web search functionality for the tutor agent.

Create a file named .env in the root of your project directory.

Add your API key to this file:

SERPER_API_KEY="your_secret_api_key_here"

▶ Running the Application
Once the setup is complete, you can run the Streamlit application with a single command:

streamlit run app.py

Your web browser should automatically open to the LearnSphere AI Tutor application!

Built with ❤ for the Defang Hackathon.
