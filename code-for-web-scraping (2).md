<center><h1>Scraping LeetCode Problem Details Using Python, Selenium & BeautifulSoup</h1></center>

<center>
    <a href="https://leetcode.com/">
         <img src="https://i.imgur.com/9aLyuqU.png" width=100px style="box-shadow:rgba(52, 64, 77, 0.2) 0px 1px 5px 0px;border-radius:4px;">
    </a>
</center>

<h2> Web Scraping </h2>

- Web Scraping is the process of deriving data from a website using a computer program. The program scrapes for HTML code, which is the standard markup that websites display content.

<h2> List of the tools used in this Notebook </h2>

- Python
- Selenium (Selenium is a Python library and tool used for automating web browsers to do a number of tasks. One of such is web-scraping to extract useful data and information that may be otherwise unavailable.)
- BeautifulSoup (Beautiful Soup is a Python library for pulling data out of HTML and XML files.)

<h2> About LeetCode </h2>

<p> "LeetCode is your ultimate platform for skill enhancement, knowledge expansion, and effective technical interview preparation, offering a vast library of 2900+ coding problems."</p>

 <center><a href="https://leetcode.com/problemset/all/"><img src="https://i.imgur.com/lyJxDDz.png" style="box-shadow:rgba(52, 64, 77, 0.2) 0px 1px 5px 0px;border-radius:10px;" width=800px></a></center>

<center><h3> First Part </h3></center>

- First we will be scraping all problems list.
- The first section of the website is the problem set table that contains all the problems. There are over 2900+ problems, distributed over 59 pages.The first section of the website is the problem set table that contains all the problems.

<center><a href="https://leetcode.com/problemset/all/"><img src="https://i.imgur.com/giQr7yk.png" style="box-shadow:rgba(52, 64, 77, 0.2) 0px 1px 5px 0px;border-radius:10px;" width=75%></a></center>
 
**The details we are scraping from the first section are:**

1. Title
2. Problem_URL
3. Solution_URL
4. Acceptance
5. Difficulty

<center><h3> Second Part </h3></center>

- Second we will scrape the problem information from every problem page.
- we needed to scrape 2900+ problem pages because we have 2900+ problems.

<div style="display: flex;">
  <div style="flex: 1; margin-right: 10px;">
    <a href="https://leetcode.com/problems/4sum/">
      <img src="https://i.imgur.com/5BwD2nb.png" style="box-shadow:rgba(52, 64, 77, 0.2) 0px 1px 5px 0px;border-radius:10px;width: 33%;">
    </a>
  </div>
  <div style="flex: 1; margin-right: 10px;">
    <a href="https://leetcode.com/problems/4sum/">
      <img src="https://i.imgur.com/ozWPDVj.png" style="box-shadow:rgba(52, 64, 77, 0.2) 0px 1px 5px 0px;border-radius:10px;width: 33%;">
    </a>
  </div>
  <div style="flex: 1;">
    <a href="https://leetcode.com/problems/4sum/">
      <img src="https://i.imgur.com/QETFefW.png" style="box-shadow:rgba(52, 64, 77, 0.2) 0px 1px 5px 0px;border-radius:10px;width: 33%;">
    </a>
  </div>
</div>

**The details we are scraping from the second section are:**

1. Premium Status
2. Title
3. Problem Description 
4. Topic Tags
5. Accepted
6. Submission
7. Solution
8. Discussion Count
9. Likes
10. Dislikes
11. Similar Questions

<center><h3> Third Part </h3></center>

- This is the last part.
- Combine both part of The DataFrame.
- Analyzing Data Availability

<h2> Scraping First Section: </h2>

`Importing And Installing Relevant Libraries`:


```python
import time  # For handling time-related functions
import re  # For regular expressions
import numpy as np  # For numerical operations
import pandas as pd  # For data manipulation and analysis
import dataframe_image as dfi  # For converting Pandas DataFrame to image
from bs4 import BeautifulSoup  # For web scraping
from selenium import webdriver  # For browser automation
from selenium.webdriver.chrome.service import Service  # For configuring the ChromeDriver service
```

1. `get_driver`:
   - Returns a configured instance of the Chrome WebDriver for browser automation.


```python
def get_driver():
    """
    Creates and configures a Chrome WebDriver instance for web scraping.

    Returns:
        WebDriver: Configured instance of the Chrome WebDriver.
    """
    # Set the path to the ChromeDriver executable
    service = Service('C:/Program Files/Google/Chrome/Application/chromedriver-win64/chromedriver.exe')

    # Configure Chrome options
    options = webdriver.ChromeOptions()
    
    # Ignore certificate errors
    options.add_argument('--ignore-certificate-errors')
    
    # Start the browser in maximized mode
    options.add_argument('--start-maximized')

    # Create a Chrome WebDriver instance with the specified service and options
    driver = webdriver.Chrome(service=service, options=options)

    # Return the WebDriver instance
    return driver

```

2. `get_page_source(url, delay=10)`:
   - Gets the page source of a specified URL using Selenium and BeautifulSoup.
   - Optional delay parameter to wait for the page to load.



```python
def get_page_source(url, delay=10):
    """
    Retrieves the page source of a specified URL using Selenium and BeautifulSoup.

    Args:
        url (str): The URL to scrape.
        delay (int, optional): Delay in seconds to wait for the page to load. Default is 10 seconds.

    Returns:
        BeautifulSoup: The BeautifulSoup object representing the page source.
    """
    # Get a Chrome WebDriver instance
    driver = get_driver()

    # Open the specified URL in the browser
    driver.get(url)

    # Allow time for the page to load (adjust delay as needed)
    time.sleep(delay)

    # Get the page source using BeautifulSoup for parsing
    page_source = BeautifulSoup(driver.page_source, 'html.parser')

    # Close the WebDriver to release resources
    driver.quit()

    # Return the parsed page source
    return page_source

```

3. `get_titles(page_source, first_page=False)`:
   - Extracts titles of problems from the page source.
   - Optional parameter `first_page` to handle starting index.


```python
def get_titles(page_source, first_page=False):
    """
    Extracts titles of problems from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.
        first_page (bool, optional): Flag indicating whether it's the first page. Default is False.

    Returns:
        list: List of extracted titles.
    """
    # Determine the starting index based on whether it's the first page or not
    start_index = 1 if first_page else 0

    # Find all title elements using BeautifulSoup
    title_elements = page_source.find_all(
        'a',
        class_=[
            'h-5 hover:text-blue-s dark:hover:text-dark-blue-s',
            'h-5 hover:text-blue-s dark:hover:text-dark-blue-s opacity-60'
        ]
    )[start_index:]

    # Extract text from title elements and store in a list
    titles = [title_element.text for title_element in title_elements]

    # Return the list of titles
    return titles

```

4. `get_problems_URL(page_source, first_page=False)`:
   - Extracts URLs of problems from the page source.
   - Optional parameter `first_page` to handle starting index.


```python
def get_problems_URL(page_source, first_page=False):
    """
    Extracts URLs of problems from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.
        first_page (bool, optional): Flag indicating whether it's the first page. Default is False.

    Returns:
        list: List of extracted problem URLs.
    """
    # Determine the starting index based on whether it's the first page or not
    start_index = 1 if first_page else 0

    # Find all problem elements with an 'a' tag and an 'href' attribute using BeautifulSoup
    problem_elements = page_source.find_all('a', href=True, class_=[
        'h-5 hover:text-blue-s dark:hover:text-dark-blue-s',
        'h-5 hover:text-blue-s dark:hover:text-dark-blue-s opacity-60'
    ])[start_index:]

    # Extract the 'href' attribute values from the problem elements and store in a list
    problems_url = [el['href'] for el in problem_elements]

    # Return the list of problem URLs
    return problems_url

```

5. `get_acceptances_difficulties(page_source, first_page=False)`:
   - Extracts acceptance rates and difficulties of problems from the page source.
   - Optional parameter `first_page` to handle starting index.


```python
def get_acceptances_difficulties(page_source, first_page=False):
    """
    Extracts acceptances and difficulties of problems from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.
        first_page (bool, optional): Flag indicating whether it's the first page. Default is False.

    Returns:
        tuple: Two lists - acceptances and difficulties.
    """
    # Find all div elements with the specified class using BeautifulSoup
    div_elements = page_source.find_all('div', class_='mx-2 flex items-center py-[11px]')

    # Determine the starting index based on whether it's the first page or not
    start_index = 1 if first_page else 0

    # Extract text from the span elements within the div elements and store in a list
    items = [
        span_element.text.strip()
        for div_element in div_elements
        for span_element in [div_element.find('span')]
        if span_element
    ]

    # Separate the items into acceptances and difficulties lists
    acceptances, difficulties = [], []
    for item in items:
        if item:
            (acceptances if item.endswith('%') else difficulties).append(item)

    # Adjust lists based on the starting index
    acceptances = acceptances[start_index:]
    difficulties = difficulties[start_index:]

    # Return the lists of acceptances and difficulties
    return acceptances, difficulties

```

6. `get_single_page_df(url, first_page=False)`:
   - Combines the above functions to create a Pandas DataFrame for a single page of problems.



```python
def get_single_page_df(url, first_page=False):
    """
    Creates a Pandas DataFrame for a single page of problems.

    Args:
        url (str): The URL of the page to scrape.
        first_page (bool, optional): Flag indicating whether it's the first page. Default is False.

    Returns:
        DataFrame: Pandas DataFrame containing titles, problem URLs, acceptances, and difficulties.
    """
    # Get the page source for the specified URL
    page_source = get_page_source(url)

    # Extract titles, problem URLs, acceptances, and difficulties from the page source
    titles = get_titles(page_source, first_page)
    problems_url = get_problems_URL(page_source, first_page)
    acceptances, difficulties = get_acceptances_difficulties(page_source, first_page)

    # Create a dictionary with the extracted data
    data = {
        'title': titles,
        'problem_URL': problems_url,
        'acceptance': acceptances,
        'difficulty': difficulties
    }

    # Create a DataFrame using the dictionary
    df = pd.DataFrame(data)

    # Return the DataFrame
    return df

```

7. `get_multiple_page_df(start=1, end=59)`:
   - Calls `get_single_page_df` for a range of pages and concatenates the results into a single DataFrame.


```python
def get_multiple_page_df(start=1, end=59):
    """
    Calls get_single_page_df for a range of pages and concatenates the results into a single DataFrame.

    Args:
        start (int, optional): The starting page. Default is 1.
        end (int, optional): The ending page. Default is 59.

    Returns:
        DataFrame: Pandas DataFrame containing titles, problem URLs, acceptances, and difficulties for multiple pages.
    """
    # Initialize an empty list to store DataFrames for each page
    list_of_dfs = []

    # Set the flag for the first page
    first_page = True if start == 1 else False

    # Iterate over the specified range of pages
    for i in range(start, end + 1):
        # Construct the URL for the current page
        url = 'https://leetcode.com/problemset/all/?page=' + str(i)

        # Get the DataFrame for the current page and append it to the list
        df = get_single_page_df(url, first_page)
        list_of_dfs.append(df)

        # Update the first_page flag for subsequent pages
        first_page = False

    # Concatenate the list of DataFrames into a single DataFrame
    df = pd.concat(list_of_dfs, ignore_index=True)

    # Return the final DataFrame
    return df

```

8. `scrape(start=1, end=59, file_name='part1.csv')`:
   - Initiates the scraping process, saving the resulting DataFrame to a CSV file.


```python
def scrape(start=1, end=59, file_name='part1.csv'):
    """
    Initiates the scraping process, saving the resulting DataFrame to a CSV file.

    Args:
        start (int, optional): The starting page for scraping. Default is 1.
        end (int, optional): The ending page for scraping. Default is 59.
        file_name (str, optional): The name of the CSV file to save the scraped data. Default is 'part1.csv'.
    """
    # Get the DataFrame by scraping multiple pages
    df = get_multiple_page_df(start, end)

    # Save the DataFrame to a CSV file
    df.to_csv(path_or_buf=file_name, index=False)

```

`Running Scraping Process`:


```python
scrape(start=1, end=2, file_name='x1.csv')
```

<p>This is the sample Dataframe : </p>

<img src="https://i.imgur.com/bj5NPGA.png">

`Data preprocessing in first section`:

- We need to convert `problem_URL` to proper url format.
- We need to add `solution_URL` column in this dataframe.


```python
df1['problem_URL'] = df1['problem_URL'].apply(lambda x: f'{"https://leetcode.com"}{x}')
```


```python
df1['solution_URL'] = df1['problem_URL'].apply(lambda x: f'{x}{"/solution"}')
```

<p>Now we are done with preprocssing: </p>

<img src="https://i.imgur.com/7gUlYSg.png">

<center> <h2 style="color:blue"> Result of First Section: </h2> </center>

<p>This is the sample Dataframe : </p>

<img src="https://i.imgur.com/Z70wkgZ.png">

<h2> Scraping Second Section: </h2>

1. `get_title(page_source)`:
   - Extracts the title from the given page source.`


```python
def get_title(page_source):
    """
    Extracts the title from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: The extracted title text.
    """
    # Find the title element using BeautifulSoup
    title_element = page_source.find(
        'a',
        class_='mr-2 text-label-1 dark:text-dark-label-1 hover:text-label-1 dark:hover:text-dark-label-1 text-lg font-medium'
    )

    # Extract the text content from the title element
    title = title_element.text

    # Return the title
    return title

```

2. `get_problem_description(page_source)`:
   - Extracts the problem description from the given page source.


```python
def get_problem_description(page_source):
    """
    Extracts the problem description from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: The extracted problem description text.
    """
    # Find the div element containing the problem description using BeautifulSoup
    description_element = page_source.find(
        'div',
        class_='xFUwe'
    )

    # Extract the text content from the description element
    description_text = description_element.text

    # Return the problem description
    return description_text

```

3. `get_topic_tags(page_source)`:
   - Extracts topic tags from the given page source.


```python
def get_topic_tags(page_source):
    """
    Extracts the topic tags from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: Comma-separated string of extracted topic tags.
    """
    # Initialize an empty list to store topic tags
    topic_tags = []

    # Find all elements with the specified class using BeautifulSoup
    topic_tag_elements = page_source.find_all('a',
                 class_='mr-4 rounded-xl px-2 py-1 text-xs transition-colors text-label-2 dark:text-dark-label-2 hover:text-label-2 dark:hover:text-dark-label-2 bg-fill-3 dark:bg-dark-fill-3 hover:bg-fill-2 dark:hover:bg-dark-fill-2') 

    # Extract text content from each topic tag element and append to the list
    for topic_tag_element in topic_tag_elements:
        topic_tag = topic_tag_element.text
        topic_tags.append(topic_tag)

    # Join the list of topic tags into a comma-separated string
    topic_tags_str = ', '.join(f"'{item}'" for item in topic_tags)

    # Return the formatted string of topic tags
    return topic_tags_str

```

4. `get_accepted(page_source)`:
   - Extracts the number of accepted submissions from the given page source.


```python
def get_accepted(page_source):
    """
    Extracts the number of accepted submissions from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        Tag: The BeautifulSoup Tag object containing the information about accepted submissions.
    """
    # Find all elements with the specified class using BeautifulSoup
    accepted_elements = page_source.find_all('div', class_='text-label-1 dark:text-dark-label-1 text-sm font-medium')

    # Return the first element (assuming it contains the information about accepted submissions)
    return accepted_elements[0]

```

5. `get_submission(page_source)`:
   - Extracts the number of total submissions from the given page source.


```python
def get_submission(page_source):
    """
    Extracts the number of total submissions from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        Tag: The BeautifulSoup Tag object containing the information about total submissions.
    """
    # Find all elements with the specified class using BeautifulSoup
    submission_elements = page_source.find_all('div', class_='text-label-1 dark:text-dark-label-1 text-sm font-medium')

    # Return the second element (assuming it contains the information about total submissions)
    return submission_elements[1]

```

6. `get_solution(page_source)`:
   - Extracts the solution type (e.g., 'Accepted') from the given page source.


```python
def get_solution(page_source):
    """
    Extracts the solution type (e.g., 'Accepted') from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: The extracted solution type.
    """
    # Find the element with a link containing 'solutions' using BeautifulSoup
    solutions_element = page_source.find(href=re.compile("solutions"))

    # Use regular expression to extract the solution type from the link text
    solution_type = re.findall(r"\((.*?)\)", solutions_element.text)[0]

    # Return the extracted solution type
    return solution_type

```

7. `get_discussion_count(page_source)`:
   - Extracts the count of discussions from the given page source.


```python
def get_discussion_count(page_source):
    """
    Extracts the count of discussions from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: The extracted count of discussions.
    """
    # Find the element with the specified class using BeautifulSoup
    discussion_count_element = page_source.find_all('div', class_='flex-1 text-sm leading-[22px]')[0]

    # Use regular expression to extract the count of discussions from the element's text
    discussion_count = re.findall(r"\((.*?)\)", discussion_count_element.text)[0]

    # Return the extracted count of discussions
    return discussion_count

```

8. `get_likes(page_source)`:
   - Extracts the number of likes from the given page source.


```python
def get_likes(page_source):
    """
    Extracts the number of likes from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: The extracted number of likes.
    """
    # Find the element with the specified class using BeautifulSoup
    likes_element = page_source.find_all('div', class_='text-lg text-gray-6 dark:text-dark-gray-6')[0]

    # Find the next sibling element and extract the text content
    likes_count = likes_element.find_next_sibling().text

    # Return the extracted number of likes
    return likes_count

```

9. `get_dislikes(page_source)`:
   - Extracts the number of dislikes from the given page source.


```python
def get_dislikes(page_source):
    """
    Extracts the number of dislikes from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: The extracted number of dislikes.
    """
    # Find the element with the specified class using BeautifulSoup
    dislikes_element = page_source.find_all('div', class_='text-lg text-gray-6 dark:text-dark-gray-6')[1]

    # Find the next sibling element and extract the text content
    dislikes_count = dislikes_element.find_next_sibling().text

    # Return the extracted number of dislikes
    return dislikes_count

```

10. `get_similar_questions(page_source)`:
    - Extracts a list of similar questions from the given page source.


```python
def get_similar_questions(page_source):
    """
    Extracts a list of similar questions from the given page source.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: Comma-separated string of extracted similar questions.
    """
    # Initialize an empty list to store similar questions
    similar_questions = []

    # Find all elements with the specified class using BeautifulSoup
    similar_question_elements = page_source.find_all('a', class_='text-sm font-medium transition-none text-label-1 dark:text-dark-label-1 hover:text-blue-s dark:hover:text-dark-blue-s')

    # Extract text content from each similar question element and append to the list
    for similar_question_element in similar_question_elements:
        similar_question = similar_question_element.text
        similar_questions.append(similar_question)

    # Join the list of similar questions into a comma-separated string
    similar_questions_str = ', '.join(f"'{item}'" for item in similar_questions)

    # Return the formatted string of similar questions
    return similar_questions_str

```

11. `get_page_source(url, delay=10)`:
    - Retrieves the page source of a specified URL using Selenium and BeautifulSoup.


```python
def get_page_source(driver, url, delay=10):
    """
    Retrieves the page source of a specified URL using Selenium and BeautifulSoup.

    Args:
        driver (webdriver): The Selenium WebDriver object.
        url (str): The URL to scrape.
        delay (int, optional): Delay in seconds to wait for the page to load. Default is 10 seconds.

    Returns:
        BeautifulSoup: The BeautifulSoup object representing the page source.
    """
    
    # Open the specified URL in the browser
    driver.get(url)

    # Allow time for the page to load (adjust delay as needed)
    time.sleep(delay)

    # Get the page source using BeautifulSoup for parsing
    page_source = BeautifulSoup(driver.page_source, 'html.parser')

    # Return the parsed page source
    return page_source

```

12. `get_ispremium(page_source)`:
    - Checks if the page indicates a premium status.


```python
def get_is_premium(page_source):
    """
    Checks if the page indicates a premium status.

    Args:
        page_source (BeautifulSoup): The BeautifulSoup object representing the page source.

    Returns:
        str: 'True' if premium, 'False' otherwise.
    """
    # Find the element with the specified class using BeautifulSoup
    premium_element = page_source.find('a', class_='no-underline truncate cursor-pointer rounded-lg py-[5px] px-4 text-md font-medium text-white hover:text-white hover:dark:text-white bg-brand-orange dark:bg-dark-brand-orange')

    # Determine premium status based on the existence of the element
    is_premium = 'True' if premium_element else 'False'

    # Return the premium status
    return is_premium

```

13. `scrape(df1, start=1, end=2920, file_name='x2.csv')`:
    - Scrapes data for a range of links from the provided DataFrame and saves it to a CSV file.


```python
def scrape(df1, start=1, end=2920, file_name='x2.csv'):
    """
    Scrapes data for a range of links from the provided DataFrame and saves it to a CSV file.

    Args:
        df1 (DataFrame): The DataFrame containing problem URLs.
        start (int, optional): The starting index for scraping. Default is 1.
        end (int, optional): The ending index for scraping. Default is 2920.
        file_name (str, optional): The name of the CSV file to save the scraped data. Default is 'x2.csv'.
    """
    # Extract links for the specified range from the DataFrame
    links = df1['problem_URL'][start - 1:end]

    # Initialize an empty list to store DataFrames for each link
    dfs = []
    
    # Set the path to the ChromeDriver executable
    service = Service('C:/Program Files/Google/Chrome/Application/chromedriver-win64/chromedriver.exe')

    # Configure Chrome options
    options = webdriver.ChromeOptions()
    options.add_argument('--ignore-certificate-errors')  # Ignore certificate errors
    options.add_argument('--start-maximized')  # Start the browser in maximized mode

    # Create a Chrome WebDriver instance with the specified service and options
    driver = webdriver.Chrome(service=service, options=options)

    # Iterate over the links and scrape data
    for link in links:
        i = 0
        # Get the page source for the current link
        page_source = get_page_source(driver, link, delay=10)

        # Create a dictionary to store scraped data
        data = {'is_premium': get_is_premium(page_source)}

        # Check if the problem is not premium before scraping additional data
        if data['is_premium'] == 'False':
            # Update the data dictionary with additional scraped data
            data.update({
                'title': get_title(page_source),
                'problem_description': get_problem_description(page_source),
                'topic_tags': get_topic_tags(page_source),
                'accepted': get_accepted(page_source),
                'submission': get_submission(page_source),
                'solution': get_solution(page_source),
                'discussion_count': get_discussion_count(page_source),
                'likes': get_likes(page_source),
                'dislikes': get_dislikes(page_source),
                'similar_questions': get_similar_questions(page_source)
            })

            # Create a DataFrame for the current link and append it to the list
            df = pd.DataFrame(data, index=[i])
            dfs.append(df)
            i += 1

    # Concatenate the list of DataFrames into a single DataFrame
    df = pd.concat(dfs, ignore_index=True)

    # Save the final DataFrame to a CSV file
    df.to_csv(path_or_buf=file_name, index=None)
```


```python
scrape(df1, start=1, end=5, file_name='x2.csv')
```

<center> <h2 style="color:blue"> Result of Second Section: </h2> </center>

<p>This is the sample Dataframe : </p>

<img src="https://i.imgur.com/kKCZWQy.png">

<h2> Third Section </h2>

<p> Combine Both Part of The DataFrame: </p>


```python
df = df1.merge(df2,left_on='title', right_on='title', how='left')
```


```python
df.to_csv('x.csv', index=None)
```


```python
len(df.columns)
```




    14



<center> <h2 style="color:blue"> Result of Third Section: </h2> </center>

<p style="font-size: 18px"><b> Information about a DataFrame:</b></p>

```python
df.info()
```

<center>
<pre>
Int64Index: 2920 entries, 0 to 2919
Data columns (total 14 columns):
 #   Column               Non-Null Count  Dtype  
---  ------               --------------  -----  
 0   title                2920 non-null   object 
 1   problem_URL          2920 non-null   object 
 2   acceptance           2920 non-null   object 
 3   difficulty           2920 non-null   object 
 4   is_premium           2103 non-null   object 
 5   problem_description  2103 non-null   object 
 6   topic_tags           2051 non-null   object 
 7   accepted             2103 non-null   object 
 8   submission           2103 non-null   object 
 9   solution             2103 non-null   object 
 10  discussion_count     2103 non-null   float64
 11  likes                2103 non-null   object 
 12  dislikes             2103 non-null   object 
 13  similar_questions    1308 non-null   object 
dtypes: float64(1), object(13)
memory usage: 342.2+ KB
</pre>
</center>

- We have data on 2,920 LeetCode problems.
- Out of these, 817 are premium problems, so we lack additional data for them.
- There are 2,051 null values in the `topic_tags` column. This is because 817 premium problems and other 52 null values indicate that the topic tags information is not available in the leetcode website.
- In the `similar_questions` column, there are 1,612 null values. This is because 817 premium problems and other 795 null values indicate that similar questions information is not available in the leetcode website.

**Future Work**

- Finding ways to improve upon the code and documentation of this project
- Parallelizing the code to make the scaping process simpler and faster 
- Automating this script to run every week to get the latest data
- Getting information about the premium data

**References**

1. Selenium Documentation: https://www.selenium.dev/selenium/docs/api/py/api.html
2. Selenium-Python Documentation https://selenium-python.readthedocs.io/
3. Beautiful Soup Documentation https://www.crummy.com/software/BeautifulSoup/bs4/doc/
4. Stackoverflow https://stackoverflow.com/
