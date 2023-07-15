for this task to complete we require BeautifulSoup ,requests,pandas 
#pip install beautifulsoup4-- comand to install BeautifulSoup 
#import all nesasary module by

# pythonproject
this is data scraping project 

from bs4 import BeautifulSoup
import requests

url='https://www.teaboard.gov.in/WEEKLYPRICES/2023'
page=requests.get(url)
soup=BeautifulSoup(page.text,'html') 
#here we are trying to retrive the HTML content of web page using 'requests'  and 'Beautifulsoup' library 
# we define the url2 variable with the desired URL. Next, we use the requests.get() function to send a GET request to the URL and retrieve the HTML content of the page. Finally, we pass the HTML content (page2.text) to the BeautifulSoup


print(soup)

table=soup.find_all('table')[1] 
#soup.find_all('table') returns a list of all the <table> elements in the HTML content. 
# our data is in table 2 in the page from where we are retriving data  so with considering list we have to use 1 to get desired table
soup.find('table',class_='axr1') #Additionally, if you want to find a table with a specific class name, you can use the find() method with the class_ parameter.
print(table)

column_names=table.find_all('th')# only th table coulumns will be scraped as we mentioned TH tag 
# will return a list of all the <th> elements within the table. Each element represents a column name.

print(column_names)

col=  [title.text.strip() for title in column_names]
print(col)
# it will extract the text content from each <th> element in the column_names list and store them as column names in the col list.  and strip will  remove any leading or trailing whitespace

import pandas as pd # now we will format the data by using pandas
df=pd.DataFrame(columns=col) #it will create an empty DataFrame with the specified column names and print it out. 
df
col_data=table.find_all('tr') # it will return a list of all the <tr> elements within the table, which represents the table rows.

for row in col_data[1:]: # the 1st row is empty so we will start with 2nd
    row_data=row.find_all('td') #It then extracts the <td> elements within each row using the find_all('td')
    individual_row_data=[data.text.strip()for data in row_data] # The text content of each <td> element is stored in the individual_row_data list. 
    print(individual_row_data)
    length=len(df)
    df.loc[length]=individual_row_data#Finally, the individual_row_data list is added as a new row to the DataFrame df using the loc method.
    
df

df['location']='kolkata-'+df['Kolkata']+'Guwahati-'+df['Guwahati']+df['Guwahati']+'Siliguri-'+'Jalpaiguri-'+df['Jalpaiguri']+'mjunction'+df['mjunction']+'Cochin'+df['Cochin']+'Coonoor'+df['Coonoor']+'Coimbatore-'+df['Coimbatore'] # out task is to create single column (location ) so we will concate all the unnecessary columns into single 
df 


# similarly as aboue we are doing same process for year 2022 and will create datafreame2 as df2

url2='https://www.teaboard.gov.in/WEEKLYPRICES/2022'  
page2=requests.get(url2)
soup2=BeautifulSoup(page2.text,'html')

table2=soup2.find_all('table')[1]
column_names2=table2.find_all('th')
col2=  [title.text.strip() for title in column_names2]
print(col2)

df2=pd.DataFrame(columns=col2)
col_data2=table2.find_all('tr')

for row in col_data2[1:]:
    row_data2=row.find_all('td')
    individual_row_data2=[data.text.strip()for data in row_data2]
    print(individual_row_data2)
    length2=len(df2)
    df2.loc[length2]=individual_row_data2
df2

df2['location']='kolkata-'+df2['Kolkata']+'Guwahati-'+df2['Guwahati']+df2['Guwahati']+'Siliguri-'+'Jalpaiguri-'+df2['Jalpaiguri']+'mjunction'+df2['mjunction']+'Cochin'+df2['Cochin']+'Coonoor'+df2['Coonoor']+'Coimbatore-'+df2['Coimbatore']


# in df_final we are storing both data freames in single that is data from year 2023 and year 2022
df_final = pd.concat([df, df2], ignore_index=True)
df_final # we have to ignore index of 2nd df so i'll continue with single index

df_final= df_final.rename(columns={'Week Ending/Date': 'week', 'Tea Serve': 'average_prize'}) # we will rename the columns in the required format

df_final.to_csv(r'C:\Users\shivk\Desktop\ETL\pandas\Bharti_Institute_of_Public_Policy_task1.csv', columns=["week","location","average_prize"], index=False) # exporting to the csv file with name 'Bharti_Institute_of_Public_Policy_task1'.and we are done
