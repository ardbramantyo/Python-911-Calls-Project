# Data Capstone Project –  911 Calls

## Overview:
911 Calls (Fire, Traffic, EMS) had made from Montgomery County, PA from 2015-2020 and the records contain the following fields:

![image](https://user-images.githubusercontent.com/37673834/168406846-f5f509fe-90d5-4647-961d-abb54241630c.png)

Based on analysis using df.info(), the data contain 663522 rows with 9 columns, there are:
####    • lat: String variable, Latitude
####    • lng: String variable, Longitude
####    •	desc: String variable, Description of the Emergency Call
####    •	zip: String variable, Zipcode
####    •	title: String variable, Title
####    •	timeStamp: String variable, YYYY-MM-DD HH:MM
####    •	twp: String variable, Township
####    •	addr: String variable, Address
####    •	e: String variable, Dummy variable (always 1)

Further analysis was conducted to get insight on top 5 zipcodes for 911 calls using:
```ruby
df['zip'].value_counts().head(5)
```
The results are:

![image](https://user-images.githubusercontent.com/37673834/168406920-41f870cf-c5f2-4704-8081-a01ce132d051.png)

Then another analysis to get top 5 townships (twp) for 911 calls information using:
```ruby df['twp'].value_counts().head(5) ```

![image](https://user-images.githubusercontent.com/37673834/168407074-e3f4dd49-070e-4579-9e64-462b6a6dec77.png)

To check the total of unique title codes, I used:
df['title'].nunique()
and there are 148 unique title codes.

### Creating new features
In the titles column there are "Reasons/Departments" specified before the title code. These are EMS, Fire, and Traffic. A new column called "Reason" was created using .apply() with a custom lambda expression that contains this string value. For example, if the title column value is EMS: BACK PAINS/INJURY, then the Reason column value would be EMS. The result is:

![image](https://user-images.githubusercontent.com/37673834/168407099-4bdb5980-a845-408c-8940-9f147fb225f1.png)

Then based on new column “Reason”, to check the most common reason for a 911 call using:
df['Reason'].value_counts().head(1)

![image](https://user-images.githubusercontent.com/37673834/168407165-1d6c4941-ce36-48c9-b7a6-d778033b0a91.png)

Now, to plot 911 Calls by region I used Seaborn package and create a countplot.
The code : sns.countplot(x='Reason',data=df)

![image](https://user-images.githubusercontent.com/37673834/168407195-a15737d8-8dca-4651-9c73-4744dc80dd08.png)

Next, I focused on time information. I checked the data type of the objects in the timestamp column using df.info(). And the result:

![image](https://user-images.githubusercontent.com/37673834/168407202-fd4b3507-c396-4c2d-adfa-e9ccbbb1c934.png)

The result for data type in the timestamp column is object and it’s not a number, so actually it’s just a string. So I check the validity to one of those objects using:
type(df['timeStamp'].iloc[0]) and the result is:

![image](https://user-images.githubusercontent.com/37673834/168407213-7942b840-aa72-4358-8734-86b61ae923c6.png)

Then I turned the data type in the timestamp column from string into DateTime objects.

![image](https://user-images.githubusercontent.com/37673834/168407224-08b26a3a-7a93-412d-b2b5-f240d74336e2.png)

After get dateTime, I can get specific attribute by calling them. For example:
Time = df[‘timeStamp’].iloc[0]
Time.hour
So, basically we can use Jupyter’s tab method to explore the various attributes we can call. Now that the timestamp column are actually DateTime objects, I use .apply() to create 3 new columns called Hour, Month, and Day of week. I created these columns based off of the timestamp column. 
df['Hour'] = df['timeStamp'].apply(lambda time: time.hour)
df['Month'] = df['timeStamp'].apply(lambda time: time.month)
df['Day of Week'] = df['timeStamp'].apply(lambda time: time.dayofweek)
The new data will become like this:

![image](https://user-images.githubusercontent.com/37673834/168407243-f06236af-1adb-4814-b4d1-b187240ad2c5.png)

The next thing I wanted to notice was how the day of week is integer from 0 to 6. Then I use the .map() with this dictionary to map the actual string names to the day of the week:
dmap = {0:’Mon’,1:’Tue’,2:’Wed’,3:’Thu’,4:’Fri’,5:’Sat’,6:’Sun’}
df['Day of Week'] = df['Day of Week'].map(dmap)
And the new data will look like this:

![image](https://user-images.githubusercontent.com/37673834/168407262-ac7919af-0701-4d2b-91cf-469b2be38660.png)

Now I want to see more and create a counter plot of the Day of Week column with the hue based off of the Reason column with:
sns.countplot(x='Day of Week',data=df)

![image](https://user-images.githubusercontent.com/37673834/168407284-6a284bc5-206a-4388-9201-6462a35a5b2b.png)

We can see that there’s a bit of drop on Sunday. We can add hue by Reason so we can recreate the plot into these:

![image](https://user-images.githubusercontent.com/37673834/168407296-aeec74d1-c1a3-47a2-9023-35e4edf03966.png)

I changed color palette to viridis and the plot become like this:

![image](https://user-images.githubusercontent.com/37673834/168407303-8c54dd15-58e0-4cd8-adff-d3d363e6c07c.png)

And the last thing to note is that the legend is actually inside the plot, so I relocate the legend using:
plt.legend(bbox_to_anchor=(1.05,1),loc=2,borderaxespad=0)

![image](https://user-images.githubusercontent.com/37673834/168407319-8111c525-3e00-49cc-9917-e9d6b77b4aba.png)

Now, I do the exact same step with the month column and the result is this plot bellow:

![image](https://user-images.githubusercontent.com/37673834/168407328-274580ec-a129-4d70-aaa4-77a03a80e530.png)

Next I created group by Month:

![image](https://user-images.githubusercontent.com/37673834/168407336-c6ae8eed-0ef7-4113-afe1-1eda2a570e7b.png)

Next, I created seaborn’s lmplot() to create a linear fit on the number of calls per month. First I must reset the byMonth column to create this plot:
Reset: byMonth.reset.index()
sns.lmplot(x='Month',y='twp',data=byMonth.reset_index())
And I get 

![image](https://user-images.githubusercontent.com/37673834/168407346-bb1bddd5-563f-4461-9075-90e6a2b4bb9a.png)

From linear fit, generally we can see that as far as what Seaborn have plot month in this model that number of calls goes down from month-1 to month-12. Seaborn also shows shaded area indicating eror and the errors basically grows as you go into these months. This trend is mixed because the data consist multiple year.
To create time series plot, first I can extract just only date from timestamp column to new column called Date using this command:
df['Date'] = df['timeStamp'].apply(lambda t:t.date())

![image](https://user-images.githubusercontent.com/37673834/168407352-7660b8f1-cb38-4d1a-be79-79fa6928303e.png)

I’m using df.groupby('Date').count().head() to make Date column as the index so all the calls from the same date will add to one date as count.
I tried to using
df.groupby('Date').count()['lat'].plot(figsize=(14,3))
plt.tight_layout()

to get some information from the plot:

![image](https://user-images.githubusercontent.com/37673834/168407363-35090be0-e0a0-44cd-ab69-d8bbb01cc6ec.png)

We can notice that it looks like there are some significant spikes in the beginning and late of 2018, and something in 2020. I do the same thing on every Reason:

![image](https://user-images.githubusercontent.com/37673834/168407372-14eac975-2726-4802-b409-c3b6980cdee5.png)
![image](https://user-images.githubusercontent.com/37673834/168407378-db217395-c1f9-4c5a-ae40-265e1184f2d2.png)
![image](https://user-images.githubusercontent.com/37673834/168407394-f568654e-91e3-46f8-8e6b-6e61ef404d81.png)

Next is creating heating map. First I create groupby as variable dayHour:
dayHour = df.groupby(by=['Day of Week','Hour']).count()['Reason'].unstack()

![image](https://user-images.githubusercontent.com/37673834/168407422-64f7ca84-ce2e-41b2-936b-b86f4a0d8963.png)

After that, I create Heat Map using:
plt.figure(figsize=(18,6))
sns.heatmap(dayHour, cmap='viridis')

![image](https://user-images.githubusercontent.com/37673834/168407442-684ca01f-e8c4-4628-8932-cc946446fae5.png)
![image](https://user-images.githubusercontent.com/37673834/168407454-464a6a75-199b-4fef-ba2a-70c35851677d.png)

Then, I created the same variable using Date of Week and Month to get this information:

![image](https://user-images.githubusercontent.com/37673834/168407469-d31bd581-ab9c-4fe3-a643-60032295b3b6.png)
![image](https://user-images.githubusercontent.com/37673834/168407479-c9133300-a2fc-4fb1-958d-5d91e632b5e5.png)

## REFERENCE
1. https://www.kaggle.com/datasets/mchirico/montcoalert
