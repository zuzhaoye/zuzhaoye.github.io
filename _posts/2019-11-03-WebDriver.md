---
title: How to use Selenium and a browser driver to scrap web data
date: 2019-11-03 12:00:00 -0500
categories: [Tech Blog, Operation Demo]
tags: [webdriver]
---

Here are some notes on how to use Selenium package to scrap web data.
    
Language: Python 3.7

Dependencies:
- Selenium\
use pip install or it comes with Anaconda
- BeautifulSoup\
pip install bs4
- Browser\
Firefox will be used as an example. The same for Chrome.
- Browser Driver\
Find [Firefox driver] or [Chrome driver], download, and place it to a path you like.


## Step 1. Set up connection
{% highlight python %}
import time
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.keys import Keys
from selenium.common.exceptions import TimeoutException
from selenium.common.exceptions import NoSuchElementException

# function for initiating driver
def init_driver(file_path):
    options = webdriver.FirefoxOptions()
    options.add_argument("--start-maximized")
    driver = webdriver.Firefox(executable_path=file_path, firefox_options=options)
    driver.wait = WebDriverWait(driver, 10)
    return(driver)

# this should be the path where you place your driver
path = '/home' 
# 'geckodriver' is the driver for Firefox browser.
# Use 'geckodriver.exe' instead if you are using Windows OS.
driver = init_driver(path + "/geckodriver") 
{% endhighlight %}

If the above code runs successfully on your computer, you should see your browser pops out. Let's move on.

![](/assets/img/tech-blog/notes/webdriver/connect.png){:height="90%" width="90%"}

## Step 2. Open a website

Under the above code, add thess lines:

{% highlight python %}
# function for page navigation
def navigate_to_website(driver, site):
    driver.get(site)

# let's use Zillow as an example
site = "https://www.zillow.com/homes/"
navigate_to_website(driver, site)
{% endhighlight %}


You should see Zillow is opened in your browser.\
Note: Using web scrapper on Zillow is against Zillow's terms. It is only used for demonstration here.

## Step 3. Input
Now we want to automatically have something input to the website.

{% highlight python %}
def enter_search_term(driver, search_term):
    try:
        search_bar = driver.wait.until(EC.presence_of_element_located(
            (By.CLASS_NAME, "react-autosuggest__input")))
        search_bar.clear()
        search_bar.send_keys(search_term)
    except:
        raise ValueError("Entering search term failed")
    return search_bar

search_term = '10001'
handle = enter_search_term(driver, search_term)
{% endhighlight %}

You should see the searching box is filled with '10001'.\
Note: "react-autosuggest__input" is the class name of the input box, which you can find through right clicking on the input box and then click "inspect element".\

Then use the following command to enter the search.

{% highlight python %}
handle.send_keys(Keys.ENTER);
{% endhighlight %}

For a complete list of Keys available, refer to [Selenium api].

![](/assets/img/tech-blog/notes/webdriver/enter.png){:height="90%" width="90%"}

## Step 4. Click a button
Say if we want to click on a button, e.g. next page.

{% highlight python %}
def click_next_button(driver):
    try:
        button = driver.wait.until(EC.element_to_be_clickable(
            (By.CLASS_NAME, "zsg-pagination-next")))
        button.click()
    except:
        raise ValueError("Clicking button failed")

time.sleep(5) # give some time for the web to be loaded
click_next_button(driver)
{% endhighlight %}

You will be taken to next page.


![](/assets/img/tech-blog/notes/webdriver/click.png){:height="90%" width="90%"}


Notes:
- To be continued
- The remaining part will be using BeautifulSoup to parse the web information. This will be a relatively easy task.
- Visiting Zillow so frequently will be tested by CAPTCHA.
- For more information, refer to this [repo](https://github.com/ChrisMuir/Zillow) where I learned web scrapper from, but note that the code there is out-of-date.

[Firefox driver]:https://github.com/mozilla/geckodriver/releases
[Chrome driver]:https://chromedriver.chromium.org/downloads
[Selenium api]:https://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.common.keys

