# Assignment

## Brief

Write the Python codes for the following questions.

## Instructions

Paste the answer as Python in the answer code section below each question.

### Question 1

Question: The scraping of `https://www.scrapethissite.com/pages/forms/` in the last section assumes a hardcoded (fixed) no of pages. Can you improve the code by removing the hardcoded no of pages and instead use the `»` button to determine if there are more pages to scrape? Hint: Use a `while` loop.

```python
def parse_and_extract_rows(soup: BeautifulSoup):
    """
    Extract table rows from the parsed HTML.

    Args:
        soup: The parsed HTML.

    Returns:
        An iterator of dictionaries with the data from the current page.
    """
    header = soup.find('tr')
    headers = [th.text.strip() for th in header.find_all('th')]
    teams = soup.find_all('tr', 'team')
    for team in teams:
        row_dict = {}
        for header, col in zip(headers, team.find_all('td')):
            row_dict[header] = col.text.strip()
        yield row_dict
```

Answer:

```python
def parse_and_extract_rows(soup: BeautifulSoup):
   
    header = soup.find('tr')
    headers = [th.text.strip() for th in header.find_all('th')]

    rows = []
    teams = soup.find_all('tr', class_='team')
    for team in teams:
        row_dict = {}
        for header, col in zip(headers, team.find_all('td')):
            row_dict[header] = col.text.strip()
        rows.append(row_dict)

    return rows


def scrape_all_pages(start_url: str):
    """
    Scrape all pages starting from the given URL, following the 'Next' button.

    Args:
        start_url: The URL of the first page.

    Yields:
        Dictionaries for each row across all pages.
    """
    url = start_url
    while url:
        response = requests.get(url)
        soup = BeautifulSoup(response.text, "html.parser")

        # Yield rows from current page
        for row in parse_and_extract_rows(soup):
            yield row

        # Find the "Next" button
        next_btn = soup.find("a", {"aria-label": "Next"})
        if next_btn and "href" in next_btn.attrs:
            # Handle relative links safely
            url = urljoin(url, next_btn["href"])
        else:
            url = None


# Example usage:
if __name__ == "__main__":
    start_url = "https://www.scrapethissite.com/pages/forms/"
    for row in scrape_all_pages(start_url):
        print(row)

```

## Submission

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.
