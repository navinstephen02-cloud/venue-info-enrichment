# venue-info-enrichment
Developed a Python pipeline using the OpenAI API to enrich and structure venue datasets. Automated CSV-to-JSON transformation with schema validation, including demographics, opening hours, and metadata. Showcased skills in data engineering, API integration, and pipeline automation for real-world applications.
Venue Information Extraction with LLMs

This project uses Large Language Models (LLMs) to enrich raw venue data with structured information such as categories, opening hours, demographics, and media links. It is designed for dating and social applications where accurate, contextual venue information is critical for recommendations and user experiences.

Features
Reads venue data from a CSV file.
Uses the OpenAI GPT model (gpt-4o-mini) to:
Normalize venue details (name, type, website, contact).
Add structured metadata (opening hours, categories, promotional text).
Speculate demographic distributions (e.g., expected audience preferences).
Outputs structured JSON in a consistent schema.
Saves enriched data to venues_info_output.json.

Project Structure
├── main.py                     # Main script 
├── venues_info_output.json     # Example output file
├── requirements.txt            # Dependencies
└── README.md                   # Project documentation

Requirements
Python 3.8+
Install dependencies:
pip install openai

Usage

Place your CSV file with venue data in the project folder.

Expected columns: Name, Type, Latitude, Longitude, Phone, Website, Opening Hours, Logo URL, Address, All Tags

Update the input path in the script:
with open("dating_locations_copenhagen.csv", newline='', encoding='utf-8') as csvfile:
Run the script:
python main.py
Results will be saved in:
venues_info_output.json


Example Output
{
  "place_name": "Café Example",
  "promotional_text": "A cozy spot for coffee and casual dates.",
  "logo_image_url": "https://www.example.com/logo.png",
  "website_url": "https://www.example.com",
  "category": "Café",
  "opening_hours_mon_from": "08:00",
  "opening_hours_mon_to": "22:00",
  "Men_who_like_women": 90,
  "Men_who_like_men": 10,
  "Women_who_like_men": 85,
  "Women_who_like_women": 15
}

Use Cases

Dating Apps – Recommend venues aligned with user preferences.

Event Apps – Enrich event locations with demographics & context.

Maps/Travel – Provide richer metadata for points of interest.

Requires an OpenAI API key:
client = OpenAI(api_key="your_api_key_here")

