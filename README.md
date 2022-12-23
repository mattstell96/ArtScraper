<h1> Art Auction Prices: Dataset Generation With Scraping And APIs </h1>

<h2>Description</h2>
Describe project here
<br />


<h2>Python Libraries and Utilities</h2>


 - <b>Selenium</b>
 - <b>Pytrends (Google)</b>
 - <b>GoogleNews (Google)</b>    

<h2>Environments Used </h2>

- <b>macOS Monterey</b>
- <b>Chrome98.dmg</b>

<h2>Project walk-through:</h2>

<p align="center">

**Step 1. Load Libraries** <br/>

```py
#Basic
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import time

#Parsing
import requests
from bs4 import BeautifulSoup

#Crawling
import lxml
from selenium.webdriver import Chrome
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from time import sleep

from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager
```

<br />

**Step 2. Instantiate ChromeDriver: Open Artsy.net** <br/>

```py
# create an instance of web driver
driver = webdriver.Chrome(ChromeDriverManager().install())

main_page_url = 'https://www.artsy.net/'

#Open Artsy
driver.get(main_page_url)
sleep(10) #Give some time open the website
```

<br />

**Step 3. Execute Login** <br/>

```py
login = driver.find_element(by=By.XPATH, value='//*[@class ="Button__Container-sc-1bhxy1c-0 fnJjPI"]')
login.click()

#Login Button CLICK --> Shows pop up window

user = driver.find_element_by_xpath('//*[@name ="email"]')
passw = driver.find_element_by_xpath('//*[@name ="password"]')
login_click = driver.find_element_by_xpath('//*[@class ="Button__Container-sc-1bhxy1c-0 gMKEFL"]')

#HERE WE LOG-IN USING THE WEB ELEMENTS THAT WE SAVED
user.send_keys( YOUR EMAIL HERE );
passw.send_keys( YOUR PASSWORD HERE );
login_click.click()
sleep(10)
```

<br />

**Step 4. Generate A List of URLs Using Artists' First and Last Name** <br/>

*NOTE: Artsy follows a specific name formatting for artists (first-middle-last), which can be leveraged to create the URLs. *<br/>

```py
#URLS BUILDER
 
#WRITE ARTISTS NAME HERE USING THE FORMAT name-middlename-surname
my_artists = ['jean-michel-basquiat']

my_urls = []

for artist in my_artists:
    url = []
    url='https://www.artsy.net/artist/'+str(artist)+'/auction-results?categories%5B0%5D=Painting'
    my_urls.append(url)
```

<br />

**Step 5. Crawl and Scrape using Selenium** <br/>

First of all, let's define how many webpages we should crawl onto for a given artist<br/>

```py
pages = 5
```

Then, let's proceed with the scraping <br/>

```py

artwork_df = []
iterations = 0 #this is the iterations list that allows us to limit the pages to crowl to a given number...5

for url in my_urls:
    
    driver.get(url)
    
    next_page = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/nav/a')
        
    while True:

        if iterations == pages: #YOU CAN SET HOW MANY ITERATIONS YOU DESIRE
            break
        else:
            
            driver.get(url)
            
            iterations += 1 #Here we add the iteration count
            
            box1 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[1]').text
            box2 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[2]').text
            box3 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[3]').text
            box4 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[4]').text
            box5 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[5]').text
            box6 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[6]').text
            box7 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[7]').text
            box8 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[8]').text
            box9 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[9]').text
            box10 = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/button[10]').text         
            
            artboxes = [box1,box2,box3,box4,box5,box6,box7,box8,box9,box10]
            
            paintings = []
            
            for box in artboxes:
                if len(box)<10:
                    pass
                else:
                    paintings.append(box)
                    
            for painting in paintings:
                painting_info = []
                
                #ARTIST NAME
                name = url.split('artist/', 1)[1].split('/auction')[0]
                painting_info.append(name)
                
                #TITLE
                
                title = painting.split("\n")[0].split(",")[0]  
                painting_info.append(title)
                
                #YEAR

                year = painting.split("\n")[0].split(",")[1]
                painting_info.append(year)
                
                #MEDIUM
                medium = painting.split("\n")[1]
                painting_info.append(medium)
                
                #SALE_DATE
                date = painting.split("\n")[2]
                painting_info.append(date)
                
                #HOUSE
                house = painting.split("\n")[3]
                painting_info.append(house)
                
                #PRICE
                price = painting.split("\n")[4]
                painting_info.append(price)
                
                #PRICE_USD
                #price_usd = 
                    
                artwork_df.append(painting_info)
                
                i+=1
                
            next_page.click()
            sleep(2)
```

<br />

**Step 6. Close the driver** <br/>

```py
driver.quit()
```

<br />

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
