# **Part B: RAG Answer Quality & Customer Safety

## **1. System Prompt**

**System Prompt (for the AI assistant):**

"You are an AI assistant that answers user questions **using only the active customer Knowledge Base**. Follow these rules:

- Only use information that is fully or partially present in the current active Knowledge Base for this customer.
- Clearly distinguish between information that is *fully present*, *partially present*, or *absent* in the Knowledge Base.
- If the answer is **fully present** in the KB, provide the information and note that it is fully supported by the KB.
- If the answer is **partially present**, explain what is known from the KB and what is missing or uncertain.
- If the question cannot be answered with the KB (information is **absent**), explicitly say so and do not guess or hallucinate.
- Do not use any outside knowledge or guess answers. Avoid speculation entirely.
- When referencing source documents, use a minimal format (e.g. “Based on *Onboarding Guide – 2024*,” “According to *API Reference 2023*,” etc.) to indicate where the information is found."

This prompt enforces that the model sticks strictly to the Knowledge Base. It ensures the model explicitly handles *fully present*, *partially present*, and *absent* information, and it forbids guessing. The references are minimized to the document name (italicized here) to ground the answer in the KB.

## **2. Example User Questions**

1. **(Complete)** *“What are the standard customer support hours?”*
2. **(Partial)** *“Does ProductX support multi-factor authentication?”*
3. **(None)** *“What is our company’s market share?”*

Each question is designed to test the model’s handling of different coverage levels in the KB.

## **3. Expected Model Answers**

1. **Question:** “What are the standard customer support hours?” (Answer fully in KB)
    
    **Assistant:** “Based on *Customer Handbook – 2024*, our customer support is available **Monday through Friday from 9:00 AM to 5:00 PM**. This information is fully present in the Knowledge Base document. All requested details about support hours can be directly found in the Customer Handbook.”
    
2. **Question:** “Does ProductX support multi-factor authentication?” (Answer partially in KB)
    
    **Assistant:** “I found general information on security in *ProductX Security Guide – 2024*, but the document does **not explicitly mention multi-factor authentication (MFA)**. The KB covers password reset and login security procedures, but it does not confirm whether MFA is supported or how it works. So, based on the KB, I can say that multi-factor authentication is **not detailed** in our available documents, and I cannot fully answer whether ProductX supports it without further information.”
    
3. **Question:** “What is our company’s market share?” (No information in KB)
    
    **Assistant:** “I did not find any information on our company’s market share in the active Knowledge Base. The documents focus on product details and support, and there is **no data on market share**. Therefore, I cannot provide an answer from the KB. I’m sorry, but that information is not available in our current Knowledge Base.”
    

Each answer above shows how the assistant should behave:

- In Answer 1, the assistant cites the source document (*Customer Handbook – 2024*) and confirms the information is fully present.
- In Answer 2, the assistant notes the partial coverage (security guide exists but no MFA mention) and clearly states what is missing.
- In Answer 3, the assistant admits absence of information and refuses to guess, aligning with the prompt’s rules.

## **4. Explanation of Prompting Approach**

- **Improves Reliability:** The system prompt forces the model to only use verified KB content. This grounding ensures answers are factual and relevant. By distinguishing *fully/partially/absent*, the assistant is transparent about confidence and completeness of the answer. This dramatically reduces the chance of hallucinations or fabricating details.
- **Prevents Mixing/Confusion:** Since each customer has a separate KB, this prompt prevents the model from accidentally mixing information across customers. It confines the model to “the active customer’s” data. This avoids cross-customer contamination or using general world knowledge.
- **Prevents Common Failures:** Typical failures include giving answers outside the KB or hallucinating facts. By explicitly telling the model to say “no information” when absent, it stops it from making up an answer. It also avoids speculation on partially covered topics by indicating uncertainty.
- **Future Improvements:** We could further structure this approach by requiring answers in a consistent format (e.g. JSON or labeled fields) to support automation. We might add a step for the model to list citations or reasoning steps, making it even more transparent. Over time, refining prompts or adding validation checks (e.g. follow-up queries to the KB) could improve accuracy and enable automated answer validation.