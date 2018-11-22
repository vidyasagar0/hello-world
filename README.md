# hello-world
df['eventtime'] = df['event_time'].apply(lambda x : datetime.strptime(x[:-7],'%Y-%m-%d %H:%M:%S'))
df['date'] = df["event_time"].apply(lambda x:x[:10])
df['time'] = df['event_time'].apply(lambda x : datetime.strptime(x[11:-7],'%H:%M:%S'))
df1=df[df.time <'1900-01-01 19:00:00']
df['offline']=df["detail"].str.contains('False')
df['online'] = (df['detail'].str.contains('True') | df['detail'].str.contains('Action on Job'))
df1=df.drop(df[(df.offline==False) & (df.online==False)].index)
df1.loc[df1.offline == True, 'Status'] = 'offline'
df1.loc[df1.online == True, 'Status'] = 'online'
df1=df1.dropna()
df1=df1.groupby('provider_id').apply(lambda x: x.sort_values(by = 'eventtime', ascending = True))
def get_status(df1):
    if df1['time'] <datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'):
        return datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')
    elif df1['time'] >=datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'):
        return df1['time']
   df1['New_time'] = df1.apply(get_status, axis = 1)
   
   
   
   
# coding: utf-8

# In[1]:


import pandas as pd
import numpy as np
from datetime import datetime


# In[65]:


df=pd.read_csv(r"assign.csv")


# In[66]:


df.head()


# In[4]:


df.dtypes
df.shape


# In[67]:


df['eventtime'] = df['event_time'].apply(lambda x : datetime.strptime(x[:-7],'%Y-%m-%d %H:%M:%S'))


# In[68]:


df['date'] = df["event_time"].apply(lambda x:x[:10])


# In[69]:


df['time'] = df['event_time'].apply(lambda x : datetime.strptime(x[11:-7],'%H:%M:%S'))


# In[36]:


df


# In[70]:


df1=df[df.time <'1900-01-01 19:00:00']


# In[10]:


df.shape


# In[71]:


df['offline']=df["detail"].str.contains('False')


# In[72]:


df['online'] = (df['detail'].str.contains('True') | df['detail'].str.contains('Action on Job'))


# In[13]:


df.head()


# In[ ]:





# In[73]:


df1=df.drop(df[(df.offline==False) & (df.online==False)].index)


# In[15]:


df1


# In[ ]:





# In[74]:


df1.loc[df1.online == True, 'Status'] = 'online'


# In[75]:


df1.loc[df1.offline == True, 'Status'] = 'offline'


# In[18]:


df1


# In[76]:


df1=df1.dropna()


# In[ ]:





# In[20]:


df1=df1.groupby('provider_id').apply(lambda x: x.sort_values(by = 'eventtime', ascending = True))


# In[21]:


df1


# In[22]:


df1.columns


# In[23]:


def get_status(df1):
    if df1['time'] <datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'):
        return datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')
    elif df1['time'] >=datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'):
        return df1['time']
    
df1['New_time'] = df1.apply(get_status, axis = 1)


# In[24]:


df1


# In[25]:


df2=df1[df1['time'] <datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')]


# In[26]:


df2


# In[77]:


def get_status(df1):
    if df1['time'] <datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'):
        return datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')
    elif df1['time'] >=datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'):
        return df1['time']
    
df1['New_time'] = df1.apply(get_status, axis = 1)


# In[78]:


df1 = df1.sort_values(['provider_id', 'date', "New_time"])


# In[79]:


df1["offset_status"] = df1["Status"].shift(1)


# In[80]:


df1["offset_time"] = df1["New_time"].shift(1)


# In[83]:


df1["offset_date"] = df1["date"].shift(1)


# In[98]:


df1["offset_pid"] = df1["provider_id"].shift(1)


# In[84]:


df1.iloc[30:45]


# In[99]:


def  online_secs (x):
    if x["provider_id"] == x["offset_pid"]:
        if x["date"] == x["offset_date"]:
            if x["Status"] == x["offset_status"] and x["Status"] == "offline":
                return 0
            elif x["Status"] == x["offset_status"] and x["Status"] == "online":
                return datetime.strptime(str(x["New_time"]),'%Y-%m-%d %H:%M:%S') - datetime.strptime(str(x["offset_time"]),'%Y-%m-%d %H:%M:%S')
            elif x["Status"] != x["offset_status"] and x["Status"] == "online" and x["offset_status"] == "offline" :
                return 0
            elif x["Status"] != x["offset_status"] and x["Status"] == "offline" and x["offset_status"] == "online" :
                return datetime.strptime(str(x["New_time"]),'%Y-%m-%d %H:%M:%S') - datetime.strptime(str(x["offset_time"]),'%Y-%m-%d %H:%M:%S')
        else:
            return 0
    else:
        return 0


# In[100]:


df1["online_secs"] = df1.apply(online_secs, axis = 1)


# In[101]:


df1.head()
