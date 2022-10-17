# Extract Keyword from a Website using Python

```python
"""
Run this command in Terminal after activating environment:
python -c "import nltk;nltk.download('stopwords')"
python -c "import nltk;nltk.download('punkt')"
"""

from bs4 import BeautifulSoup
import requests
from rake_nltk import Rake

rake = Rake()  # ML library to extract keywords from text

url_to_scrape = "https://html_programmer.com"


def get_text(url):
    # get the html content
    html_content = requests.get(url).text
    # parse the html content
    soup = BeautifulSoup(html_content, "html.parser")
    return soup.get_text()


website_text = get_text(url_to_scrape)
rake.extract_keywords_from_text(website_text)
keywords = rake.get_ranked_phrases_with_scores()

for score, keyword in keywords:
    print(f"{score} -> {keyword}")
```