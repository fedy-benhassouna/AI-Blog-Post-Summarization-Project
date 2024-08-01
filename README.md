# AI Blog Post Summarization with Hugging Face Transformers & Beautiful Soup

## Project Overview
This project demonstrates how to use Hugging Face's Transformers library and Beautiful Soup to scrape and summarize blog posts. The summarization pipeline uses a pre-trained model to condense long articles into concise summaries.

## Author
Fedy Ben Hassouna

## Setup Instructions

### 1. Install Dependencies
Make sure you have the necessary libraries installed. You can install them using pip:
```bash
pip install transformers beautifulsoup4 requests
```

### 2. Load Summarization Pipeline
We use the `sshleifer/distilbart-cnn-12-6` model for summarization. Load the pipeline with the following code:
```python
from transformers import pipeline

model = "sshleifer/distilbart-cnn-12-6"
Summarizer = pipeline(task="summarization", model=model)
```

### 3. Web Scraping Blog Post
We use Beautiful Soup to scrape the content of a blog post. For example, to scrape a post from HackerNoon:
```python
from bs4 import BeautifulSoup
import requests

URL = "https://hackernoon.com/school-is-dead-embrace-modern-education-instead"
r = requests.get(URL)
soup = BeautifulSoup(r.text, 'html.parser')
results = soup.find_all(['h1', 'p'])

text = [result.text for result in results]
ARTICLE = ' '.join(text)
```

### 4. Chunk Text
To summarize long articles, we chunk the text into smaller parts:
```python
max_chunk = 500
current_chunk = 0
chunks = []

sentences = ARTICLE.split('. ')

for sentence in sentences:
    if len(chunks) == current_chunk + 1:
        if len(chunks[current_chunk]) + len(sentence.split(' ')) <= max_chunk:
            chunks[current_chunk].extend(sentence.split(' '))
        else:
            current_chunk += 1
            chunks.append(sentence.split(' '))
    else:
        chunks.append(sentence.split(' '))

for chunk_id in range(len(chunks)):
    chunks[chunk_id] = ' '.join(chunks[chunk_id])
```

### 5. Summarize Text
Use the summarization pipeline to generate the summary for each chunk:
```python
results = Summarizer(chunks, max_length=120, min_length=30, do_sample=False)
summary_text = ' '.join([res['summary_text'] for res in results])
```

### 6. Save Summary to File
Write the summarized text to a file:
```python
with open('blogsummary.txt', 'w') as f:
    f.write(summary_text)
```

## Notes
- Ensure that the Hugging Face model is properly authenticated if required. You may need an HF_TOKEN for certain models.
- The `do_sample` parameter in the `Summarizer` function controls whether sampling is used for generating summaries. Setting it to `False` ensures deterministic outputs.

## License
This project is open-source and free to use under the MIT license.

## Acknowledgments
- Hugging Face for the Transformers library
- Beautiful Soup for web scraping utilities
- HackerNoon for the example blog post used in this project

For any questions or issues, feel free to contact[fedy.benhassouna@insat.ucar.tn].
