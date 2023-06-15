# Exploratory Analysis of Geolocational Data

This repository contains code for performing exploratory analysis of geolocational data and running K Means clustering on the data to recommend accommodations to immigrants in a city.

## Code Description

The code is written in Python and is provided in the Jupyter Notebook format (Exploratory_Analysis_of_Geolocational_Data.ipynb). The notebook consists of the following sections:

### Importing Libraries

The required libraries for this project are imported in this section.

### Fetching Data

The code fetches the data from a CSV file and converts it into a pandas DataFrame.

### Cleaning and Visualizing the Data

This section performs exploratory data analysis (EDA) on the DataFrame and uses various visualization techniques to understand the dataset. The code cleans the data by selecting specific columns and removing any missing values.

### Performing KMeans Clustering on the Data

The code performs K Means clustering on the selected columns of the cleaned dataset. It determines the optimal number of clusters using the elbow method and visualizes the distortion values.

### Getting Geolocation of the Data

The code uses the Foursquare API to retrieve geolocational data for the given coordinates. It fetches nearby venues and adds additional columns to the DataFrame.

### Plotting the Clustered Locations on a Map

The code runs K Means clustering on the geolocational data and plots the clustered locations on a map using the Folium library. It assigns colors and sizes to the markers based on the restaurant and other categories.

## Usage

To run the code, follow these steps:

1. Install the required libraries mentioned in the notebook.
2. Download the provided CSV file ("food_coded.csv") and place it in the appropriate location.
3. Open the Jupyter Notebook file "Exploratory_Analysis_of_Geolocational_Data.ipynb" in a Jupyter Notebook environment.
4. Execute each cell in the notebook sequentially.

Please note that you need to have the necessary API credentials for the Foursquare API to fetch geolocational data successfully.

Feel free to explore and modify the code according to your requirements. Happy coding!
