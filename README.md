# Data Visualization and Exploration : A User-Friendly Tool Using Streamlit and Plotly




https://github.com/Ramanaathan2000/Phone_pe-Pulse_Data_Visualization/assets/113617103/bfa18a6b-ae2c-49a8-ae3d-5a6fa4feee84






# What is PhonePe Pulse?
The PhonePe Pulse website showcases more than 2000+ Crore transactions by consumers on an interactive map of India. With over 45% market share, PhonePe's data is representative of the country's digital payment habits. The insights on the website and in the report have been drawn from two key sources - the entirety of PhonePe's transaction data combined with merchant and customer interviews. The report is available as a free download on the PhonePe Pulse website and GitHub.


# Libraries and Modules needed for the project!

  1. Plotly - (To plot and visualize the data)
  2. Pandas - (To Create a DataFrame with the scraped data)
  3. postgresql.connector - (To store and retrieve the data)
  4. Streamlit - (To Create Graphical user Interface)!        
  5. json - (To load the json files)
  6. git.repo.base - (To clone the GitHub repository)
     
 
![phonepe](https://github.com/Ramanaathan2000/Phone_pe-Pulse_Data_Visualization/assets/113617103/bddf17ca-1273-41e5-80ee-011bd6e2767d)

# Workflow
# Importing the Libraries:

1.Importing the libraries. As I have already mentioned above the list of libraries/modules needed for the project. First we have to import all those libraries. If the libraries are not installed already use the below piece of code to install.
         !pip install ["Name of the library"]

2.If the libraries are already installed then we have to import those into our script by mentioning the below codes.

import json
import streamlit as st
import pandas as pd
import requests
import psycopg2
import plotly.express as px
import plotly.graph_objects as go
from streamlit_option_menu import option_menu
import os
import json
import git


# Step 2:

# Data extraction:

Clone the Github using scripting to fetch the data from the Phonepe pulse Github repository and store it in a suitable format such as JSON. Use the below syntax to clone the phonepe github repository into your local drive.

         from git.repo.base import Repo
         Repo.clone_from("GitHub Clone URL","Path to get the clouded files")


# Step 3:

# Data transformation:

In this step the JSON files that are available in the folders are converted into the readeable and understandable DataFrame format by using the for loop and iterating file by file and then finally the DataFrame is created. In order to perform this step I've used os, json and pandas packages. And finally converted the dataframe into CSV file and storing in the local drive.

     path1 = "Path of the JSON files"
    agg_trans_list = os.listdir(path1)
# Give any column names that you want
columns1 = {'State': [], 'Year': [], 'Quarter': [], 'Transaction_type': [], 'Transaction_count': [],'Transaction_amount': []}


# Looping through each and every folder and opening the json files appending only the required key and values and creating the dataframe.

for state in agg_trans_list:
    cur_state = path1 + state + "/"
    agg_year_list = os.listdir(cur_state)

    for year in agg_year_list:
        cur_year = cur_state + year + "/"
        agg_file_list = os.listdir(cur_year)

        for file in agg_file_list:
            cur_file = cur_year + file
            data = open(cur_file, 'r')
            A = json.load(data)

            for i in A['data']['transactionData']:
                name = i['name']
                count = i['paymentInstruments'][0]['count']
                amount = i['paymentInstruments'][0]['amount']
                columns1['Transaction_type'].append(name)
                columns1['Transaction_count'].append(count)
                columns1['Transaction_amount'].append(amount)
                columns1['State'].append(state)
                columns1['Year'].append(year)
                columns1['Quarter'].append(int(file.strip('.json')))

df = pd.DataFrame(columns1)

# Converting the dataframe into csv file
        
        df.to_csv('filename.csv',index=False)


# Step 4:

# Database insertion:

To insert the datadrame into POSTGRESQL first I've created a new database and tables using "POSTGRESQL-connector-python" library in Python to connect to a POSTGRESQL database and insert the transformed data using SQL commands.

### sql connection
mydb = psycopg2.connect(host = "localhost",
                        user = "username",
                        password = "password",
                        database = "phonepe_data",
                        port = "5432"
                        )
cursor = mydb.cursor()

# Creating tables:
create_query7= '''CREATE TABLE if not exists table name (States varchar(50),
                                                         Years int,
                                                         Quarter int,
                                                         Insurance_type varchar(50),
                                                         Insurance_count bigint,
                                                         Insurance_amount bigint
                                                         )'''
cursor.execute(create_query7)
mydb.commit()


# Inserting query to insert values into tables
for index,row in table name.iterrows():
    insert_query7 = '''INSERT INTO table name (States, Years, Quarter, Insurance_type, Insurance_count, Insurance_amount)
                                                        values(%s,%s,%s,%s,%s,%s)'''
    values = (row["States"],
              row["Years"],
              row["Quarter"],
              row["Insurance_type"],
              row["Insurance_count"],
              row["Insurance_amount"]
              )
    cursor.execute(insert_query7,values)
    mydb.commit()


# Step 5:

# Dashboard creation:

To create colourful and insightful dashboard I've used Plotly libraries in Python to create an interactive and visually appealing dashboard. Plotly's built-in Pie, Bar, Geo map functions are used to display the data on a charts and map and Streamlit is used to create a user-friendly interface with multiple dropdown options for users to select different facts and figures to display.

#  create_choropleth method and geoJson data

        url= "https://gist.githubusercontent.com/jbrobst/56c13bbbf9d97d187fea01ca62ea5112/raw/e388c4cae20aa53cb5090210a42ebb9b765c0a36/india_states.geojson"
        response= requests.get(url)
        data1= json.loads(response.content)
        states_name_tra= [feature["properties"]["ST_NM"] for feature in data1["features"]]
        states_name_tra.sort()
        

        fig_india_1= px.choropleth(aiyg, geojson= data1, locations= "States", featureidkey= "properties.ST_NM",
                                 color= "Transaction_amount", color_continuous_scale= "Sunsetdark",
                                 range_color= (aiyg["Transaction_amount"].min(),aiyg["Transaction_amount"].max()),
                                 hover_name= "States",title = f"{year} TRANSACTION AMOUNT",
                                 fitbounds= "locations",width =600, height= 600)
        fig_india_1.update_geos(visible =False)
        
        st.plotly_chart(fig_india_1)

# Step 6:
# Data retrieval:
Finally if needed Using the "postgresql-connector-python" library to connect to the postgreSQL database and fetch the data into a Pandas dataframe and displayed my output in streamlit.












