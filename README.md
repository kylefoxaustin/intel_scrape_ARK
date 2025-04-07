

# Intel Product Page Scraper

A tool for downloading Intel product specification pages and extracting service status information.
Specifically this focuses on Intel products with the 'Embedded Option' flag set to yes on the Intel ARK 
website: https://www.intel.com/content/www/us/en/ark/featurefilter.html?productType=873&0_Embedded=True 

# Pre-requisites:
an .ods or .xlsx file which contains at a minimum the URL(s) to a specific processor page
e.g. https://www.intel.com/content/www/us/en/products/sku/242897/intel-xeon-6503pb-processor-48m-cache-2-00-ghz/specifications.html 

# One method to get this table is:
1) Visit the Intel ARK website
2) Select the filter options you want
3) click 'Show More' at the bottom of the page (if more than 50 processors are available)
4) highlight the entire table, copy it and paste it into an .xlsx or .ods spreadsheet file
5) name the file.   it will be used in the script exectuion below. 

# Structure of the input file: 
Input:
a spreadsheet table in this format

          Product Name   |  Launch Date   |  Total Cores  |  Max Turbo Frequency  |  Processor Base Frequency  |  Cache  | TDP
         
          Intel® Xeon®   | 	   Q1'25	   |     12	       |     3.5 GHz	         |          2 GHz	            |  48 MB  | 110 W
      ^^^This is a URL link^^^
-- Note the column header text does not matter, the script will skip the first line in the spreadsheet

## Overview

This project consists of two main scripts:

1. `scrape.py` - Downloads HTML content from Intel product pages while bypassing anti-scraping measures
2. `extract_status.py` - Extracts service status information from the downloaded HTML files

The scraper handles Intel's anti-scraping protections by using multiple request methods and adding appropriate delays between requests. It can extract hyperlinks from ODS/Excel files and download the corresponding web pages.

## Requirements

### System Requirements

- Python 3.7 or higher
- Ubuntu (or any Linux distribution)

### Python Packages

Install the required packages using pip:

```bash
pip install requests pandas beautifulsoup4 openpyxl odfpy lxml
```

- `requests`: For making HTTP requests
- `pandas`: For data manipulation and file I/O
- `beautifulsoup4`: For HTML parsing
- `openpyxl`: For Excel file support
- `odfpy`: For ODS file support
- `lxml`: For better HTML parsing performance

## Usage

### Step 1: Download Product Pages

```bash
python3 scrape.py <input_file.ods> [output_directory] [options]
```

**Arguments:**
- `input_file.ods`: ODS/Excel file containing hyperlinks to Intel product pages
- `output_directory`: (Optional) Directory to save downloaded pages (default: intel_pages)

**Options:**
- `--delay <seconds>`: Delay between requests in seconds (default: 300)
- `--jitter <seconds>`: Random jitter added to delay (default: 60)
- `--start <index>`: Start from this index (for resuming)
- `--max <count>`: Maximum number of URLs to process
- `--test`: Test mode: process first product and exit
- `--retry-methods`: Try multiple access methods for each URL

**Example:**
```bash
# Download all pages with 1-minute delay
python3 scrape.py intel_embedded_table.ods --retry-methods --delay 60 --jitter 30

# Test with first URL only
python3 scrape.py intel_embedded_table.ods --test --retry-methods
```

### Step 2: Extract Service Status Information

```bash
python3 extract_status.py [options]
```

**Options:**
- `--html-dir <directory>`: Directory containing HTML files (default: intel_pages)
- `--original-file <file>`: Original ODS/Excel file to update with service status
- `--output-file <file>`: Output file path (default: service_status_results.xlsx)
- `--json`: Also output results as JSON

**Example:**
```bash
python3 extract_status.py --original-file intel_embedded_table.ods --output-file intel_with_status.xlsx
```

## File Structure

```
project/
├── scrape.py               # Main scraper script
├── extract_status.py       # Service status extraction script
├── intel_embedded_table.ods # Example input file with hyperlinks
├── intel_pages/            # Downloaded HTML files
│   ├── 20250407_*.html     # HTML content with timestamps
│   ├── progress.json       # Tracking progress
│   └── summary.csv         # Summary of downloaded pages
└── README.md               # This file
```

## How It Works

### Scraper

1. Extracts hyperlinks from an ODS/Excel file
2. Uses multiple request methods (desktop browser, mobile browser, etc.)
3. Adds delays between requests to avoid detection
4. Saves full HTML content with timestamps
5. Maintains progress tracking in case of interruptions

### Extractor

1. Processes downloaded HTML files to extract service status
2. Uses multiple extraction strategies to locate service status information
3. Merges extracted data with the original file
4. Generates a comprehensive summary of findings

## Troubleshooting

### 403 Forbidden Errors

If you encounter 403 Forbidden errors, the script will automatically try alternative access methods. The mobile browser method works most consistently.

### File Not Found

Ensure that all paths are correct and that the input file contains hyperlinks to Intel product pages. For ODS files, the hyperlinks must be in the first column.

### Extraction Issues

If the service status is not found, try:
1. Manually checking a few of the downloaded HTML files
2. Adjusting the extraction strategies in `extract_status.py`
3. Running with a single file to debug: `python3 extract_status.py --html-dir ./intel_pages/specific_file.html`

## Notes

- This script respects website terms of service by using significant delays between requests
- For large sets of URLs, the process may take several days to complete
- The extraction script can be run periodically while the scraper is still running
