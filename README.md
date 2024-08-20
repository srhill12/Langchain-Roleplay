# README

## Project Overview

This project demonstrates the use of OpenAI's GPT-3.5 model with `langchain` to perform a variety of tasks including role-based conversation generation, instruction following using templates, and creating examples for few-shot learning. The project highlights how to configure and utilize the model to respond to user queries based on predefined roles, templates, and examples.

## Key Components

### 1. **Model and API Key Setup**
   - The project uses a `.env` file to securely store the OpenAI API key. The model used is `"gpt-3.5-turbo"`, which is configured using the environment variable `OPENAI_API_KEY`.

### 2. **Roles**
   - This section demonstrates how to define roles for the model by using system and human messages. The model is set up to act as an athletic trainer and provides workout recommendations based on the role it is assigned.

### 3. **Templates for Instructions**
   - The project shows how to use `PromptTemplate` and `LLMChain` from `langchain` to create a template-based interaction where the model acts as a history tutor. It answers questions within the scope of history and politely declines to answer unrelated queries.

### 4. **Templates for Examples**
   - This section introduces few-shot learning by providing the model with examples of how to generate rhyming sentences based on a given word. The model uses a `FewShotPromptTemplate` to learn from the examples and produce new outputs based on similar input queries.

## Installation

### Prerequisites
- Python 3.7 or higher
- Pip package manager

### Required Python Packages
Install the required packages using pip:

```bash
pip install langchain langchain_openai python-dotenv
```

### .env File Setup
Create a `.env` file in the root directory of the project. This file should contain your OpenAI API key:

```
OPENAI_API_KEY=your_openai_api_key_here
```

Replace `your_openai_api_key_here` with your actual API key from OpenAI.

## Usage

### 1. Roles
To run the role-based conversation:

```python
from langchain_openai import ChatOpenAI
from langchain.schema import HumanMessage, SystemMessage

# Initialize the model
llm = ChatOpenAI(openai_api_key=OPENAI_API_KEY, model_name="gpt-3.5-turbo", temperature=0.3)

# Create messages
messages = [
    SystemMessage(content="You are an athletic trainer."),
    HumanMessage(content="Provide me with a summary of what to do this week for my workouts.")
]

# Get the result
result = llm.invoke(messages)
print(result.content)
```

### 2. Templates for Instructions
To run the instruction-based interaction:

```python
from langchain import PromptTemplate, LLMChain

# Initialize the model
llm = ChatOpenAI(openai_api_key=OPENAI_API_KEY, model_name="gpt-3.5-turbo", temperature=0.3)

# Create a prompt template
format = """
You are a history tutor. Answer only questions that would be covered in a history course.
If the human asks questions not related to history, remind them that your job is to help
them learn history, and ask them for a question on that topic. If they ask a question which
there is not enough information to answer, tell them you don't know and don't make up an 
answer.

Question: {query}

Answer: 
"""

prompt_template = PromptTemplate(input_variables=["query"], template=format)
chain = LLMChain(llm=llm, prompt=prompt_template)

# Run the chain
query = {"query": "Why were the 1980 summer Olympics boycotted?"}
result = chain.invoke(query)
print(result["text"])
```

### 3. Templates for Examples
To run the few-shot learning interaction:

```python
from langchain import PromptTemplate, FewShotPromptTemplate, LLMChain

# Initialize the model
llm = ChatOpenAI(openai_api_key=OPENAI_API_KEY, model_name="gpt-3.5-turbo", temperature=0.3)

# Define the examples
prefix = """
Here are examples between a human and AI. The human provides a word, and
the AI provides a single sentence with easy to read words that mostly rhyme
with the word the human provided. The sentence does not have to include the 
original word. For example:
"""

examples = [
    {"query": "rat", "answer": "The cat sat next to the bat."},
    {"query": "frog", "answer": "A dog hops a log in the bog."},
    {"query": "ten", "answer": "Ben sent ten hens to the glen."}
]

example_format = """
Human: {query}
AI: {answer}
"""

example_template = PromptTemplate(input_variables=["query", "answer"], template=example_format)
suffix = """
Human: {query}
AI: 
"""

prompt_template = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_template,
    input_variables=["query"],
    prefix=prefix,
    suffix=suffix,
    example_separator="\n\n"
)
chain = LLMChain(llm=llm, prompt=prompt_template)

# Run the chain
query = "grime"
result = chain.run(query)
print(result)
```

## Improvements

1. **Enhanced Role Management:** Implement more sophisticated role management, allowing the model to switch roles dynamically based on user input or context.

2. **Template Expansion:** Introduce additional templates for different subject areas or tasks, making the system more versatile.

3. **Interactive UI:** Develop a web-based user interface using Flask or Django to interact with the model in a more user-friendly manner.

4. **Error Handling:** Implement more robust error handling to manage unexpected inputs or API failures gracefully.

## License

This project is licensed under the MIT License.

