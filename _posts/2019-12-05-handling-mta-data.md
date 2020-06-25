---
title: Handling MTA Turnstile Data
date: 2019-12-05 10:10:00 -0500
categories: [Tech Blog, Code Demo]
tags: [data handling]
---

## Abstract

MTA (Subway operator of NYC) provides passenger flow data based on the record of each turnstile. Wait, what is a turnstile? It's something like this:

| ![](/assets/img/tech-blog/mta/turnstile.jpg) | 
|:--:| 
| *Fig 1. Turnstiles, source: [http://www.turnstile.com/](http://www.turnstile.com/)* |

Imagine that each station has tens of turnstiles and each line has tens of stations, isn't it hard to harness these data for analysis? Don't be panic, in this notebook, we will go through the following items:
- Read MTA turnstile data
- Find turnstile data by line name (or it can be easily tailored to station names)
- Calculate entries and exits for each station
- Visualize daily entry/exit dynamics for your chosen stations

## Read MTA Turnstile Data

{% highlight python%}
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sb
from collections import defaultdict
pd.options.display.float_format = '{:.2f}'.format
%matplotlib inline
{% endhighlight %}
{% highlight python%}
# 1 sample week. 
# MTA uses the data on each Saturday, e.g 10/05/2019, to name their data file.
week = 191005

# url of data source
url = 'http://web.mta.info/developers/data/nyct/turnstile/turnstile_{}.txt'

df_sample = pd.read_csv(url.format(week)) 
df_sample.head()
{% endhighlight %}
<style scoped="">
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
	font-size: 0.75em;
    }
    .dataframe tbody td {
	font-size: 0.75em;
    }
</style>
<table class="dataframe" border="0" frame = "vside">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>C/A</th>
      <th>STATION</th>
      <th>LINENAME</th>
      <th>DATE</th>
      <th>TIME</th>
      <th>ENTRIES</th>
      <th>EXITS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>A002</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>09/28/2019</td>
      <td>00:00:00</td>
      <td>7215740</td>
      <td>2444319</td>
    </tr>
    <tr>
      <td>1</td>
      <td>A002</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>09/28/2019</td>
      <td>04:00:00</td>
      <td>7215766</td>
      <td>2444322</td>
    </tr>
    <tr>
      <td>2</td>
      <td>A002</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>09/28/2019</td>
      <td>08:00:00</td>
      <td>7215788</td>
      <td>2444357</td>
    </tr>
    <tr>
      <td>3</td>
      <td>A002</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>09/28/2019</td>
      <td>12:00:00</td>
      <td>7215862</td>
      <td>2444436</td>
    </tr>
    <tr>
      <td>4</td>
      <td>A002</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>09/28/2019</td>
      <td>16:00:00</td>
      <td>7216108</td>
      <td>2444474</td>
    </tr>
  </tbody>
</table>

Each row in the records represents one turnstile in a station. The <span style="color:red">"ENTRIES"</span> and <span style="color:red">"EXITS"</span> column are count of total entries and exits since this turnstile is installed. They are provided on a 4-hour per record basis. Given this way, for example, if we want to calculate how many entries happen within a day, we can use today's record at midnight to minus the record at midnight of the previous day. A comprehensive explaination could be found on MTA's [website](http://web.mta.info/developers/resources/nyct/turnstile/ts_Field_Description.txt).

## Find Turnstile Data by Line Name

Alright, let's start with a simple application: Find out how many entries happen in each A line station at the weeks of 10/05/2019, 10/12/2019, and 10/19/2019.

{% highlight python%}
weeks = [191005, 191012, 191019]
lines = ['A'] # we write code capable of handling multiple lines
df_list = []

for week in weeks:
    df_temp = pd.read_csv(url.format(week))    
    ind = (df_temp['UNIT'] == None)
    print('week: ', week) # print out weeks to know how far we have gone
    for line in lines:
        ind = (ind | df_temp['LINENAME'].str.contains(line))
        print('  line: ', line) # also print out lines to know how far we have gone
    df_list.append(df_temp.loc[ind])

mta_df = pd.concat(df_list)
mta_df.sort_values(by = ['STATION'], inplace = True) # group by station name
mta_df.head()
{% endhighlight %}
<style scoped="">
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
	font-size: 0.75em;
    }
    .dataframe tbody td {
	font-size: 0.75em;
    }
</style>
<table class="dataframe" border="0" frame = "vside">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>C/A</th>
      <th>STATION</th>
      <th>LINENAME</th>
      <th>DATE</th>
      <th>TIME</th>
      <th>ENTRIES</th>
      <th>EXITS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>70148</td>
      <td>N137</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/15/2019</td>
      <td>20:00:00</td>
      <td>1681115231</td>
      <td>978345287</td>
    </tr>
    <tr>
      <td>69605</td>
      <td>N137</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/04/2019</td>
      <td>08:00:00</td>
      <td>301455</td>
      <td>191435</td>
    </tr>
    <tr>
      <td>69604</td>
      <td>N137</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/04/2019</td>
      <td>04:00:00</td>
      <td>301164</td>
      <td>191418</td>
    </tr>
    <tr>
      <td>69603</td>
      <td>N137</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/04/2019</td>
      <td>00:00:00</td>
      <td>301155</td>
      <td>191403</td>
    </tr>
    <tr>
      <td>69602</td>
      <td>N137</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/03/2019</td>
      <td>20:00:00</td>
      <td>301130</td>
      <td>191322</td>
    </tr>
  </tbody>
</table>

Since data is recorded each 4 hours, we want to collapse them into a one-day level, i.e. merge same day records to one record.

{% highlight python%}
# This section calculates No. of Entries and Exits...
# for each turnstile at a same-day level
temp_d = defaultdict(list) # for Entries
temp_d1 = defaultdict(list) # for Exits
turnstile_d = {}
turnstile_d1 = {}

# Collapse data to a same-day level
for row in mta_df.itertuples():
    C_A, unit, scp, station, linename, date =\
	row[1], row[2], row[3], row[4], ''.join(sorted(row[5])), row[7]
    entries = row[10]
    exits = row[11]
	# Create a unique key for each turnstile at each day
    k = (C_A, unit, scp, station, linename, date)
	# Record entries and exits of each record at the same day
    temp_d[k].append(entries) 
    temp_d1[k].append(exits)
    
for key, value in temp_d.items():
    entry = abs(max(value) - min(value)) # (max - min) of a day
    turnstile_d[key] = [entry]
for key, value in temp_d1.items():
    exit = abs(max(value) - min(value)) # (max - min) of a day
    turnstile_d1[key] = [exit]

# form dataframes from dictionaries
dict_df = pd.DataFrame.from_dict(turnstile_d, orient='index')
dict_df.rename(columns = {0:'Entries'}, inplace=True)
dict_df1 = pd.DataFrame.from_dict(turnstile_d1, orient='index')
dict_df1.rename(columns = {0:'Exits'}, inplace=True)

dict_df['Exits'] = dict_df1['Exits']

# take a look of what we obtain
dict_df.head()
{% endhighlight %}

<table class="dataframe" border="0" frame = "vside">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Entries</th>
      <th>Exits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>(N137, R354, 00-06-01, 104 ST, A, 10/15/2019)</td>
      <td>289</td>
      <td>11</td>
    </tr>
    <tr>
      <td>(N137, R354, 00-00-00, 104 ST, A, 10/04/2019)</td>
      <td>689</td>
      <td>269</td>
    </tr>
    <tr>
      <td>(N137, R354, 00-00-00, 104 ST, A, 10/03/2019)</td>
      <td>685</td>
      <td>379</td>
    </tr>
    <tr>
      <td>(N137, R354, 00-00-00, 104 ST, A, 10/02/2019)</td>
      <td>720</td>
      <td>352</td>
    </tr>
    <tr>
      <td>(N137, R354, 00-00-00, 104 ST, A, 10/01/2019)</td>
      <td>603</td>
      <td>352</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>(N094, R029, 01-03-04, WORLD TRADE CTR, 23ACE, 10/07/2019)</td>
      <td>529</td>
      <td>214</td>
    </tr>
    <tr>
      <td>(N094, R029, 01-03-04, WORLD TRADE CTR, 23ACE, 10/08/2019)</td>
      <td>572</td>
      <td>196</td>
    </tr>
    <tr>
      <td>(N094, R029, 01-03-03, WORLD TRADE CTR, 23ACE, 10/09/2019)</td>
      <td>430</td>
      <td>372</td>
    </tr>
    <tr>
      <td>(N094, R029, 01-03-03, WORLD TRADE CTR, 23ACE, 10/10/2019)</td>
      <td>446</td>
      <td>493</td>
    </tr>
    <tr>
      <td>(N094, R029, 01-03-03, WORLD TRADE CTR, 23ACE, 10/11/2019)</td>
      <td>493</td>
      <td>465</td>
    </tr>
  </tbody>
</table>

We just collasped the index column for statistical purpose, now let's expand it again.

{% highlight python%}
# convert back to original format
turnstile_df = pd.DataFrame(columns=[])
turnstile_df['CA'] = [row[0][0] for row in dict_df.itertuples()]
turnstile_df['unit'] = [row[0][1] for row in dict_df.itertuples()]
turnstile_df['scp'] = [row[0][2] for row in dict_df.itertuples()]
turnstile_df['station'] = [row[0][3] for row in dict_df.itertuples()]
turnstile_df['linename'] = [row[0][4] for row in dict_df.itertuples()]
turnstile_df['date'] = [row[0][5] for row in dict_df.itertuples()]
turnstile_df['entries'] = [row[1] for row in dict_df.itertuples()]
turnstile_df['exits'] = [row[2] for row in dict_df.itertuples()]
turnstile_df.head()
{% endhighlight %}

<table class="dataframe" border="0" frame = "vside">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CA</th>
      <th>unit</th>
      <th>scp</th>
      <th>station</th>
      <th>linename</th>
      <th>date</th>
      <th>entries</th>
      <th>exits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>N137</td>
      <td>R354</td>
      <td>00-06-01</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/15/2019</td>
      <td>289</td>
      <td>11</td>
    </tr>
    <tr>
      <td>1</td>
      <td>N137</td>
      <td>R354</td>
      <td>00-00-00</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/04/2019</td>
      <td>689</td>
      <td>269</td>
    </tr>
    <tr>
      <td>2</td>
      <td>N137</td>
      <td>R354</td>
      <td>00-00-00</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/03/2019</td>
      <td>685</td>
      <td>379</td>
    </tr>
    <tr>
      <td>3</td>
      <td>N137</td>
      <td>R354</td>
      <td>00-00-00</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/02/2019</td>
      <td>720</td>
      <td>352</td>
    </tr>
    <tr>
      <td>4</td>
      <td>N137</td>
      <td>R354</td>
      <td>00-00-00</td>
      <td>104 ST</td>
      <td>A</td>
      <td>10/01/2019</td>
      <td>603</td>
      <td>352</td>
    </tr>
  </tbody>
</table>

Then, let's arrange the dataframe in another way:
{% highlight python%}
# sum up entries at each station, use 'station' as columns and 'date' as index
tstile = pd.pivot_table(turnstile_df, values='entries',\
 index=['date'], columns=['station'], aggfunc=np.sum) 
tstile.head()
{% endhighlight %}

<table class="dataframe" border="0" frame = "vside">
  <thead>
    <tr style="text-align: right;">
      <th>station</th>
      <th>104 ST</th>
      <th>111 ST</th>
      <th>125 ST</th>
      <th>14 ST</th>
      <th>145 ST</th>
      <th>168 ST</th>
      <th>...</th>
      <th>W 4 ST-WASH SQ</th>
      <th>WORLD TRADE CTR</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>09/28/2019</td>
      <td>0</td>
      <td>1</td>
      <td>17522</td>
      <td>11118</td>
      <td>14909</td>
      <td>9626</td>
      <td>...</td>
      <td>28372</td>
      <td>450</td>
    </tr>
    <tr>
      <td>09/29/2019</td>
      <td>0</td>
      <td>0</td>
      <td>14850</td>
      <td>8301</td>
      <td>12179</td>
      <td>6601</td>
      <td>...</td>
      <td>23094</td>
      <td>741</td>
    </tr>
    <tr>
      <td>09/30/2019</td>
      <td>1569</td>
      <td>2227</td>
      <td>22058</td>
      <td>17018</td>
      <td>18440</td>
      <td>16861</td>
      <td>...</td>
      <td>31745</td>
      <td>16174</td>
    </tr>
    <tr>
      <td>10/01/2019</td>
      <td>1613</td>
      <td>2438</td>
      <td>23991</td>
      <td>18127</td>
      <td>19394</td>
      <td>18269</td>
      <td>...</td>
      <td>35842</td>
      <td>17173</td>
    </tr>
    <tr>
      <td>10/02/2019</td>
      <td>2029</td>
      <td>2929</td>
      <td>27774</td>
      <td>21693</td>
      <td>24183</td>
      <td>20073</td>
      <td>...</td>
      <td>37851</td>
      <td>18381</td>
    </tr>
  </tbody>
</table>

It is well know that the source data could be problematic because of faulty turnstiles. So, before doing any analysis, we want to check if the data is valid. One method is to look at the average weekly entries for each station.

{% highlight python%}
checker = tstile.sum()/len(weeks)
checker.sort_values(ascending = False)
{% endhighlight %}

![](/assets/img/tech-blog/mta/scshot_ranking.png)


From the above table, we can see that <span style="color:red">59 ST COLUMBUS</span> station has more than 16 million entries each week, but Penn Station (34 ST-PENN STA, one of the transportation centers of NYC) has only roughly 1 million. There must be some faulty turnstiles at 59 ST COLUMBUS station. Let's remove <span style="color:red">59 ST COLUMBUS</span> to avoid misleading results. If interested, one can find that it was the turnstile with SCP number '01-05-01' encountered problem from 0:00-8:00 AM on 09/28/2019. We are supposed to remove thoes faulty records and calculate for 59 ST COLUMBUS station again, but for simplicity, we just remove this station.

Let's plot the results.

{% highlight python%}
tstile.drop(columns=['59 ST COLUMBUS'], inplace = True)
tstile_weekly = tstile.sum()/len(weeks)
tstile_weekly.sort_values(ascending=False, inplace=True)
{% endhighlight %}

| ![](/assets/img/tech-blog/mta/entries_ranking.png){:width="600px"} | 
|:--:| 
| *Fig 1. Ranking of entries - Stations on A line* |

Not surprising, those well known places, like Penn Station, Port Authority, Fulton Street (under World Trade Center), and Time Square rank on the top. However, we also notice BEACH 36 ST station, which is the terminal station and locates at far south Brooklyn, ranks in the 2nd place. This seems a little weired. If you are interested, you can further check this station like what we did for 59 ST COLUMBUS.


## Visualize Daily Entry/Exit Dynamics for Chosen Stations
Now, we already have the weekly ranking, but what if we want to know intra-week dynamics of entries/exits? Let's say, for Penn Station, Port Authority, and Time Square.

{% highlight python%}
stations = ['34 ST-PENN STA', '42 ST-PORT AUTH', 'TIMES SQ-42 ST']

# Convert date string to datetime object for plot
turnstile_df['date'] = pd.to_datetime(turnstile_df['date'])
tstile_dynamic = turnstile_df.groupby(['station','date']).sum().\
sort_values(by=['station', 'date'],ascending=False)
tstile_dynamic.reset_index(inplace=True)
tstile_dynamic.set_index('date', inplace = True)
tstile_dynamic.sort_index(ascending=True, inplace = True)

legend = ['Entries', 'Exits']

# Define a function for better view of y sticks
def y_fmt(tick_val, pos):
    if tick_val > 1000000:
        val = int(tick_val)/1000000
        return '{:.0f} M'.format(val)
    elif tick_val > 1000:
        val = int(tick_val) / 1000
        return '{:.0f} k'.format(val)
    else:
        return tick_val

for station in stations:
    f, ax = plt.subplots(figsize = (15,3))
    tstile_dynamic[tstile_dynamic['station'] == station].plot(ax = ax, y = 'entries')
    tstile_dynamic[tstile_dynamic['station'] == station].plot(ax = ax, y = 'exits')
    ax.legend(legend)
    ax.set_xlabel('Date',size = 12)
    ax.set_ylabel('Entries/Exits',size = 15)
    ax.set_title('Staion: ' + station)    
    ax.yaxis.set_major_formatter(tick.FuncFormatter(y_fmt))
{% endhighlight %}

| ![](/assets/img/tech-blog/mta/week_dynamic1.png){:width="600px"} | 
| ![](/assets/img/tech-blog/mta/week_dynamic2.png){:width="600px"} | 
| ![](/assets/img/tech-blog/mta/week_dynamic3.png){:width="600px"} | 
|:--:| 
| *Fig 2. Weekly dynamics of entries/exits at several busy NYC stations* |

What happens at Penn Station on 10/05? Another turnstile problem? A big event (what event can boost the entries/exits by more than 800k)? It is subjected to be checked.


## Closing Notes
Unlike other datasets, MTA turnstile data is provided in .txt format, so we need to use extra code snippets to handle it. Also remember that turnstile data can be misleading due to frequent malfunction of turnstiles. Make sure to check if the data makes sense before going any further.


<span style="font-size:90%"><i>Acknowledgement: the part on calculating entries per day was forked from [https://github.com/ZachHeick/MTA_Data_Analysis](https://github.com/ZachHeick/MTA_Data_Analysis).</i></span>

