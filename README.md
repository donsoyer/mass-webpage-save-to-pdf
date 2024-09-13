Change path of the output_folder    
You will need:
python, wkhtmltox, chromedriver (google chrome or chrome for testing)

    # mass-webpage-save-to-pdf
    save multiple websites as pdf files
    import os
    import time
    from selenium import webdriver
    from selenium.webdriver.chrome.service import Service
    from selenium.webdriver.chrome.options import Options
    import pdfkit
    
    # Path settings
    output_folder = r'C:\str'
    if not os.path.exists(output_folder):
        os.makedirs(output_folder)
    
    # List of URLs
    urls = """
    https://www.xxxxxxxxxxxxxxxxxxxxxx.com
    https://ccccccccccccccccccccccccccccccc.com
    https://dsdfsdccccccccccccccccccccccccccccccc.com
    """.strip().split('\n')
    
    # Chrome browser options
    chrome_options = Options()
    chrome_options.binary_location = r'C:\chrome for testing\chrome.exe'  # Setting Chrome for Testing
    chrome_options.add_argument('--headless')  # To run in the background without a browser window
    chrome_options.add_argument('--no-sandbox')
    chrome_options.add_argument('--disable-dev-shm-usage')
    
    # Path to the ChromeDriver file
    chromedriver_path = r'C:\chrome for testing\chromedriver\chromedriver.exe'
    
    # Launching the Chrome browser
    service = Service(chromedriver_path)
    driver = webdriver.Chrome(service=service, options=chrome_options)
    
    # Function to scroll the page and save as PDF
    def save_url_as_pdf(url, output_path):
        try:
            print(f"Processing: {url}")
            driver.get(url)
            
            # Scrolling down the page to load all content
            last_height = driver.execute_script("return document.body.scrollHeight")
            while True:
                driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
                time.sleep(2)
                new_height = driver.execute_script("return document.body.scrollHeight")
                if new_height == last_height:
                    break
                last_height = new_height
            
            # Getting the HTML of the page
            html_source = driver.page_source
    
            # Adding a header with UTF-8 encoding and style for Polish characters
            html_with_style = """
            <meta charset="UTF-8">
            <style>
            body {
                font-family: Arial, sans-serif;
            }
            </style>
            """ + html_source
    
            # Saving HTML to PDF
            print(f"Saving page as PDF: {output_path}")
            pdfkit.from_string(html_with_style, output_path)
            print(f"Saved {output_path}")
        except Exception as e:
            print(f"Error processing {url}: {str(e)}")
    
    # Processing each page
    for url in urls:
        file_name = url.split("//")[-1].replace('.', '_').replace('/', '_') + ".pdf"
        output_path = os.path.join(output_folder, file_name)
        save_url_as_pdf(url, output_path)
    
    # Closing the browser
    driver.quit()
    
    print("Finished processing all pages.")
    
