# PriceHunter

PriceHunter is a multi-agent system that autonomously hunts for online deals by analyzing product descriptions, estimating fair prices, and identifying significant discounts. It combines advanced LLMs with traditional ML techniques to deliver robust deal detection, price estimation, and user notifications via WhatsApp, alongside a browser-based UI built with Gradio.

---

## Core Components

- **Agent Framework:**  
  Coordinates specialized agents and manages the memory of deals.

- **Agents:**

  - **Scanner Agent:**  
    Scrapes public websites, uses GPT-4o to filter deals with clear descriptions and valid prices, and stores deals in a predefined JSON format.
  - **Specialist Agent:**  
    A fine-tuned Llama 3.1-8B model hosted serverlessly on AWS, responsible for generating price predictions.
  - **Ensemble Agent:**  
    Combines predictions from multiple models (fine-tuned Llama, GPT-4o with RAG, and RandomForest) using a linear regression approach to produce a refined price estimate.
  - **Planning Agent:**  
    Orchestrates the workflow between the various agents.
  - **Messaging Agent:**  
    Sends WhatsApp notifications when the estimated profit exceeds the threshold.

- **Vector Database:**  
  Utilizes a persistent ChromaDB for Retrieval Augmented Generation (RAG).

- **UI & Logs:**  
  Gradio provides a browser-based interface for viewing deals and system logs.

---

## Workflow

1. **Data Collection:**

   - The **Scanner Agent** scrapes public websites for new deals.
   - GPT-4o is used to filter out deals with clear descriptions and valid prices.
   - Deals are converted into a predefined JSON format.

2. **Price Estimation:**

   - A prompt is constructed from the scraped deal data.
   - The **Specialist Agent** (fine-tuned Llama) generates an initial price estimate.
   - The **Ensemble Agent** refines this estimate using a linear regression model combining outputs from:
     - Fine-tuned Llama
     - GPT-4o with RAG
     - RandomForest

3. **Decision Making:**

   - The system calculates the estimated profit as:  
     `Estimated Profit = Estimated Price - Scraped Price`
   - If the profit exceeds $50, the **Messaging Agent** sends a WhatsApp notification.

4. **Display:**
   - All deals and logs are displayed on a Gradio-based UI.

---

## Training & Deployment

- **Training:**
  - **Data:**  
    400K+ product descriptions with price labels (sourced from HuggingFace).
  - **Base Model:**  
    Llama 3.1-8B.
  - **Method:**  
    QLoRA fine-tuning.
  - **Infrastructure:**  
    Google Colab with an A100 GPU.
  - **Experiment Tracking:**  
    WandB.
- **Deployment:**
  - The fine-tuned Llama model endpoint is hosted serverlessly on an AWS EC2 instance via Modal.com.
  - ChromaDB is deployed locally for vector storage.
  - The Gradio UI runs locally to display deals and system logs.

---

## Architecture Diagram

```
[Gradio UI]◄───[DealAgentFramework]───►[ChromaDB]
               │           │
               ▼           ▼
       [Planning Agent]─┐ [Memory.json]
               │        │
               ▼        ▼
       [Scanner Agent]  [Ensemble Agent]───┬──►[Specialist Agent]───►[Serverless Endpoint]
       (GPT-4o)          (Linear Regression)├──►[Frontier Agent|RAG]────►[ChromaDB Search]
                                           └──►[Random Forest]
               │
               ▼
       [Messaging Agent]
```
## Demo
<img width="1919" alt="SCR-20250213-klzy" src="https://github.com/user-attachments/assets/cd085258-a46f-4ad1-9971-e9e516d6a6f6" />

