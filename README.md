# venue-info-enrichment with LLMs
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




import csv
import json
from openai import OpenAI

client = OpenAI(api_key="your_api_key_here") # Replace with your actual API key

# Define the function that retrieves venue information
def get_venue_information(name, venue_type, latitude, longitude, phone, website, opening_hours, logo_url, address, all_tags):
    function = {
        "name": "get_venue_information",
        "description": "Provide structured information about a venue",
        "parameters": {
            "type": "object",
            "properties": {
                "place_name": {"type": "string"},
                "promotional_text": {"type": "string"},
                "logo_image_url": {"type": "string"},
                "location_background": {"type": "string"},
                "website_url": {"type": "string"},
                "category": {"type": "string"},
                "opening_hours_mon_from": {"type": "string"},
                "opening_hours_mon_to": {"type": "string"},
                "opening_hours_tue_from": {"type": "string"},
                "opening_hours_tue_to": {"type": "string"},
                "opening_hours_wed_from": {"type": "string"},
                "opening_hours_wed_to": {"type": "string"},
                "opening_hours_thu_from": {"type": "string"},
                "opening_hours_thu_to": {"type": "string"},
                "opening_hours_fri_from": {"type": "string"},
                "opening_hours_fri_to": {"type": "string"},
                "opening_hours_sat_from": {"type": "string"},
                "opening_hours_sat_to": {"type": "string"},
                "opening_hours_sun_from": {"type": "string"},
                "opening_hours_sun_to": {"type": "string"},
                "Men_who_like_women": {"type": "number"},
                "dob_lower_Men_who_like_women": {"type": "number"},
                "dob_upper_Men_who_like_women": {"type": "number"},
                "Men_who_like_men": {"type": "number"},
                "dob_lower_Men_who_like_men": {"type": "number"},
                "dob_upper_Men_who_like_men": {"type": "number"},
                "Women_who_like_men": {"type": "number"},
                "dob_lower_Women_who_like_men": {"type": "number"},
                "dob_upper_Women_who_like_men": {"type": "number"},
                "Women_who_like_women": {"type": "number"},
                "dob_lower_Women_who_like_women": {"type": "number"},
                "dob_upper_Women_who_like_women": {"type": "number"}
            },
            "required": ["place_name"]
        }
    }

    # System prompt as requested
    system_prompt = """You are a helpful mapping assistant that provides detailed and structured information about venues and locations by searching the web. You will receive information in this format based on information previously gathered for you to research.
    You will output into the get_venue_information json_object format which relates to the fields description below.
    Here is an explanation of the fields.
    place_name: Official name of the location.
    promotional_text: Brief promotional tagline or description.
    logo_image_url: Direct URL to the location's logo (starting with "http(s)://www.").
    location_background: Direct URL to a background image for the location (starting with "http(s)://www.").
    website_url: Direct URL to the official website (starting with "http(s)://www.").
    category: Type or category of the location (e.g., park, museum).
    opening_hours_mon_from: Monday's opening time (in 24-hour format, e.g., "23:00").
    opening_hours_mon_to: Monday's closing time (in 24-hour format).
    opening_hours_tue_from: Tuesday's opening time (in 24-hour format).
    opening_hours_tue_to: Tuesday's closing time (in 24-hour format).
    opening_hours_wed_from: Wednesday's opening time (in 24-hour format).
    opening_hours_wed_to: Wednesday's closing time (in 24-hour format).
    opening_hours_thu_from: Thursday's opening time (in 24-hour format).
    opening_hours_thu_to: Thursday's closing time (in 24-hour format).
    opening_hours_fri_from: Friday's opening time (in 24-hour format).
    opening_hours_fri_to: Friday's closing time (in 24-hour format).
    opening_hours_sat_from: Saturday's opening time (in 24-hour format).
    opening_hours_sat_to: Saturday's closing time (in 24-hour format).
    opening_hours_sun_from: Sunday's opening time (in 24-hour format).
    opening_hours_sun_to: Sunday's closing time (in 24-hour format).
    - For the expected audience based on gender and orientation preferences, provide speculative percentages and age bounds.
    
    **Important**: Ensure the total distribution for men's preferences (`Men_who_like_women` + `Men_who_like_men`) sums to 100%. The same applies for women's preferences (`Women_who_like_men` + `Women_who_like_women`).

    For example, if 90% of men like women, then 10% of men like men. Similarly, if 85% of women like men, 15% of women like women."""

    # Prepare the user request
    user_request = "Provide detailed structured information for the venue with the following details:\n"
    fields = [
        ("Name", name), ("Type", venue_type), ("Latitude", latitude), ("Longitude", longitude),
        ("Phone", phone), ("Website", website), ("Opening Hours", opening_hours),
        ("Logo URL", logo_url), ("Address", address), ("All Tags", all_tags)
    ]
    for field, value in fields:
        if value is not None and value != "":
            user_request += f"{field}: {value}\n"

    try:
        response = client.chat.completions.create(
            model="gpt-4o-mini-2024-07-18",
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": user_request}
            ],
            functions=[function],
            function_call={"name": "get_venue_information"},
            temperature=0.1,
            max_tokens=1024,
            top_p=1,
            frequency_penalty=0,
            presence_penalty=0
        )

        # Extracting the JSON response from the API output
        function_call_arguments = response.choices[0].message.function_call.arguments
        json_response = json.loads(function_call_arguments)

        return json_response
    except Exception as e:
        print(f"An error occurred: {e}")
        return None

# Reading the CSV file
venues_info = []
with open("/kaggle/input/kaggle/dating_locations_copenhagen (4).xls", newline='', encoding='utf-8') as csvfile:
    reader = csv.DictReader(csvfile)
    for row in reader:
        # Extracting data from CSV columns
        name = row['Name']
        venue_type = row['Type']
        latitude = row['Latitude']
        longitude = row['Longitude']
        phone = row['Phone']
        website = row['Website']
        opening_hours = row['Opening Hours']
        logo_url = row['Logo URL']
        address = row['Address']
        all_tags = row['All Tags']
        
        # Call the get_venue_information function for each venue
        venue_info = get_venue_information(
            name=name,
            venue_type=venue_type,
            latitude=latitude,
            longitude=longitude,
            phone=phone,
            website=website,
            opening_hours=opening_hours,
            logo_url=logo_url,
            address=address,
            all_tags=all_tags
        )
        
        if venue_info:
            venues_info.append(venue_info)

# Print or save the collected information (for debugging you can print the JSON)
print(json.dumps(venues_info, indent=2))

# Optionally, save the output to a JSON file
with open("venues_info_output.json", "w", encoding="utf-8") as f:
    json.dump(venues_info, f, indent=2)
