import streamlit as st
from groq import Groq
import PyPDF2

st.set_page_config(page_title="AI Bro", page_icon="🤖")

st.title("🤖 AI Bro")
st.write("Hi, I am AI Bro. I built this when I was 15 to learn how AI apps work.")

# Sidebar
st.sidebar.header("Settings")
api_key = st.sidebar.text_input("Enter Groq API Key", type="password")
st.sidebar.write("Get free key from console.groq.com")

if not api_key:
    st.info("Please add your API key in sidebar to start.")
    st.stop()

client = Groq(api_key=api_key)

# Memory for chat
if "messages" not in st.session_state:
    st.session_state.messages = []

# PDF upload
pdf_file = st.file_uploader("Upload PDF if you want to ask from PDF", type="pdf")
pdf_text = ""

if pdf_file:
    reader = PyPDF2.PdfReader(pdf_file)
    for page in reader.pages:
        pdf_text += page.extract_text() or ""
    st.success(f"PDF loaded, {len(reader.pages)} pages found.")

# Show old messages
for msg in st.session_state.messages:
    with st.chat_message(msg["role"]):
        st.markdown(msg["content"])

# User input
if prompt := st.chat_input("Ask me anything..."):
    st.session_state.messages.append({"role": "user", "content": prompt})
    with st.chat_message("user"):
        st.markdown(prompt)

    # If PDF is there, use it
    system_message = "You are AI Bro, a friendly and helpful assistant. Keep answers simple and clear."
    if pdf_text:
        system_message += f" Answer from this PDF: {pdf_text[:8000]}"

    all_messages = [{"role": "system", "content": system_message}] + st.session_state.messages

    with st.chat_message("assistant"):
        box = st.empty()
        answer = ""
        try:
            response = client.chat.completions.create(
                model="openai/gpt-oss-20b",
                messages=all_messages,
                stream=True
            )
            for chunk in response:
                text = chunk.choices[0].delta.content
                if text:
                    answer += text
                    box.markdown(answer + "▌")
            box.markdown(answer)
        except Exception as e:
            answer = f"Oops, error came: {e}"
            box.markdown(answer)

    st.session_state.messages.append({"role": "assistant", "content": answer})
