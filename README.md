# Python-Web-Scrapping-Project
Helios Watch Store Web Scrapping

---

### **1. Importing Required Libraries**
```python
from bs4 import BeautifulSoup
import requests
import pandas as pd
import numpy as np
```
- **BeautifulSoup**: Parses HTML and extracts data from web pages.
- **Requests**: Sends HTTP requests to fetch web page content.
- **Pandas**: Stores extracted data in structured format (CSV, DataFrame).
- **NumPy**: Handles missing values efficiently.

---

### **2. Defining Functions to Extract Product Details**
These functions parse the webpage to extract relevant details:

#### **Extracting Product Title**
```python
def get_Title(soup):
    try:
        Title = soup.find("h3").text.strip()
    except AttributeError:
        Title = "N/A"
    return Title
```
- Finds the `<h3>` tag (which contains the product title).
- Returns `"N/A"` if the tag is missing.

#### **Extracting Discounted Price**
```python
def get_Price(soup):
    try:
        Price = soup.find("span", {'class': 'special-price sp-custom check-3'}).text.strip()
    except AttributeError:
        Price = "N/A"
    return Price
```
- Looks for a `<span>` tag with the class `"special-price sp-custom check-3"`, which contains the discounted price.

#### **Extracting Product Model**
```python
def get_Product_Model(soup):
    try:
        Product_Model = soup.find("div", {'class': 'product attribute sku'}).text.strip()
    except AttributeError:
        Product_Model = "N/A"
    return Product_Model
```
- Locates the `<div>` tag with class `"product attribute sku"` to get the product model.

#### **Extracting Product Tags**
```python
def get_Product_Tag(soup):
    try:
        Product_Tag = soup.find("div", {'class': 'product_tags'}).text.strip()
    except AttributeError:
        Product_Tag = "N/A"
    return Product_Tag
```
- Looks for the `"product_tags"` class to extract product tags.

---

### **3. Sending HTTP Request**
```python
if __name__ == '__main__':
    # Add your user agent
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36"
    }
```
- The script starts execution.
- **User-Agent** helps avoid getting blocked by the website.

---

### **4. Fetching the Webpage**
```python
url = "https://www.helioswatchstore.com/mens-watches"
```
- URL of the website to scrape.

```python
webpage = requests.get(url, headers=headers)
```
- Sends an HTTP GET request to fetch webpage content.

```python
soup = BeautifulSoup(webpage.content, "html.parser")
```
- Parses the webpage using BeautifulSoup.

---

### **5. Extracting Product Links**
```python
links = soup.find_all("a", {'class': 'product photo product-item-photo'})
```
- Finds all anchor (`<a>`) tags that contain product links.

```python
links_list = []
for link in links:
    links_list.append(link.get('href'))
```
- Extracts `href` (product URLs) and stores them in `links_list`.

---

### **6. Extracting Product Details**
```python
d = {"Title":[], "Price":[], "Product_Model":[], "Product_Tag":[]}
```
- Initializes an empty dictionary to store scraped data.

```python
for link in links_list:
    new_webpage = requests.get(link, headers=headers)
    new_soup = BeautifulSoup(new_webpage.content, "html.parser")

    # Function calls to extract product details
    d['Title'].append(get_Title(new_soup))
    d['Price'].append(get_Price(new_soup))
    d['Product_Model'].append(get_Product_Model(new_soup))
    d['Product_Tag'].append(get_Product_Tag(new_soup))
```
- Loops through product links.
- Sends an HTTP request to each product page.
- Extracts **Title, Price, Product Model, and Tags** using predefined functions.

---

### **7. Storing Data in a CSV File**
```python
Watch_df = pd.DataFrame.from_dict(d)
Watch_df['Title'].replace('', np.nan, inplace=True)
Watch_df = Watch_df.dropna(subset=['Title'])
Watch_df.to_csv("Watch_data.csv", header=True, index=False)
```
- Converts dictionary `d` into a **Pandas DataFrame**.
- Replaces empty values with `NaN`.
- Removes rows where `Title` is missing.
- Saves data as **"Watch_data.csv"**.

---

### **8. Displaying Extracted Data**
```python
Watch_df
```
- Displays the DataFrame.

---

## **Summary**
This project:
1. **Sends an HTTP request** to fetch the men's watch collection from Helios.
2. **Extracts all product links** from the main page.
3. **Scrapes each product page** to collect:
   - **Title**
   - **Price**
   - **Product Model**
   - **Tags**
4. **Stores the extracted data** in a CSV file.
---
**Thank You!**
