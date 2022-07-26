
# Exploratory Analysis of Geolocational Data 🌍

This project involves the use of K-Means Clustering to find the best accommodation for students in Bangalore or Pune, (or any other city of your choice) by classifying accommodation for incoming students on the basis of their preferences on amenities, budget and proximity to the location.


## Project Outcome
You should have a map centered on the location you chose, with the locations differentiated
by colour scheme. Have a look at the screenshot below for reference.

![Screenshot (23)](https://user-images.githubusercontent.com/102010266/180993792-b045a4d7-829d-4ac3-b935-e930e77116c4.png)

## Project Flow 📈

![Screenshot (25)](https://user-images.githubusercontent.com/102010266/180993924-e85a40e3-52cc-4ef5-a3e0-2d05f29aa43e.png)

## How to run the code?🏃‍♂️
1. Used Visual Studio Code or Google Colab to execute this project.
2. Ensure that all the required packages been installed.
3. Download the ipynb and store it on jupyter directory.
4. ipynb can run each cell at time or can be run completely in on go.
## Reference🔗

- Head over to [this]('https://www.kaggle.com/datasets/borapajo/food-choices') link to get a dataset we'll be using. If you run into "too many requests", here's a [drive link]('https://drive.google.com/drive/folders/1TbRjwBz3Pvy0Fn8t__DUmZgtj21ttDhn').
- Note that you'll need both files, the csv and the document the document explains the meaning of the CSV values.
- You will need a jupyter notebook installed in your environment.
## Requriements🔨
Following Libraries are requried for this projects are

        from pandas.io.json import json_normalize
        import json
        import numpy as np
        import torch
        import seaborn as sns
        import matplotlib.pyplot as plt
        %matplotlib inline
        import folium
        import geopy
        from sklearn import preprocessing, cluster
        import scipy
        from geopy.geocoders import Nominatim 
        import requests
## Explanation of the code🔎

#### Getting Started
We need data to do data analysis! Fetch the data we need and set up your environment
before you move on to data analysis.

        df=pd.read_csv("/content/food_coded.csv")
        print('Shape of Food Data:', df.shape)
        df.head()

#### Data Exploration and Visualisation
Now that we have our data, we need to understand it. A good way to do this is by visualising
the data via graphs. Graphs help us quickly get a sense of the data, and are a much more
user-friendly way of understanding data as compared to reading thousands of rows of data!

        cols=['cook',
        'eating_out',
        'employment',
        'ethnic_food', 
        'exercise','fruit_day',
        'income','on_off_campus',
        'pay_meal_out',
        'sports',
        'veggies_day'
        ]

        clean_df  = df[cols]

        clean_df.boxplot(figsize = (16, 4))
        plt.show()

#### Run KMeans Clustering on the data
K Means Clustering will help us group locations based on the amenities located around
them.

        var=['cook','income']
        X = clean_df[var]
        max_k = 10

        ## iterations
        distortions = [] 
        for i in range(1, max_k+1):
        if len(X) >= i:
        model = cluster.KMeans(n_clusters=i, init='k-means++', max_iter=300, n_init=10, random_state=0)
        model.fit(X)
        distortions.append(model.inertia_)

        ## best k: the lowest derivative
        k = [i*100 for i in np.diff(distortions,2)].index(min([i*100 for i 
        in np.diff(distortions,2)]))

        ## plot
        fig, ax = plt.subplots()
        ax.plot(range(1, len(distortions)+1), distortions)
        ax.axvline(k, ls='--', color="orange", label="k = "+str(k))
        ax.set(title='The Elbow Method', xlabel='Number of clusters', 
        ylabel="Distortion")
        ax.legend()
        ax.grid(True)
        plt.show()

#### Get Geolocational Data from Foursquare API
Now that we know the best K value for our population, we need to get geolocational data
from the Foursquare API to find these people some accomodation!

        resta=[]
        oth=[]
        for lat,long in zip(nearby_venues['venue.location.lat'],nearby_venues['venue.location.lng']):
        url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
        CLIENT_ID, 
        CLIENT_SECRET, 
        VERSION, 
        lat,long,
        1000, 
        100)
        res = requests.get(url).json()
        venue = res['response']['groups'][0]['items']
        nearby_venue = json_normalize(venue)
        df=nearby_venue['venue.categories']

        g=[]
        for i in range(0,df.size):
        g.append(df[i][0]['icon']['prefix'].find('food'))
        co=0
        for i in g:
        if i>1:
                co+=1
        resta.append(co)
        oth.append(len(g)-co)

        nearby_venues['restaurant']=resta
        nearby_venues['others']=oth

        n=nearby_venues.drop(['referralId', 'reasons.count', 'reasons.items', 'venue.id',
       'venue.name', 
       'venue.location.labeledLatLngs', 'venue.location.distance',
       'venue.location.cc', 
       'venue.categories', 'venue.photos.count', 'venue.photos.groups',
       'venue.location.crossStreet', 'venue.location.address','venue.location.city',
       'venue.location.state', 'venue.location.crossStreet',
       'venue.location.neighborhood',	'venue.venuePage.id',
       'venue.location.postalCode','venue.location.country'],axis=1)

       n.head()

#### Plot the clustered locations on a map
Now it's time to run K Means clustering on the data (finally!) and plot the results on a map.

        x, y = "lat", "long"
        color = "restaurant"
        size = "others"
        popup = "venue.location.formattedAddress"
        data = n.copy()

        ## create color column
        lst_colors=["red","green","orange"]
        lst_elements = sorted(list(n[color].unique()))

        ## create size column (scaled)
        scaler = preprocessing.MinMaxScaler(feature_range=(3,15))
        data["size"] = scaler.fit_transform(
                data[size].values.reshape(-1,1)).reshape(-1)

        ## initialize the map with the starting location
        map_ = folium.Map(location=location, tiles="cartodbpositron",
                        zoom_start=11)
        ## add points
        data.apply(lambda row: folium.CircleMarker(
                location=[row[x],row[y]],popup=row[popup],
                radius=row["size"]).add_to(map_), axis=1)
        ## add html legend


        ## plot the map
        map_



## Applications
K-Means clustering is used in a variety of examples or business cases in real life, like:
- Academic performance (grouping students by their learning rate)
-  Diagnostic systems (grouping system faults under various reasons)
- Search engines (grouping search results)
- Wireless sensor networks (Mapping networks)

The FourSquare API data can be used for:
- Building a restaurant review app like Swiggy Zomato etc.
- Supporting a ride sharing service like Uber Pool
