import tkinter as tk
from tkinter import scrolledtext
import google.generativeai as genai

# Configure Gemini API
API_KEY = "AIzaSyA8lPG6E740ZHW6C74HgxR9oVJjx0UAhso"  # Replace with your Google API Key
genai.configure(api_key=API_KEY)

# Use the fast model
model = genai.GenerativeModel("gemini-1.5-flash")

# Chatbot GUI class
class ChatApp:
    def __init__(self, root):
        self.root = root
        self.root.title("AI ChatBot - Gemini (Fast Mode)")
        
        # Chat display
        self.chat_area = scrolledtext.ScrolledText(root, wrap=tk.WORD, width=70, height=25, font=("Arial", 12))
        self.chat_area.pack(padx=10, pady=10)
        self.chat_area.config(state=tk.DISABLED)
        
        # User input
        self.entry = tk.Entry(root, font=("Arial", 12), width=55)
        self.entry.pack(side=tk.LEFT, padx=10, pady=10)
        self.entry.bind("<Return>", lambda event: self.send_message())  # Enter key to send
        
        # Send button
        self.send_btn = tk.Button(root, text="Send", command=self.send_message, font=("Arial", 12))
        self.send_btn.pack(side=tk.LEFT, pady=10)
        
        # Start a new chat session
        self.chat = model.start_chat(history=[])

    def send_message(self):
        user_input = self.entry.get()
        if user_input.strip() == "":
            return
        
        # Show user message
        self.chat_area.config(state=tk.NORMAL)
        self.chat_area.insert(tk.END, "You: " + user_input + "\n", "user")
        self.chat_area.config(state=tk.DISABLED)
        self.entry.delete(0, tk.END)

        # Show bot response header
        self.chat_area.config(state=tk.NORMAL)
        self.chat_area.insert(tk.END, "Bot: ", "bot")
        self.chat_area.config(state=tk.DISABLED)
        self.chat_area.see(tk.END)

        # Stream AI response in real time
        for chunk in self.chat.send_message(user_input, stream=True):
            if chunk.candidates and chunk.candidates[0].content.parts:
                text = chunk.candidates[0].content.parts[0].text
                self.chat_area.config(state=tk.NORMAL)
                self.chat_area.insert(tk.END, text)
                self.chat_area.config(state=tk.DISABLED)
                self.chat_area.see(tk.END)

        # Add line break after bot finishes
        self.chat_area.config(state=tk.NORMAL)
        self.chat_area.insert(tk.END, "\n\n")
        self.chat_area.config(state=tk.DISABLED)
        self.chat_area.see(tk.END)

# Run the app
if __name__ == "__main__":
    root = tk.Tk()
    app = ChatApp(root)
    root.mainloop()


Here is The Screenshot For How It Works!
<img width="846" height="692" alt="Screenshot 2025-08-16 133017" src="https://github.com/user-attachments/assets/d75f5628-3181-4b50-b1c7-d7977f2426ea" />
