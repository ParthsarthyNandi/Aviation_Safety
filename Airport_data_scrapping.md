```python
"""
Written by Parthsarthy Nandi |
References -> 
    Scraping Data from a Real Website | Web Scraping in Python | Youtube Channel: Alex the Analyst |
        Link - https://www.youtube.com/watch?v=8dTpNajxaH0
    Chat-gpt - model 4o
"""

import requests
from bs4 import BeautifulSoup
import pandas as pd

# Base URL for the site
base_url = "https://asn.flightsafety.org"

# Start with the first page URL
start_url = "/asndb/year/2020/1"

# Headers to mimic a real browser request
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
}

# Function to scrape table from a single page
def scrape_table_from_page(url):
    full_url = base_url + url
    print(f"Scraping page: {full_url}")  # Debug URL being scraped
    response = requests.get(full_url, headers=headers)
    
    if response.status_code != 200:
        print(f"Failed to retrieve page {url}. Status code: {response.status_code}")
        return None, None
    
    soup = BeautifulSoup(response.content, 'html.parser')
    
    # Find the table by class name (replace with actual class if needed)
    # this is arbitary please refer to website go to inspect then the element and check the class name.
    table = soup.find('table', {'class': 'hp'}) 
    
    if table:
        rows = table.find_all('tr')
        table_data = []
        
        # Extract data from rows
        for row in rows:
            columns = row.find_all('td')
            columns = [col.text.strip() for col in columns]
            table_data.append(columns)
        
        # Find the "Next" pagination link
        next_link = soup.find('a', text='Next >')
        if next_link:
            next_page = next_link['href']
        else:
            next_page = None
        
        return table_data, next_page
    else:
        print(f"No table found on page {url}")
        return None, None

# Function to scrape all pages until pagination ends
def scrape_all_pages(start_url):
    all_data = []
    current_url = start_url
    
    while current_url:
        page_data, next_url = scrape_table_from_page(current_url)
        
        if page_data:
            all_data.extend(page_data)
        
        current_url = next_url  # Move to next page
    
    return all_data

# Scrape starting from the first page
data = scrape_all_pages(start_url)

# Convert to DataFrame for easy manipulation and saving
if data:
    df = pd.DataFrame(data, columns=['Incident Data', 'Aircraft Type', 'Registration', 'Operator', 'Fatalities', 'Location', 'column','Damage_Code', 'column'])  # Adjust column names as needed
    # Save to CSV
    df.to_csv('scraped_data2020.csv', index=False)
    print("Data saved to scraped_data2022.csv")
else:
    print("No data scraped.")

```


```python

```


```python

```
