# state import requests
from bs4 import BeautifulSoup
import pandas as pd

# URL of the Wikipedia page
url = "https://en.wikipedia.org/wiki/List_of_states_and_territories_of_the_United_States"

# Send a GET request to fetch the raw HTML content
response = requests.get(url)
if response.status_code != 200:
    print(f"Error: Unable to fetch page. Status code: {response.status_code}")
    exit()

# Parse the HTML content with BeautifulSoup
soup = BeautifulSoup(response.text, 'html.parser')

# Debug: Print first 500 characters of the page content
print(response.text[:500])

# Locate the table with the state data
table = soup.find('table', {'class': 'wikitable sortable plainrowheaders'})
if not table:
    print("Error: Could not find the table. Check the page structure or update the selector.")
    exit()

# Extract table rows
rows = table.find_all('tr')

# Initialize lists to hold state names and populations
states = []
populations = []

# Iterate through table rows and extract data
for row in rows[1:]:  # Skip the header row
    cells = row.find_all('td')
    if len(cells) > 0:
        state_name = cells[0].text.strip()
        population = cells[2].text.strip()
        states.append(state_name)
        populations.append(population)

# Combine the data into a DataFrame
data = pd.DataFrame({
    'State': states,
    'Population': populations
})

# Display the first few rows of the DataFrame
print(data.head())

# Optionally save the data to a CSV file
data.to_csv('us_states_population.csv', index=False)
