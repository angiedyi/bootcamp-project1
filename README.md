```python
#import all the necessary libraries
from random import random #random library
import matplotlib.pyplot as plt #graphing library
import requests as req #api requesting library
import pandas as pd #dataframe tools library
import seaborn #graphic visualization library
import json
import numpy as np
from collections import Counter
```
```python
api_key = "1cb8c986583bd3f3e6a53ab801b9ab15"
api_secret = "530fd7db491ca69eeb95dfd46232b35e"
url = "http://api.petfinder.com/"
```
```python
shelterfind_query_url = url + "shelter.find?" + "key=" + api_key + "&count=1" + "&name=animal" "&format=json" + "&location="
shelter_getpets_url = url + "shelter.getPets?" + "key=" + api_key + "&format=json" + "&count=200" + "&output=id" + "&id="
pets_byid_url = url + "pet.get?" + "key=" + api_key + "&format=json" + "&id="
```
```python
with open("california_cities.csv") as cities:
    cities_reader = csv.reader(cities)
    
    cities = [city[0] for city in cities_reader]
    
    cities = cities[3:7]
    print(cities)
    
    #example url: http://api.petfinder.com/shelter.find?key=1cb8c986583bd3f3e6a53ab801b9ab15&location=san%20francisco,ca&format=json

    shelter_find_query = [req.get(shelterfind_query_url + city + ",ca").json() for city in cities]
    print(json.dumps(shelter_find_query, indent=2))
```
```python
shelter_id_data_full = [data.get("petfinder").get("shelters").get("shelter").get("id").get("$t") for data in shelter_find_query]
```
```python
len(shelter_id_data_full)

shelter_getpets_data = [req.get(shelter_getpets_url + shelter).json() for shelter in shelter_id_data_full]
```
```python
pet_id_data_full = [data.get("petfinder").get("petIds").get("id") for data in shelter_getpets_data]
```
```python
sanjose_petids_list = pet_id_data_full[0]
sanfrancisco_petids_list = pet_id_data_full[1]
sacramento_petids_list = pet_id_data_full[2]
oakland_petids_list = pet_id_data_full[3]

#print(sanfrancisco_petids_list)
#testing to see if it'll print out the petids specific to city

sanjose_petids = [number["$t"] for number in sanjose_petids_list]
sanfrancisco_petids = [number["$t"] for number in sanfrancisco_petids_list]
sacramento_petids = [number["$t"] for number in sacramento_petids_list]
oakland_petids_list = [number["$t"] for number in oakland_petids_list]
```
```python
pets_byid_sanjose = [req.get(pets_byid_url + petid).json() for petid in sanjose_petids]
pets_byid_sanfrancisco = [req.get(pets_byid_url + petid).json() for petid in sanfrancisco_petids]
pets_byid_sacramento = [req.get(pets_byid_url + petid).json() for petid in sacramento_petids]
pets_byid_oakland = [req.get(pets_byid_url + petid).json() for petid in oakland_petids]
```
```python
type_animal_sanjose = [data.get("petfinder").get("pet").get("animal").get("$t") for data in pets_byid_sanjose]
type_animal_sanfrancisco = [data.get("petfinder").get("pet").get("animal").get("$t") for data in pets_byid_sanfrancisco]
type_animal_sacramento = [data.get("petfinder").get("pet").get("animal").get("$t") for data in pets_byid_sacramento]
type_animal_oakland = [data.get("petfinder").get("pet").get("animal").get("$t") for data in pets_byid_oakland]

sanjose_dogs = type_animal_sanjose.count("Dog")
sanjose_cats = type_animal_sanjose.count("Cat")
sanjose_others = len(type_animal_sanjose) - (sanjose_cats + sanjose_dogs)
sanjose_animal_numbers = [sanjose_dogs, sanjose_cats, sanjose_others]

sanfrancisco_dogs = type_animal_sanfrancisco.count("Dog")
sanfrancisco_cats = type_animal_sanfrancisco.count("Cat")
sanfrancisco_others = len(type_animal_sanfrancisco) - (sanfrancisco_cats + sanfrancisco_dogs)
sanfrancisco_animal_numbers = [sanfrancisco_dogs, sanfrancisco_cats, sanfrancisco_others]

sacramento_dogs = type_animal_sacramento.count("Dog")
sacramento_cats = type_animal_sacramento.count("Cat")
sacramento_others = len(type_animal_sacramento) - (sacramento_cats + sacramento_dogs)
sacramento_animal_numbers = [sacramento_dogs, sacramento_cats, sacramento_others]

oakland_dogs = type_animal_oakland.count("Dog")
oakland_cats = type_animal_oakland.count("Cat")
oakland_others = len(type_animal_oakland) - (oakland_cats + oakland_dogs)
oakland_animal_numbers = [oakland_dogs, oakland_cats, oakland_others]


type_animal_data = pd.DataFrame({
    "Animal Types": ["Dogs", "Cats", "Other"],
    "San Jose": sanjose_animal_numbers,
    "San Francisco": sanfrancisco_animal_numbers,
    "Sacramento": sacramento_animal_numbers,
    "Oakland": oakland_animal_numbers
})
```
```python
labels = ["Dogs", "Cats", "Other"]
sizes = [oakland_dogs, oakland_cats, oakland_others]
colors = ["yellowgreen", "orange", "lightskyblue"]
# how big is the explosion, and what should explode?
explode = (0, .1, 0)

# the first two %'s format the number. The last one is what is displayed on the graph
plt.pie(sizes, explode=explode, labels=labels, colors=colors,
        autopct="%.1f%%", shadow=True, startangle=0)
plt.title("Oakland Animal Services (% by Animal Type)")
plt.axis("equal") # doesn't stretch when equal
plt.savefig("oakland_animaltype.png")
plt.show()
```
```python
labels = ["Dogs", "Cats", "Other"]
sizes = [sacramento_dogs, sacramento_cats, sacramento_others]
colors = ["yellowgreen", "orange", "lightskyblue"]
# how big is the explosion, and what should explode?
explode = (0, .1, 0)

# the first two %'s format the number. The last one is what is displayed on the graph
plt.pie(sizes, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=0)
plt.title("City of Sacramento Animal Care Services (% by Animal Type)")
plt.axis("equal") # doesn't stretch when equal
plt.savefig("sacramento_animaltype.png")
plt.show()
```
```python
labels = ["Dogs", "Cats", "Other"]
sizes = [sanfrancisco_dogs, sanfrancisco_cats, sanfrancisco_others]
colors = ["yellowgreen", "orange", "lightskyblue"]
# how big is the explosion, and what should explode?
explode = (0, .1, 0)

# the first two %'s format the number. The last one is what is displayed on the graph
plt.pie(sizes, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=0)
plt.title("SF Animal Care & Control (% by Animal Type)")
plt.axis("equal") # doesn't stretch when equal
plt.savefig("sf_animaltype.png")
plt.show()
```
```python
labels = ["Dogs", "Cats", "Other"]
sizes = [sanjose_dogs, sanjose_cats, sanjose_others]
colors = ["yellowgreen", "orange", "lightskyblue"]
# how big is the explosion, and what should explode?
explode = (0, .1, 0)

# the first two %'s format the number. The last one is what is displayed on the graph
plt.pie(sizes, explode=explode, labels=labels, colors=colors,
        autopct="%.1f%%", shadow=True, startangle=0)
plt.axis("equal") # doesn't stretch when equal
plt.title("City of San Jose Animal Shelter (% by Animal Type)")
plt.savefig("sanjose_animaltype.png")
plt.show()
```
```python
#Analytical Calculations and Observations
#using resources from: https://www.aspca.org/animal-homelessness/shelter-intake-and-surrender/pet-statistics
#44% of dogs are euthanized, 56% of cats are euthanized
cat_percentages = [70.2, 30.8, 45, 51.7]
dog_percentages = [17, 69.2, 20, 31.5]
other_percentages = [12.8, 0, 35, 16.8]

cat_euth_rate = .56
dog_euth_rate = .44

cat_average = np.mean(cat_percentages)
#On average, there are about 49.4% cats in these Northern California shelters
dog_average = np.mean(dog_percentages)
#On average, there are about 34.4% dogs in these Northern California shelters

all_cats = sanjose_cats + sacramento_cats + oakland_cats + sanfrancisco_cats
print(all_cats)
all_dogs = sanjose_dogs + sacramento_dogs + oakland_dogs + sanfrancisco_dogs
print(all_dogs)

euthanized_cats = all_cats * cat_euth_rate
print(euthanized_cats)
#87 out of the 156 will be euthanized
euthanized_dogs = all_dogs * dog_euth_rate
#51 out of the 116 will be euthanized
print(euthanized_dogs)
```
