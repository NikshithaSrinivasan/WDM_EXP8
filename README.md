### EX8 Web Scraping On E-commerce platform using BeautifulSoup
### DATE: 17-03-2026
### AIM: To perform Web Scraping on Amazon using (beautifulsoup) Python.
### Description: 
<div align = "justify">
Web scraping is the process of extracting data from various websites and parsing it. In other words, it’s a technique 
to extract unstructured data and store that data either in a local file or in a database. 
There are many ways to collect data that involve a huge amount of hard work and consume a lot of time. Web scraping can save programmers many hours. Beautiful Soup is a Python web scraping library that allows us to parse and scrape HTML and XML pages. 
One can search, navigate, and modify data using a parser. It’s versatile and saves a lot of time.
<p>The basic steps involved in web scraping are:
<p>1) Loading the document (HTML content)
<p>2) Parsing the document
<p>3) Extraction
<p>4) Transformation

### Procedure:

1) Import necessary libraries (requests, BeautifulSoup, re, matplotlib.pyplot).
2) Define convert_price_to_float(price) Function: to Remove non-numeric characters from a price string and convert it to a float.
3) Define get_amazon_products(search_query) Function: to Scrape Amazon for product information based on the search query.
4) Fetch and parse the HTML content then Extract product names and prices from the search results and Sort product information based on converted prices in ascending order.
5) Return sorted product data as a list of dictionaries.
6) Call get_amazon_products(search_query) to get product data based on the user's search query.
7) Check if products are found; if not, display "No products found."
8) Visualize Product Data using a Bar Chart

### Program:
```
import requests
from bs4 import BeautifulSoup
import re
import matplotlib.pyplot as plt

def convert_price_to_float(price):
    price = re.sub(r'[^\d.]', '', price)
    return float(price)

def get_products(search_query):
    url = "https://books.toscrape.com/"
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')

    products_data = []

    books = soup.find_all('article', class_='product_pod')
    for book in books:
        name = book.h3.a['title']
        price = book.find('p', class_='price_color').text
        availability = book.find('p', class_='instock availability').text.strip()

 
        if search_query.lower() in name.lower():
            products_data.append({
                'Product': name,
                'Price': price,
                'Availability': availability
            })

    return sorted(products_data, key=lambda x: convert_price_to_float(x['Price']))


search_query = input("Enter product to search: ")

products = get_products(search_query)


if products:
    print("\nSearch Results:\n")
    for i, product in enumerate(products, start=1):
        print(f"{i}. Product Name : {product['Product']}")
        print(f"   Price        : {product['Price']}")
        print(f"   Availability : {product['Availability']}")
        print("-" * 50)

    
    product_names = [
        p['Product'][:30] if len(p['Product']) > 30 else p['Product']
        for p in products
    ]
    product_prices = [convert_price_to_float(p['Price']) for p in products]

    plt.figure(figsize=(10, 6))
    plt.barh(product_names, product_prices)
    plt.xlabel("Price")
    plt.ylabel("Product")
    plt.title(f"Search Results for '{search_query}' (Ascending Order)")
    plt.tight_layout()
    plt.show()
else:
    print("No products found.")
```
### Output:


### Result:
Thus,Web scrapping has been executed sucessfully.
