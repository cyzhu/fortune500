# Historical Fortune 500 company lists
The dataset can be found under the `csv/` directory.

The lists are collected from a variety of sources, because I failed to find a complete dataset that contains all lists from 1955 to 2018. The methods and sources are described below.

## 1955-2005
HTML sources are downloaded using `urllib`, parsed using [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/), and saved as CSV. 

Data source (given as Python code):
```Python3
base = 'https://money.cnn.com/magazines/fortune/fortune500_archive/full/{}/{}.html'
urls = [base.format(year, page) for year in range(1955,2006) for page in (1,101,201,301,401)]
```

## 2006-2012
The data are scrapped manually from the sources below, because the HTML pages containing 2006-2012 data do not follow a uniform structure.

Data source:
```Python3
base = 'https://money.cnn.com/magazines/fortune/fortune500/{}/full_list/{}.html'
pages = ('index', '101_200', '201_300', '301_400', '401_500')
urls = [base.format(year, page) for year in range(2006,2013) for page in pages]
```

## 2015-
How to get data for 2015 is less obvious. Opening http://fortune.com/fortune500/2015/list with Google Chrome, only the top 20 companies are loaded. More rows are only loaded if you scroll down to the bottom of the page.

1. On the webpage, open [Developer Tools](https://developers.google.com/web/tools/chrome-devtools/).
2. Scroll to the bottom of the page, and the next 30 companies (ranked 21 through 50) will be loaded. 
3. In the **Network** panel, you can find a request whose type is [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch).
4. Right click on the request to reveal link `http://fortune.com/api/v2/list/1141696/expand/item/ranking/asc/20/30`
5. After inspecting, we find that `/20/30` means **skip 20 and take 30**, equivalent to getting row 21 through row 50.
6. It seems this API gives at most 100 rows per call. So, we can access `http://fortune.com/api/v2/list/1141696/expand/item/ranking/asc/0/100` to get the first 100 companies, and `http://fortune.com/api/v2/list/1141696/expand/item/ranking/asc/100/100` to get the next 100, and so on.
7. Finally, use the Python `json` package to parse the JSON files, and build the CSV files.

Data source:
http://fortune.com/fortune500/2015/list
http://fortune.com/api/v2/list/1141696/expand/item/ranking/asc/0/100
http://fortune.com/api/v2/list/1141696/expand/item/ranking/asc/100/100
http://fortune.com/api/v2/list/1141696/expand/item/ranking/asc/200/100
http://fortune.com/api/v2/list/1141696/expand/item/ranking/asc/300/100
http://fortune.com/api/v2/list/1141696/expand/item/ranking/asc/400/100
