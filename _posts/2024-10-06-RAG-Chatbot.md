---
title: "Rag Chatbot"
date: 2024-05-29
tags: [rag, chatbot, development]
header:
  image: "/images/port.jpg"
excerpt: "RAG Chatbot"
mathjax: "true"
---

Building a Multilingual Chatbot for VDI/VDE-IT

I recently wrapped up an exciting project that brings together cutting-edge AI, natural language processing, and an intuitive interface to build a smarter chatbot for VDI/VDE-IT. As part of my final project for the LLM Zoomcamp, I wanted to design something practical and impactful—an AI-driven chatbot that enhances the user experience of interacting with the institute’s FAQ page and resources.

In this post, I’ll walk you through the key components of this chatbot project, how it tackles real-world challenges, and what I learned along the way.

The Challenge

VDI/VDE-IT supports innovations in technology and engineering and is involved in helping businesses, research institutions, and public entities with topics like funding and digital transformation. While the institute already had a FAQ page and a simple chatbot, these resources were limited. Users had to rely on keyword searches and static responses, which made it difficult to get personalized or complex answers. There was no real interaction—only basic, pre-programmed answers.

That’s where my project comes in: a chatbot powered by a Large Language Model (LLM) that uses AI to dynamically understand user questions, even when they’re phrased differently. The goal was to make it easier for people to navigate the intricacies of administrative processes, such as applying for project funding or getting details on BMBF projects, without scrolling through long FAQ pages.

Key Features of the Chatbot

📚 Custom Knowledge Base

The first task was to scrape and build a knowledge base from the VDI/VDE-IT FAQ page. By extracting questions, answers, categories, and links, I ensured the chatbot could reference actual VDI/VDE-IT content to provide accurate and relevant responses. Instead of static answers, the chatbot delivers dynamic, context-aware answers tailored to the user’s query.

🔍 Text and Vector-Based Search

A major improvement over basic keyword-based search was the introduction of vector embeddings. The chatbot doesn’t just match exact keywords; it can understand the meaning behind a question, which enables it to find relevant answers even if the query isn’t a perfect match to the FAQ. This is particularly useful in a multilingual context, where users may phrase the same question in many different ways.

I implemented ElasticSearch for the text-based part, but also incorporated a more advanced vector embedding search to improve how the chatbot understands and matches user queries to answers.

🤖 Retrieval-Augmented Generation (RAG) Evaluation

To measure the effectiveness of the chatbot, I tested how well it could retrieve the correct answers from the knowledge base, even when asked alternative versions of the same question. I created a “ground truth” dataset of alternative question formulations and evaluated the chatbot’s responses to see if they matched up.

I used two metrics to evaluate this:

	•	Hit Rate: How often the chatbot retrieves the correct answer.
	•	MRR (Mean Reciprocal Rank): This metric evaluates how highly the correct answer ranks when a user asks a question.

💻 Streamlit Interface

The chatbot is accessible through a user-friendly Streamlit app, which provides an easy way for users to interact with the system. The UI isn’t just about simplicity—it’s optimized for providing instant, helpful feedback, enhancing the overall user experience.

⚙️ Data Ingestion Pipeline

A significant part of this project was setting up a data ingestion pipeline. This included:

	•	Scraping FAQ content with Selenium, especially since the content is dynamically loaded with JavaScript.
	•	Indexing documents in ElasticSearch to make them searchable by the chatbot.
	•	Integrating the PostgreSQL database for usage monitoring and logging.

📊 Monitoring and Analytics

Using Grafana and PostgreSQL, I set up a robust monitoring system that tracks interactions, user behavior, and query patterns. This helps keep an eye on usage statistics, like the number of queries, the language distribution of queries, and the feedback users give to answers (thumbs up or thumbs down).

Monitoring allows us to continually improve the chatbot by identifying common issues and popular queries.

Reproducibility and Deployment

For easy setup and reproducibility, I containerized the entire system using Docker and Docker Compose. Anyone interested in replicating the project can follow a straightforward process to spin up the app locally.

While cloud deployment wasn’t part of the initial project, it’s one of the future directions I’m planning to explore. The current setup runs locally, but the flexibility of the containerized environment means it’s ready for the cloud.

Learnings and Future Directions

This project was a deep dive into both technical implementation and real-world problem-solving. Some of the key takeaways include:

	•	Vector embeddings significantly improve the chatbot’s ability to understand user queries in different formulations, making it more robust in multilingual settings.
	•	Retrieval evaluation using hit rate and MRR gave a clear picture of how well the chatbot retrieves relevant information, highlighting areas for improvement, especially with more complex queries.
	•	Monitoring is essential for continuous improvement. Gathering feedback and analyzing usage patterns is key to iterating and refining the chatbot’s performance.

Future Improvements

There’s still a lot of potential to expand on this project. Some next steps include:

	•	Improved Evaluation: Use more accurate ground truth data to improve the chatbot’s reliability.
	•	Query Rewriting: Automatically rewrite user queries to better align with the knowledge base, improving retrieval.
	•	Document Re-Ranking: Enhance the search results ranking system to ensure the most relevant answers always appear at the top.
	•	Cloud Deployment: Deploying the system to the cloud will make it accessible to a broader audience and easier to maintain.

Final Thoughts

The VDI/VDE-IT Chatbot project was an exciting opportunity to build a tool that has real-world applications, helping users navigate complex administrative topics in a more intuitive way. Combining AI, search technology, and user-friendly interfaces has made the chatbot a practical, scalable solution for improving access to information.

I’m looking forward to continuing work on this project and pushing its capabilities even further. Stay tuned for more updates!

Feel free to check out the project repository if you’d like to explore the code or try setting up the chatbot yourself.