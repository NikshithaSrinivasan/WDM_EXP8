### EX8 Web Scraping On E-commerce platform using BeautifulSoup
### DATE: 17/03/2026
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
    """Convert price string like '₹1,299.00' → 1299.0"""
    price = re.sub(r'[^\d.]', '', price)
    parts = price.split('.')
    if len(parts) > 1:
        price = parts[0] + '.' + ''.join(parts[1:])
    return float(price) if price else 0.0

def get_amazon_products(search_query):
    base_url = 'https://www.amazon.in'
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
                      "AppleWebKit/537.36 (KHTML, like Gecko) "
                      "Chrome/114.0.0.0 Safari/537.36",
        "Accept-Language": "en-IN,en;q=0.9",
        "Referer": "https://www.google.com/",
        "Connection": "keep-alive"
    }

    search_query = search_query.replace(' ', '+')
    url = f'{base_url}/s?k={search_query}'

    session = requests.Session()
    response = session.get(url, headers=headers)
    products_data = []

    if response.status_code == 200:
        soup = BeautifulSoup(response.content, 'html.parser')
        product_elements = soup.select('div.s-main-slot div[data-component-type="s-search-result"]')

        for product in product_elements:
            # Product title
            title_elem = product.select_one('h2 span')
            title = title_elem.text.strip() if title_elem else 'No Title'

            # Current price
            price_container = product.select_one('span.a-price')
            if price_container:
                price_whole = price_container.select_one('span.a-price-whole')
                price_fraction = price_container.select_one('span.a-price-fraction')
                if price_whole and price_fraction:
                    price = f"{price_whole.text.strip()}.{price_fraction.text.strip()}"
                elif price_whole:
                    price = price_whole.text.strip()
                else:
                    price = '0'
            else:
                price = '0'

            # List price / Original price for discount calculation
            list_price_elem = product.select_one('span.a-price.a-text-price span.a-offscreen')
            list_price = convert_price_to_float(list_price_elem.text) if list_price_elem else convert_price_to_float(price)

            current_price = convert_price_to_float(price)
            # Discount percentage
            discount = round(((list_price - current_price) / list_price) * 100, 2) if list_price > current_price else 0.0

            products_data.append({
                'Product': title,
                'Price': current_price,
                'Discount (%)': discount
            })

            print(f'Product: {title}\nPrice: ₹{current_price}\nDiscount: {discount}%\n---')

    else:
        print(f'Failed to fetch Amazon page: {response.status_code}')

    # Sort by price ascending
    return sorted(products_data, key=lambda x: x['Price'])

# Main
search_query = input('Enter product to search on Amazon: ')
products = get_amazon_products(search_query)

# Plotting
if products:
    product_names = [p['Product'][:30] + '...' if len(p['Product']) > 30 else p['Product'] for p in products]
    product_prices = [p['Price'] for p in products]
    product_discounts = [p['Discount (%)'] for p in products]

    plt.figure(figsize=(12, 8))
    bars = plt.barh(range(len(product_prices)), product_prices, color='skyblue')
    plt.xlabel('Price (₹)')
    plt.ylabel('Product')
    plt.title(f'Products, Prices & Discounts on Amazon for "{search_query}"')
    plt.yticks(range(len(product_prices)), [f"{n} ({d}%)" for n, d in zip(product_names, product_discounts)])
    plt.tight_layout()
    plt.show()
else:
    print('No products found.')

```

### Output:

```
Enter product to search on Amazon: refrigerator
Samsung 183 L, 5 Star, Digital Inverter, Direct-Cool Single Door Refrigerator (RR20D2825HV/NL, Himalaya Poppy Blue, Base Stand Drawer)
Price: 16990.0
Discount: 26.13 %
------
Whirlpool 184 L 2 Star Direct-Cool Single Door Refrigerator (205 WDE CLS 2S SAPPHIRE BLUE-Y, Blue, 2026 Model)
Price: 11990.0
Discount: 32.45 %
------
Samsung 236 L, 2 Star, Digital Inverter, Frost Free Double Door Refrigerator (RT40H28W2QHL, Gray Silver, 2026 Model)
Price: 24990.0
Discount: 21.9 %
------
Whirlpool 192 L 4 Star Icemagic Powercool Direct-Cool Single Door Refrigerator with Base Drawer (215 IMPC ROY 4S SAPPHIRE PEONY-Y, Blue, 2026 Model)
Price: 15490.0
Discount: 28.78 %
------
Whirlpool 192 L 3 Star Vitamagic PRO Frost Free Direct-Cool Single Door Refrigerator (215 VMPRO PRM 3S RADIANT STEEL-Y, Silver, Auto Defrost Technology, 2026 Model)
Price: 15490.0
Discount: 26.06 %
------
Haier 205L 5Star Direct Cool Single Door Refrigerator | Inverter Compressor | Wide Freezer Space | Bigger Ice Tray | Longer Cooling Retention | Easy Clean Back (HED-215MRB-N, Marine Rose, Base Stand)
Price: 16990.0
Discount: 38.64 %
------
Samsung 223 L, 3 Star, Digital Inverter, Direct-Cool Single Door Refrigerator (RR24H2723RZ/NL, Midnight Blossom Red, Single Touch Defrost, 2026 Model)
Price: 18690.0
Discount: 25.24 %
------
Samsung 183 L, 4 Star, Digital Inverter, Direct-Cool Single Door Refrigerator (RR20C1824HV/HL, Himalaya poppy Blue, Base Stand Drawer)
Price: 16890.0
Discount: 23.22 %
------
Samsung 236 L, 3 Star, Convertible, Digital Inverter with Display Frost Free Double Door Refrigerator (RT28C3733S8/HL, Silver, Elegant Inox)
Price: 25490.0
Discount: 37.81 %
------
Samsung 183 L, 4 Star, Digital Inverter, Direct-Cool Single Door Refrigerator (RR20H28249U/NL, Paradise Bloom Blue, Base Stand Drawer, Single Touch Defrost, 2026 Model)
Price: 16990.0
Discount: 22.77 %
------
Godrej 180L 1Star Advanced Capillary Technology| Jumbo Vegetable Tray| Wired Shelves| 2.25L Bottle Space| Wide Shelf Space| Direct Cool Single Door Refrigerator (RD EDGE 205AN WRF PP BL, Pep Blue)
Price: 11970.0
Discount: 30.37 %
------
Samsung 236 L, 3 Star, Digital Inverter, Frost Free Double Door Refrigerator (RT28C3053S8/HL, Silver, Elegant Inox)
Price: 24990.0
Discount: 34.22 %
------
Whirlpool 207 L 5 Star Icemagic Pro Inverter Direct-Cool Single Door Refrigerator (230 IMPRO ROY 5S INV SAPPHIRE PEONY-Y, Blue, 2026 Model)
Price: 18490.0
Discount: 32.76 %
------
Godrej 183 L 2 Star| Farm Fresh Crisper Technology| Turbo Cooling Technology | Toughened Glass Shelved | Jumbo Vegetable Tray | Direct Cool Single Door Refrigerator (RD R190BN THF BR BL, Berry Blue)
Price: 12990.0
Discount: 34.36 %
------
Whirlpool 184 L 2 Star Direct-Cool Single Door Refrigerator (205 WDE CLS 2S SHERRY WINE-Y, Red, 2026 Model)
Price: 11990.0
Discount: 32.45 %
------
Whirlpool 184 L 2 Star Direct-Cool Single Door Refrigerator (205 WDE PRM 2S SAPPHIRE SERENA-Z)
Price: 12990.0
Discount: 26.24 %
------
```

<img width="1044" height="433" alt="Screenshot 2026-03-14 132827" src="https://github.com/user-attachments/assets/687789de-3266-4a92-8d9e-6a6a26d8f876" />


### Result:
 To perform Web Scraping on Amazon using (beautifulsoup) Python using implemented successfully.
