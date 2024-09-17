
# Diets-RAG

## Problem Description

In today’s world, an increasing number of people are seeking personalized diets to improve their health and achieve specific goals, such as weight loss, better metabolic function, or disease management. However, with so many different diets available—ranging from Keto to Vegan, Mediterranean, Paleo, and more—it can be difficult for individuals to choose a diet that best suits their lifestyle, health needs, and preferences. This often leads to confusion, incorrect dietary choices, or even the abandonment of dieting altogether.

To solve this problem, **Diets-RAG (Retriever-Augmented Generation)** aims to provide users with a system that can answer questions related to different diets and help guide them in choosing the most suitable one. The system is designed to serve as a personalized diet assistant by utilizing a Q&A database, initially focused on the Keto diet, with plans to expand to other diets in the future.

## Dataset and Approach

The current dataset is sourced from frequently asked questions about the Keto diet from trusted online resources. As the project evolves, the dataset will also incorporate information from the **most recent research papers** and studies on various diets. This ensures that the information provided is up-to-date and based on the latest scientific findings, offering users reliable and evidence-based guidance.

Users can input questions, and the system retrieves the most relevant answers, providing clear, concise, and accurate information. As the database expands to include other diets, the goal is to provide a **comprehensive, user-friendly tool** to help individuals make informed dietary choices based on recent research.

## Current Dataset Sources

- [20 Most Asked Questions About Keto Diet](https://www.linkedin.com/pulse/20-most-asked-questions-keto-diet-certified-operation-s-management-/)
- [Everyday Health - Burning Questions About Keto Diet Answered](https://www.everydayhealth.com/ketogenic-diet/diet/burning-questions-about-keto-diet-answered/)
- [Ruled.me - Ketogenic Diet FAQ](https://www.ruled.me/ketogenic-diet-faq/#standard_keto)
- [Diabetes.co.uk - Ketogenic Diet FAQs](https://www.diabetes.co.uk/keto/ketogenic-diet-faqs.html)

## Technologies

- **OpenAI** as the LLM
- **PostgreSQL** to save questions, answers, and user feedback
- **Elasticsearch** to retrieve data


## Preparation

1. Create a `diets-rag-app/.env` file and add the following configurations, including your OpenAI API key:

    ```bash
    # PostgreSQL Configuration
    POSTGRES_HOST=postgres
    POSTGRES_DB=course_assistant
    POSTGRES_USER=your_username
    POSTGRES_PASSWORD=your_password
    POSTGRES_PORT=5432
    
    # Elasticsearch Configuration
    ELASTIC_URL_LOCAL=http://localhost:9200
    ELASTIC_URL=http://elasticsearch:9200
    ELASTIC_PORT=9200
    
    # Streamlit Configuration
    STREAMLIT_PORT=8501
    
    # Other Configuration
    MODEL_NAME=multi-qa-MiniLM-L6-cos-v1
    INDEX_NAME=diets-questions
    OPENAI_API_KEY=your-openai-api-key-here
    ```

2. To run and initialize PostgreSQL, Elasticsearch, and Streamlit, use the following commands:

    ```bash
    docker-compose up
    ```

3. If you are running PostgreSQL locally, export the host as follows:

    ```bash
    export POSTGRES_HOST="127.0.0.1"
    ```

4. Install the required Python packages:

    ```bash
    pip install sentence-transformers elasticsearch python-dotenv psycopg2
    ```

5. Finally, initialize your setup by running the preparation script:

    ```bash
    python prep.py
    ```

## Experiments

Check the `rag_test.ipynb` file for detailed experiments.

### Retrieval Evaluation

Using **k-NN (k=5)** with vector search, the following results were obtained:

- **Vector search on "question_answer_vector"**:  
  - Hit rate: `0.81`
  - MRR (Mean Reciprocal Rank): `0.65`

- **Vector search on "answer_vector"**:  
  - Hit rate: `0.79`
  - MRR: `0.61`

- **Vector search on "question_vector"**:  
  - Hit rate: `0.64`
  - MRR: `0.48`

- **Minsearch result on question**:  
  - Hit rate: `0.76`
  - MRR: `0.59`

### Cosine Similarity Statistics for Original Answer and LLM Answer

- **Sample count**: `395`
- **Mean**: `0.78`
- **Standard Deviation**: `0.14`

### RAG Flow Evaluation

**LLM as a judge** using `prompt1_template`:

```plaintext
You are an expert evaluator for a Retrieval-Augmented Generation (RAG) system.
Your task is to analyze the relevance of the generated answer compared to the original answer provided.
Based on the relevance and similarity of the generated answer to the original answer, you will classify
it as "NON_RELEVANT", "PARTLY_RELEVANT", or "RELEVANT".

Here is the data for evaluation:

Original Answer: {answer_orig}
Generated Question: {question}
Generated Answer: {answer_llm}

Please analyze the content and context of the generated answer in relation to the original
answer and provide your evaluation in parsable JSON without using code blocks:

{
  "Relevance": "NON_RELEVANT" | "PARTLY_RELEVANT" | "RELEVANT",
  "Explanation": "[Provide a brief explanation for your evaluation]"
}
```

#### Relevance Results:

- **RELEVANT**: `299`
- **PARTLY_RELEVANT**: `96`

**For `prompt2_template`**:

```plaintext
You are an expert evaluator for a Retrieval-Augmented Generation (RAG) system.
Your task is to analyze the relevance of the generated answer to the given question.
Based on the relevance of the generated answer, you will classify it
as "NON_RELEVANT", "PARTLY_RELEVANT", or "RELEVANT".

Here is the data for evaluation:

Question: {question}
Generated Answer: {answer_llm}

Please analyze the content and context of the generated answer in relation to the question
and provide your evaluation in parsable JSON without using code blocks:

{
  "Relevance": "NON_RELEVANT" | "PARTLY_RELEVANT" | "RELEVANT",
  "Explanation": "[Provide a brief explanation for your evaluation]"
}
```

#### Relevance Results:

- **RELEVANT**: `382`
- **PARTLY_RELEVANT**: `13`

## Monitoring

The online evaluation is accessible through a **Streamlit** app. When running locally, you can access the app at:

```
http://127.0.0.1:8501
```

The evaluation results are saved in a **PostgreSQL** database.



