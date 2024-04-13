# Selenium Scraper Deployment on Google Cloud Run

This README provides detailed instructions for setting up a Selenium web scraper using Python, containerizing it with Docker, and deploying it on Google Cloud Run.

## Setup Instructions

1. **Install Required Python Libraries**

Create a `requirements.txt` file with the following content to manage Python package installations:

```plaintext
# requirements.txt
selenium==3.141.0
chromedriver-autoinstaller==0.3.1
flask==2.0.1
gunicorn==20.1.0
```

Install the packages using pip:

```bash
pip install -r requirements.txt
```

2. **Python Script for Selenium Scraper**

Create a Python script (`app.py`) that uses Selenium for web scraping:

```python
# app.py
from flask import Flask, jsonify
import chromedriver_autoinstaller
from selenium import webdriver

app = Flask(__name__)

@app.route('/scrape')
def scrape():
    chromedriver_autoinstaller.install()
    options = webdriver.ChromeOptions()
    options.add_argument('--headless')
    options.add_argument('--no-sandbox')
    options.add_argument('--disable-dev-shm-usage')
    driver = webdriver.Chrome(options=options)
    
    # Example: Scrape data from example.com
    url = "https://www.example.com"
    driver.get(url)
    data = driver.find_element_by_id('data').text
    driver.quit()
    
    return jsonify({'data': data})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

3. **Dockerfile for Containerization**

Create a `Dockerfile` to containerize your application:

```Dockerfile
# Dockerfile
FROM python:3.8-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["gunicorn", "--bind", "0.0.0.0:8080", "app:app"]
```

4. **Deploy on Google Cloud Run**

- Build your Docker image and push it to Google Container Registry.
- Deploy the image to Google Cloud Run.

Commands to build, push, and deploy:

```bash
# Build your Docker image
docker build -t gcr.io/your-project-id/selenium-scraper:v1 .

# Push the image to Google Container Registry
docker push gcr.io/your-project-id/selenium-scraper:v1

# Deploy to Google Cloud Run
gcloud run deploy selenium-scraper --image gcr.io/your-project-id/selenium-scraper:v1 --platform managed --region us-central1 --allow-unauthenticated
```

## Conclusion

This setup guides you through developing a Selenium web scraper, containerizing it with Docker, and deploying it on Google Cloud Run. Adapt the scraper to your specific needs and website structure.
