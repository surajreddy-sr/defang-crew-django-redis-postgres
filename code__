"""
LearnSphere AI Tutor - Your Personal AI Learning Companion
A Streamlit web application powered by CrewAI for intelligent, context-aware tutoring.

This application provides a dual-agent system:
1.  *Expert Tutor Agent*: For general academic questions and explanations.
2.  *Resource Analyst Agent*: For answering questions based on specific web content provided by the user via a URL.

Humanized by Gemini for the Defang Hackathon.
"""
import os
import re
import streamlit as st
from dotenv import load_dotenv
from crewai import Agent, Task, Crew, Process
from crewai_tools import SerperDevTool, WebsiteSearchTool

# --- ENVIRONMENT SETUP ---
# Load environment variables from a .env file for secure API key management.
load_dotenv()

# It's crucial to have the Serper API key for the search tool to work.
SERPER_API_KEY = os.getenv("2e1b708cd8b82fd92f1a4064f02aa263492e135d")

# --- AGENT DEFINITIONS ---
# Crafting distinct personalities for our AI agents makes the interaction feel more natural.

# Agent 1: The Knowledgeable and Encouraging Tutor
tutor_agent = Agent(
    role='Expert Academic Tutor',
    goal='Provide students with clear, concise, and engaging explanations. Generate practice problems and revision notes to solidify their understanding.',
    backstory=(
        "You are Dr. Alistair Finch, a celebrated Oxford professor with a Ph.D. in educational psychology. "
        "You have a remarkable gift for demystifying complex topics. Your teaching philosophy is built on "
        "patience, encouragement, and the belief that any student can succeed with the right guidance. "
        "You create a supportive learning environment where students feel comfortable asking anything."
    ),
    verbose=True,
    allow_delegation=False,
    tools=[SerperDevTool()]  # Equipped with powerful search capabilities.
)

# Agent 2: The Focused and Meticulous Resource Analyst
resource_agent = Agent(
    role='Resource Analysis Specialist',
    goal='Answer questions EXCLUSIVELY based on the content of a provided URL. Do not use external knowledge.',
    backstory=(
        "You are ARC (Archival Retrieval Correspondent), an AI designed for pure, unbiased information extraction. "
        "Your entire existence is dedicated to analyzing the text within a given digital document. "
        "You are meticulous, precise, and ignore all information outside the scope of the provided source, "
        "ensuring the user gets answers grounded only in their chosen material."
    ),
    verbose=True,
    allow_delegation=False
    # Note: The tool for this agent is assigned dynamically based on the user's URL.
)

# --- HELPER FUNCTIONS ---

def find_url_in_text(text: str) -> str | None:
    """
    A more robust way to find the first HTTP or HTTPS URL in a string using regex.
    This is better than simple string checking.
    """
    # This regex pattern is designed to find URLs starting with http:// or https://
    url_pattern = re.compile(r'https?://\S+')
    match = url_pattern.search(text)
    return match.group(0) if match else None

# --- STREAMLIT UI CONFIGURATION ---

st.set_page_config(
    page_title="LearnSphere AI Tutor",
    page_icon="🎓",
    layout="wide",
    initial_sidebar_state="expanded"
)

# --- SIDEBAR ---
with st.sidebar:
    st.title("About LearnSphere")
    st.markdown("""
    Welcome to your personal AI-powered learning companion! LearnSphere is designed to help you understand complex topics, whether you're exploring a new subject or analyzing a specific article.

    *How it works:*
    - *Ask a question:* The Expert Tutor will provide a detailed explanation.
    - *Provide a URL:* Paste a URL into the chat, and the Resource Analyst will answer questions based only on that page's content.
    """)
    st.warning("Please set your SERPER_API_KEY in a .env file to enable search functionality.")

# --- MAIN CHAT INTERFACE ---

st.title("🎓 LearnSphere AI Tutor")
st.write("Your personal AI-powered learning companion. Ask me anything!")

# Initialize chat history in session state if it doesn't exist.
if "messages" not in st.session_state:
    st.session_state.messages = []

# Display past messages from the chat history.
for message in st.session_state.messages:
    with st.chat_message(message["role"]):
        st.markdown(message["content"])

# --- CORE LOGIC ---

# Capture user input from the chat box.
user_prompt = st.chat_input("Explain quantum physics or analyze a URL for me...")

if user_prompt:
    # Display the user's message in the chat interface.
    st.session_state.messages.append({"role": "user", "content": user_prompt})
    with st.chat_message("user"):
        st.markdown(user_prompt)

    # Check for API key before proceeding.
    if not SERPER_API_KEY:
        st.error("Search functionality is disabled. Please provide a SERPER_API_KEY in your .env file.")
        st.stop()

    with st.chat_message("assistant"):
        with st.spinner("🤖 Thinking... Please wait."):
            try:
                # Decide which agent to use based on the user's prompt.
                url = find_url_in_text(user_prompt)
                
                if url:
                    st.info(f"Detected URL. The Resource Analyst is analyzing the content from: {url}")
                    # The user provided a URL, so we use the resource_agent.
                    url_search_tool = WebsiteSearchTool(website_url=url)
                    resource_agent.tools = [url_search_tool]
                    task = Task(
                        description=f"Analyze the content of the URL and answer the user's question based on it. The user's prompt is: '{user_prompt}'",
                        expected_output="A detailed answer derived exclusively from the website's content, presented clearly.",
                        agent=resource_agent
                    )
                else:
                    st.info("No URL detected. The Expert Tutor is on the case!")
                    # It's a general query, so we use the tutor_agent.
                    task = Task(
                        description=f"Fulfill the student's learning request: '{user_prompt}'. Provide a clear explanation, practice problems, or revision notes as needed.",
                        expected_output="A comprehensive, encouraging, and helpful response that addresses the student's learning needs.",
                        agent=tutor_agent
                    )

                # Create and run the crew.
                learning_crew = Crew(
                    agents=[tutor_agent, resource_agent],
                    tasks=[task],
                    process=Process.sequential,
                    verbose=2
                )
                
                result = learning_crew.kickoff()
                st.markdown(result)
                st.session_state.messages.append({"role": "assistant", "content": result})

            except Exception as e:
                st.error(f"An error occurred: {e}")
                st.session_state.messages.append({"role": "assistant", "content": f"Sorry, I ran into a problem: {e}"})
