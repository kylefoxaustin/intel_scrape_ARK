# intel_scrape_ARK
Designed to scrape Intel's ARK for embedded processors

do this in a virtual env for python dependencies (recommended but not absolutely required)
need python 3.6 or higher (tested on 3.10)

you need several python packages installed (pip install pandas requests beautifulsoup4 openpyxl odfpy)

you need to have installed chromedriver on your PC (not as a python package)

Input:
a spreadsheet table in this format
         Product Name          |  Launch Date  |  Total Cores  |  Max Turbo Frequency  |  Processor Base Frequency  |  Cache  | TDP
Intel® Xeon® 6503P-B Processor | 	   Q1'25	   |     12	       |     3.5 GHz	         |          2 GHz	            |  48 MB  | 110 W
      ^^^This is a URL link^^^
-- Note the column header text does not matter, the script will skip the first line in the spreadsheet

The script will read .xls and .ods files.  if for some reason the script doesn't understand .ods you likely do not have
libre office and its associated python libraries (python3-uno) installed.   To do this run: 

sudo apt-get update
sudo apt-get install libreoffice-core python3-uno

it will iterate on each hyperlink which points at an Intel product page within Intel Ark's sites.
an example product page is here: https://www.intel.com/content/www/us/en/products/sku/242897/intel-xeon-6503pb-processor-48m-cache-2-00-ghz/specifications.html 

The script will extract the Service Status field in each product page and repopulate the original spreadsheet with it.

You can modify the script to pull any data from the product page you wish. 
