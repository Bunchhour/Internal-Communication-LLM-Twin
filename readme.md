# Internal Communication Twin

An AI assistant that helps employees write internal communication in a consistent professional tone.

## About

Build an AI character that helps employees draft internal emails, Slack messages, and internal wiki documentation in their own professional voice and style.

## Data Sources

- Emails & Slack Messages
- Documents (Privacy / Code of Conduct)

## Core Features

### 1. Text → Professional Message

Transform rough drafts into polished professional messages.

**Example:**
- User input: "tell team meeting moved to 3pm"
- AI output: "Hi team, just a quick update that today's meeting has been moved to 3 PM…"

### 2. Tone Control

Select the tone for your message:
- Formal → "Please be informed that…"
- Friendly → "Just a quick heads-up…"
- Direct

Same message, different tone to match your style.

### 3. Message Type Selection

Choose the platform for your message:
- Email
- Slack message
- Announcement

### 4. Rewrite / Improve Feature

Refine your messages for better clarity and professionalism.

**Example:**
- User input: "send me report asap"
- AI output: "Hi, could you please send me the report as soon as possible? Thank you."

### 5. Templates / Quick Actions

Pre-built buttons for common communication needs:
- "Write a reminder"
- "Write an apology"
- "Write a follow-up"

## User Interface

- **Main text box:** For the user's rough draft
- **Platform dropdown:** Select email, Slack, or announcement
- **Tone dropdown:** Choose formal, friendly, or direct

## System Architecture

![IC-LLM-Twin System Architecture](IC-LLM-Twin%20System%20Architecture.png)

### Architecture Components

**Data Collection Pipeline**
- Collects data from multiple sources: Emails, Slack Messages, and Company Documents
- Processes through ETL pipeline into a NoSQL database

**Feature Pipeline**
- Cleans and formats collected data with embeddings for RAG finetuning
- Maintains a Logical Feature Store containing:
  - Instruct Dataset
  - Vector Database
  - Retrieval Client

**Training Pipeline**
- Experiment Tracking and LLM Finetuning (iterative process)
- Tests LLM candidates
- Produces LLM production candidates stored in Model Registry

**Inference Pipeline**
- Deploys the trained model
- Runs LLM Twin engine with Prompt Monitoring
- Generates professional messages
- Exposes REST API for user interactions
- Retrieves policy context through RAG for informed responses

## Why This System?

The Internal Communication Twin empowers employees to write faster and more consistently by automating brand-aligned messaging while utilizing company data in a morally sound, secure way that respects user privacy.


# Tooling architecture mapping
- **MongoDB (NoSQL)**: MongoDB will act as foundational data lake. It will store the raw, unprocessed text and metadata extracted from our data sources. Specifially, this includes your raw internal **Emails**, **Slack messages**, and the raw text of **Company Documents** (like Privacy Policies and Codes of Conduct) before they undergo cleaning, formatting, or embedding.

- **Qdrant (Vector DB)**: This will play role in feature pipeline, Qdrant will store the mathematical vectore embeddings of knowledge base. Specifically, this will be our *Company Documents* (Privacy Policies, Code of Conduct, and internal wiki documentation). Storing those data in Qdrant allows our *inference Pipeline* to use RAG to instantly fetch relevant company rules and context, ensuring the AI's generated messages are compiliant with company policies.

- **ZenML**: Will act as the central orchestrator[Central orchestrator = the main system that executes and coordinates all your pipeline steps in one place] (the 'glue') for your entire MLOps lifecycle. We will use it to defin the automate the flow between your isolated architecture components: triggering the data extraction into MongoDB, kicking off the feature pipeline to generate Qrant embeddings, running the training jobs, and finally deploying the model to the inference pipeline. it ensures our steps are reproducible and trackable ffrom end to end. 

- **Comet ML**: Comet ML
When you are training the model on your Slack messages, what are you using this for?
In your Training Pipeline, Comet ML acts as your Experiment Tracking and Model Registry platform. When fine-tuning your LLM to learn users' Slack tone, you will use Comet to track hyperparameters (like learning rate), log training/validation loss, compare different LLM candidate iterations side-by-side, and save the final "production candidate" models so you always know exactly which version of the model is deployed.

- **Opik**: Once your Slack-drafting bot is live, what will this tool be monitoring?
In your Inference Pipeline, Opik handles the Prompt Monitoring component. Once your bot is live, it will monitor the exact prompt inputs (the user's rough drafts) and the LLM's outputs (the polished messages). It will track metrics like response latency, user feedback (if they regenerate or accept the draft), tone consistency, and safety guardrails (ensuring the bot doesn't generate inappropriate content or violate the retrieved policy context).


