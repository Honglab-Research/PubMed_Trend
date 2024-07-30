# SeungpilJeong_PubMed_Trend
Overview
The script is designed to analyze trends in multiomics data analysis methods by counting the number of publications related to specific keywords from the PubMed database over a range of years (2011-2024). The results are then saved into a CSV file.

Key Components
Keywords and Targets:

base_keywords: A list of base keywords related to omics.
targets: A list of specific analysis methods and networks to search for.
Filename:

The results will be saved in a CSV file named based on the base_keywords.
Imports:

requests: Used to make HTTP requests to the PubMed API.
csv: Used to write the results to a CSV file.
API Setup:

BASE_URL and SEARCH_URL: Base URL and search endpoint for the PubMed API.
DB: Specifies the PubMed database.
Functions
formulate_query:

This function constructs a search query string by combining the keywords and optionally a publication year.
It joins keywords with " AND " to ensure all terms must be present in the search results.
def formulate_query(keywords, year=None):
    query = " AND ".join(f"({keyword})" for keyword in keywords)
    if year:
        query += f" AND (\"{year}\"[Date - Publication])"
    return query
get_count_for_year_and_target:

This function generates a query for a specific year and target, makes a request to the PubMed API, and returns the count of search results.
It adds the target keyword to the base keywords and constructs the query.
It then sends a request to the PubMed API and extracts the count of results from the JSON response.
def get_count_for_year_and_target(year, target):
    keywords = [target, base_keywords[0], base_keywords[1], base_keywords[2]]
    query = formulate_query(keywords, year)
    params = {
        "db": DB,
        "term": query,
        "retmode": "json"
    }
    response = requests.get(SEARCH_URL, params=params)
    data = response.json()
    return int(data["esearchresult"]["count"])
Main Script
Initialize Results Dictionary:

A dictionary results is created to store the publication counts for each target and year.
results = {year: {} for year in range(2011, 2024)}
Fetch Data:

For each target, the script loops through each year and populates the results dictionary with the publication counts.
for target in targets:
    for year in range(2011, 2024):
        results[year][target] = get_count_for_year_and_target(year, target)
Write Results to CSV:

The script opens a CSV file for writing.
It writes the header row, which includes "Year" and all target methods.
It writes the data rows, each corresponding to a year, followed by the publication counts for each target.
with open(filename, 'w', newline='') as csvfile:
    csvwriter = csv.writer(csvfile)
    header = ['Year'] + targets
    csvwriter.writerow(header)
    for year in results:
        row = [year] + [results[year][target] for target in targets]
        csvwriter.writerow(row)
Final Output:

The script prints a confirmation message indicating where the results have been saved.
print(f"Results saved to {filename}")
Summary
This script automates the process of querying PubMed for the number of publications related to various multiomics analysis methods over a span of years. It organizes this data into a CSV file, making it easier to analyze trends over time.
