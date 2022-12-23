<h1> Art Auction Prices: Dataset Generation With Scraping</h1>

<p align="center">

<h2>Description</h2>

The aim of this project is to create a dataset about paintings auction prices by scraping Artsy.net database. The final dataset will contain information regarding the:
+ Painter
+ Painting Name
+ Medium
+ Auction Sale Date
+ Auction House / Dealer
+ Hammer Price (Sale Price)
+ Premium with Respect to Estimate
+ Size

<br />

<h2>Python Libraries and Utilities</h2>

 - <b>Selenium</b> 
 
<br />

<h2>Environments Used </h2>

- <b>macOS Monterey</b>
- <b>Chrome98.dmg</b>

💡 Chrome98 can be also installed directly within the coding environment using the following code:

```py
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(ChromeDriverManager().install())
```

<br />

<h2>Project walk-through:</h2>

<br />
 
<h3> Setup </h3>

**Step 1. Load Libraries** <br/>

```py
#Basic
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

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
```

<br />

**Step 2. Instantiate ChromeDriver & Open Artsy.net** <br/>

```py
# create an instance of web driver
driver = Chrome('/Users/matteo-stelluti/Desktop/ADV_PYTHON/ART_PROJECT/PHASE 4/Data/chromedriver')

main_page_url = 'https://www.artsy.net/'

#Open Artsy
driver.get(main_page_url)
sleep(5)
```

<br />

**Step 3. Execute Login** <br/>

```py
#Login Button CLICK --> Shows pop up window
driver.find_element_by_xpath('//*[@class ="Button__Container-sc-1ckr5i3-0 dDcOsO"]').click()

user = driver.find_element_by_xpath('//*[@name ="email"]')
passw = driver.find_element_by_xpath('//*[@name ="password"]')
login = driver.find_element_by_xpath('//*[@class ="Button__Container-sc-1ckr5i3-0 fWQVvr"]')

#HERE WE LOG-IN USING THE WEB ELEMENTS THAT WE SAVED
user.send_keys(YOUR ARTSY.NET EMAIL HERE);
passw.send_keys(YOUR PASSWORD HERE);
login.click()
sleep(5)
```

<br />

<h3> Scraping </h3>

**Step 4. Upload the List Of Artists** <br/>

*NOTE: This list of artists was pre-made, and you can find it in the repo*<br/>

```py
urls_filtered = pd.read_csv(FILE PATH HERE)
urls_filtered = urls_filtered.URL.tolist()
```

<br />

**Step 6. Crawling & Scraping** <br/>

```py
artwork_df = []

for url in urls_filtered:
    
    driver.get(url)
    
    #Step 1. Check if the artist has the market signal section
    
    try: 
        signal = driver.find_element(By.XPATH,value='//*[@id="main"]/div/div/div/div[4]/div[2]/div[1]')
        signal.text == 'Market Signals More info'
        
        #A] Artists with the signal section
        
        #i. Open the artwork boxes for the additional info
        
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[2]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[5]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[8]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)        
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[11]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[14]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[17]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[20]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[23]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[26]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[29]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        
        #ii. Create a list with all the data from the 10 artwork boxes found on each page
        
        next_page = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[7]/div[2]/nav/a')

        iterations = 0 #this is the iterations list that allows us to limit the pages to crowl to a given number...5
        
        while True:

            if iterations ==5: #YOU CAN SET HOW MANY ITERATIONS YOU DESIRE
                break
            else:
                iterations += 1 #Here we add the iteration count

                art_box1 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[2]')
                art_box2 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[5]')
                art_box3 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[8]')
                art_box4 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[11]')
                art_box5 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[14]')
                art_box6 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[17]')
                art_box7 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[20]')
                art_box8 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[23]')
                art_box9 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[26]')
                art_box10 = driver.find_elements(By.XPATH, '//*[@id ="main"]/div/div/div/div[4]/div[7]/div[2]/div[3]/div[2]/div[29]')

                art_boxes = [art_box1,art_box2,art_box3,art_box4,art_box5,art_box6,art_box7,art_box8,art_box9,art_box10]

                arts_box_1toN = [] #This list will have n paintings out of 10 depending on the page

                for box in art_boxes:
                    if len(box)>0:
                        info = box[0].text.splitlines()
                        arts_box_1toN.append(info)
                        
                #iii. Loop over the n paintings of this i page to extract the info and put it in a list

                for art_box in arts_box_1toN: #AKA: For a single painting (1 of the 10) in a given page

                    art_box_fields = []

                    name = url.split('artist/', 1)[1].split('/auction')[0]
                    art_box_fields.append(name)
                    
                    if len(art_box)==19: # HERE WE GET THE ITEMS FOR THOSE WITH SALEPRICE IN ANOTHER CURRENCY
                    
                        try:
                            title = art_box[0].split(",")[0].replace(',','')
                        except:
                            title = ''
                        art_box_fields.append(title)
                        try:
                            year = art_box[0].split(",")[1].replace(',','')
                        except:
                            year = ''
                        art_box_fields.append(year)
                        try:
                            medium = art_box[1]
                        except:
                            medium = ''
                        art_box_fields.append(medium)
                        try:
                            sale_date = art_box[2]
                        except:
                            sale_date = ''
                        art_box_fields.append(sale_date)
                        try:
                            auction_house = art_box[3]
                        except:
                            auction_house = ''
                        art_box_fields.append(auction_house)
                        try:
                            realized_price_US = art_box[5]
                        except:
                            realized_price_US = ''
                        art_box_fields.append(realized_price_US)
                        try:
                            premium_v_est = art_box[7]
                        except:
                            premium_v_est = ''
                        art_box_fields.append(premium_v_est)
                        try:
                            size = art_box[11]
                        except:
                            size = ''
                        art_box_fields.append(size)

                    elif len(art_box)==18: # HERE WE GET THE ITEMS FOR THOSE WITH ONLY DOLLAR SALEPRICE
                        
                        try:
                            title = art_box[0].split(",")[0].replace(',','')
                        except:
                            title = ''
                        art_box_fields.append(title)
                        try:
                            year = art_box[0].split(",")[1].replace(',','')
                        except:
                            year = ''
                        art_box_fields.append(year)
                        try:
                            medium = art_box[1]
                        except:
                            medium = ''
                        art_box_fields.append(medium)
                        try:
                            sale_date = art_box[2]
                        except:
                            sale_date = ''
                        art_box_fields.append(sale_date)
                        try:
                            auction_house = art_box[3]
                        except:
                            auction_house = ''
                        art_box_fields.append(auction_house)
                        try:
                            realized_price_US = art_box[4]
                        except:
                            realized_price_US = ''
                        art_box_fields.append(realized_price_US)
                        try:
                            premium_v_est = art_box[6]
                        except:
                            premium_v_est = ''
                        art_box_fields.append(premium_v_est)
                        try:
                            size = art_box[10]
                        except:
                            size = ''
                        art_box_fields.append(size)


                    #add the list with the painting info to a list that will contain all the paintings
                    artwork_df.append(art_box_fields)
                    
                next_page.click()
                sleep(1)
        
    except:
        #B] Artists without the signal section
        
        #i. Open the artwork boxes for the additional info
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[2]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[5]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[8]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)        
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[11]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[14]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[17]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[20]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass

        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[23]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[26]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        try:
            driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[29]/div/div[1]/div/div[4]/div/div[2]').click()
            sleep(1)
        except:
            pass
        
        #ii. Create a list with all the data from the 10 artwork boxes found on each page
        
        iterations = 0 #this is the iterations list that allows us to limit the pages to crowl to a given number...5
        
        next_page = driver.find_element(by=By.XPATH, value='//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/nav/a')

        while True:

            if iterations ==5: #YOU CAN SET HOW MANY ITERATIONS YOU DESIRE
                break
            else:
                iterations += 1 #Here we add the iteration count

                art_box1 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[2]')
                art_box2 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[5]')
                art_box3 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[8]')
                art_box4 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[11]')
                art_box5 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[14]')
                art_box6 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[17]')
                art_box7 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[20]')
                art_box8 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[23]')
                art_box9 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[26]')
                art_box10 = driver.find_elements(By.XPATH, '//*[@id="main"]/div/div/div/div[4]/div[6]/div[2]/div[3]/div[2]/div[29]')

                art_boxes = [art_box1,art_box2,art_box3,art_box4,art_box5,art_box6,art_box7,art_box8,art_box9,art_box10]

                arts_box_1toN = []

                for box in art_boxes: #This loop cleans the elements above in case there are less than 10 paintings
                    if len(box)>0:
                        info = box[0].text.splitlines()
                        arts_box_1toN.append(info)
                
                #iii. Loop over the n paintings of this i page to extract the info and put it in a list
                for art_box in arts_box_1toN: #AKA: For a single painting (1 of the 10) in a given page

                    art_box_fields = []
                    
                    name = url.split('artist/', 1)[1].split('/auction')[0]
                    art_box_fields.append(name)

                    try:
                        title = art_box[0].split(",")[0].replace(',','')
                    except:
                        title = ''
                    art_box_fields.append(title)
                    try:
                        year = art_box[0].split(",")[1].replace(',','')
                    except:
                        year = ''
                    art_box_fields.append(year)
                    try:
                        medium = art_box[1]
                    except:
                        medium = ''
                    art_box_fields.append(medium)
                    try:
                        sale_date = art_box[2]
                    except:
                        sale_date = ''
                    art_box_fields.append(sale_date)
                    try:
                        auction_house = art_box[3]
                    except:
                        auction_house = ''
                    art_box_fields.append(auction_house)
                    try:
                        realized_price_US = art_box[5]
                    except:
                        realized_price_US = ''
                    art_box_fields.append(realized_price_US)
                    try:
                        premium_v_est = art_box[7]
                    except:
                        premium_v_est = ''
                    art_box_fields.append(premium_v_est)
                    try:
                        size = art_box[11]
                    except:
                        size = ''
                    art_box_fields.append(size)

                #add the list with the painting info to a list that will contain all the paintings
                    artwork_df.append(art_box_fields)
                
                next_page.click()
                sleep(1)
```

<br />

**Step 7. Convert to DF & Check Results** <br/>

```py
paintings_df = pd.DataFrame(artwork_df)
paintings_df.head()
```

OUTPUT:<br/>
<img src="https://i.imgur.com/Q6A9BVi.png" height="80%" width="80%" alt="First output"/>

Everything worked out.

<br />

**Step 8. Close the Driver** <br/>

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
