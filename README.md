from bs4 import BeautifulSoup 
import requests 
import csv
import pandas as pd
import re
import sqlite3
descriptions = [] # Create a list to store the descriptions
processors=[]
ram=[]
os=[]
storage=[]
inches=[]
warranty=[]
prices = []
ratings = []
productlinks = []
lis = []
pages = list(range(1,20))
for page in pages:
  req = requests.get("https://www.trendyol.com/tablo-x-c1185?pi={}".format(page)).text  # URL of the website which you want to scrape
  #content = req.content # Get the content
  soup = BeautifulSoup(req,'html.parser')
  #print(soup.prettify())

  desc = soup.find_all('span' , class_='prdct-desc-cntnr-name hasRatings')
  for i in range(len(desc)):
      descriptions.append(desc[i].text)
  len(descriptions)

  rating = soup.find_all('span',class_='ratingCount') 
   #Extracting the ratings of each laptop from the website   
  for i in range(len(rating)):                      
    ratings.append(rating[i].text)
    len(ratings)
  # Extracting price of each laptop from the website
  price = soup.find_all('div',class_='prc-box-sllng prc-box-sllng-w-dscntd') 
  for i in range(len(price)):
    prices.append(price[i].text)
    len(prices)
  links = soup.findAll('div', class_='p-card-chldrn-cntnr')
  for link in links:
    p_link = link.find('a').get('href')
    base_url = 'https://www.trendyol.com'
    complete_link = base_url + p_link
    productlinks.append(complete_link)
    #The below code is responsible for downloading Images of each product
#   for eachlink in productlinks:
#     res = requests.get(eachlink).text
#     each_product_soup =  BeautifulSoup(res,'html.parser')
#     img_tags =  each_product_soup.find_all('img')

#     urls = [img['src'] for img in img_tags]


#     for url in urls:
#         filename = re.search(r'/([\w_-]+[.](jpg|gif|png))$', url)
#         if not filename:
#              print("Regex didn't match with the url: {}".format(url))
#              continue
#         with open(filename.group(1), 'wb') as f:
#             if 'http' not in url:
#                 # sometimes an image source can be relative 
#                 # if it is provide the base url which also happens 
#                 # to be the site variable atm. 
#                 url = '{}{}'.format(eachlink, url)
#             response = requests.get(url)
#             f.write(response.content)
            #Images download complete
            
            
            
#     details =  each_product_soup.find_all('li')
    
#     for ul in  details:
#         print(ul.text)
#         for li in ul.findAll('li'):
# #             if li.find('ul'):
# #                 break
#             lis.append(li)

#     for li in lis:
#         print (li.text)
# print(len(descriptions))
# print(descriptions)    
    
# print(ratings) 
# print(len(ratings)) 

# print(prices)


print('Total Product title' + '' + str(len(descriptions)) + '+ ''Total Ratings' + str(len(ratings)) + 'Total Prices' + str(len(prices)) )
# print(productlinks)
#   commonclass = soup.find_all('li',class_='tVe95H') # We observe that the classnames for the different specifications are under one div.So we need to apply some method to extract the different features.
#   # Create empty lists for the features
#   for i in range(0,len(commonclass)):
#     p=commonclass[i].text # Extracting the text from the tags
#     if("Core" in p): 
#         processors.append(p)
#     elif("RAM" in p): 
#         ram.append(p)
#     elif("HDD" in p or "SSD" in p):
#         storage.append(p)
#     elif("Operating" in p):
#         os.append(p)
#     elif("Display" in p):
#         inches.append(p)
#     elif("Warranty" in p):
#         warranty.append(p)


df = {'Description':descriptions[slice(504)],'Ratings':ratings[slice(504)],'Prices':prices[slice(504)]}
dataset = pd.DataFrame(data = df) # Finally merging all the features into a single dataframe
dataset
dataset.to_csv('Mydata.csv')
Total Product title1062+ Total Ratings1062Total Prices488
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
~\AppData\Local\Temp/ipykernel_8332/2208301278.py in <module>
     94 
     95 df = {'Description':descriptions[slice(504)],'Ratings':ratings[slice(504)],'Prices':prices[slice(504)]}
---> 96 dataset = pd.DataFrame(data = df) # Finally merging all the features into a single dataframe
     97 dataset
     98 dataset.to_csv('Mydata.csv')

~\anaconda3\envs\scrapenv\lib\site-packages\pandas\core\frame.py in __init__(self, data, index, columns, dtype, copy)
    612         elif isinstance(data, dict):
    613             # GH#38939 de facto copy defaults to False only in non-dict cases
--> 614             mgr = dict_to_mgr(data, index, columns, dtype=dtype, copy=copy, typ=manager)
    615         elif isinstance(data, ma.MaskedArray):
    616             import numpy.ma.mrecords as mrecords

~\anaconda3\envs\scrapenv\lib\site-packages\pandas\core\internals\construction.py in dict_to_mgr(data, index, columns, dtype, typ, copy)
    462         # TODO: can we get rid of the dt64tz special case above?
    463 
--> 464     return arrays_to_mgr(
    465         arrays, data_names, index, columns, dtype=dtype, typ=typ, consolidate=copy
    466     )

~\anaconda3\envs\scrapenv\lib\site-packages\pandas\core\internals\construction.py in arrays_to_mgr(arrays, arr_names, index, columns, dtype, verify_integrity, typ, consolidate)
    117         # figure out the index, if necessary
    118         if index is None:
--> 119             index = _extract_index(arrays)
    120         else:
    121             index = ensure_index(index)

~\anaconda3\envs\scrapenv\lib\site-packages\pandas\core\internals\construction.py in _extract_index(data)
    633             lengths = list(set(raw_lengths))
    634             if len(lengths) > 1:
--> 635                 raise ValueError("All arrays must be of the same length")
    636 
    637             if have_dicts:

ValueError: All arrays must be of the same length
df = pd.read_csv('Mydata.csv')
df.shape
conn = sqlite3.connect('TestDB1.db')
c = conn.cursor()
c.execute('CREATE TABLE IF NOT EXISTS etable (Id, Description, Ratng, Price)')
conn.commit()
df.to_sql('etable', conn, if_exists='replace', index = False)
c.execute('''SELECT * FROM etable''')
for row in c.fetchall():
    print(row)
