# hello-world

# coding: utf-8

# In[488]:


import pandas as pd
import numpy as np
from datetime import datetime
from datetime import timedelta


# In[489]:


df=pd.read_csv(r"assign.csv")


# In[490]:


df.dtypes


# In[491]:


df['eventtime'] = df['event_time'].apply(lambda x : datetime.strptime(x[:-7],'%Y-%m-%d %H:%M:%S'))


# In[492]:




df['date'] = df["event_time"].apply(lambda x:datetime.date(datetime.strptime(x[:-7],'%Y-%m-%d %H:%M:%S')))


# In[493]:


df['time'] = df['event_time'].apply(lambda x : datetime.time(datetime.strptime(x[:-7],'%Y-%m-%d %H:%M:%S')))


# In[494]:


df.head()


# df.date

# In[495]:


df.isnull().sum()


# In[496]:


df1=df[df.time <datetime.time(datetime.strptime('1900-01-01 19:00:00','%Y-%m-%d %H:%M:%S'))]


# In[497]:


df1.shape


# In[498]:


df1['offline']=df1["detail"].str.contains('False')


# In[499]:





df1['online'] = (df1['detail'].str.contains('True') | df1['detail'].str.contains('Action on Job'))


# In[500]:


df1=df1.drop(df1[(df1.offline==False) & (df1.online==False)].index)


# In[501]:


df1.loc[df1.offline == True, 'Status'] = 'offline'


# In[502]:


df1.loc[df1.online == True, 'Status'] = 'online'


# In[503]:


df1.shape


# In[504]:


df1=df1.dropna()


# In[505]:


df1.head()


# In[506]:


df0=df1[df1.time <= datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'))]


# In[ ]:





# In[507]:


df0.shape


# In[508]:


df4=df0.groupby(['provider_id','date'])['time'].max()


# In[509]:


df4


# In[510]:


df4 = df4.reset_index()


# In[511]:


df4


# In[512]:


df5=df4[['provider_id','date','time']]


# In[513]:


df5['provider_id'].unique().shape


# In[514]:


df5.shape


# In[515]:


df7=df0.merge(df5,how='inner',on=['provider_id','date','time'])


# In[519]:


df7.shape


# In[520]:


df8 = df1[df1.time > datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'))]


# In[521]:


df8.shape


# In[522]:


frames = [df7, df8]


# In[523]:


df9 = pd.concat(frames)


# In[524]:


df9.shape


# In[525]:


def get_status(df9):
    if df9['time'] <=datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')):
        return datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'))
    elif df9['time'] >datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')):
        return df9['time']
    
df9['New_time'] = df9.apply(get_status, axis = 1)


# In[526]:


df9


# In[527]:


df10=df9.sort_values(by=['provider_id','date', 'time'])


# In[528]:


df10


# In[529]:


df10["offset_status"] = df10["Status"].shift(1)


# In[530]:


df10["offset_time"] = df10["New_time"].shift(1)


# In[531]:


df10["offset_date"] = df10["date"].shift(1)


# In[532]:


df10["offset_pid"] = df10["provider_id"].shift(1)


# In[533]:


df2


# In[534]:


def  online_secs (x):
    td_diff = timedelta(seconds=0)
    if x["provider_id"] == x["offset_pid"]:
        if x["date"] == x["offset_date"]:
            if x["Status"] == x["offset_status"] and x["Status"] == "offline":
                td_diff = timedelta(seconds=0) 
            elif x["Status"] == x["offset_status"] and x["Status"] == "online":
                strt_time = datetime.combine(x["offset_date"], x["offset_time"])
                end_time = datetime.combine(x["date"], x["New_time"])
                td_diff = end_time-strt_time
            elif x["Status"] != x["offset_status"] and x["Status"] == "online" and x["offset_status"] == "offline" :
                td_diff = timedelta(seconds=0) 
            elif x["Status"] != x["offset_status"] and x["Status"] == "offline" and x["offset_status"] == "online" :
                strt_time = datetime.combine(x["offset_date"], x["offset_time"])
                end_time = datetime.combine(x["date"], x["New_time"])
                td_diff = end_time-strt_time
    
    return td_diff.total_seconds() 


# In[535]:


df10["seconds_online"] = df10.apply(online_secs, axis = 1)


# In[536]:


df10[df10["seconds_online"] > 0.0]


# In[539]:


df10["start_time"] = df10.New_time.apply(lambda x:int(str(x)[:2]))
df10["end_time"] = df10.New_time.apply(lambda x:int(str(x)[:2])+1)


# In[540]:


df_final = df10.groupby(["provider_id", "date", "start_time", "end_time"])["seconds_online"].sum()


# In[541]:


df_final1 = df_final.to_frame().reset_index()


# In[542]:


df_final1[df_final1["seconds_online"] > 0.0]


# In[543]:


df_final1.sort_values(['provider_id', 'date', "start_time"])


# In[544]:


df_final1[df_final1.seconds_online>3600]
   


# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





# In[ ]:





   
   
   
   
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
