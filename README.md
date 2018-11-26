{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 230,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "import pandas as pd\n",
    "import numpy as np\n",
    "from datetime import datetime\n",
    "from datetime import timedelta"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 231,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df=pd.read_csv(r\"Data Engineer Assignment_1.csv\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 232,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df['eventtime'] = df['event_time'].apply(lambda x : datetime.strptime(x[:-7],'%Y-%m-%d %H:%M:%S'))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 233,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df['date'] = df[\"event_time\"].apply(lambda x:datetime.date(datetime.strptime(x[:-7],'%Y-%m-%d %H:%M:%S')))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 234,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df['time'] = df['event_time'].apply(lambda x : datetime.time(datetime.strptime(x[:-7],'%Y-%m-%d %H:%M:%S')))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 235,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df=df.dropna()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 236,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(185679, 7)"
      ]
     },
     "execution_count": 236,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 237,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df1=df[df.time <=datetime.time(datetime.strptime('1900-01-01 19:00:00','%Y-%m-%d %H:%M:%S'))]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 238,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(156349, 7)"
      ]
     },
     "execution_count": 238,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df1.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 239,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/home/n323388/.conda/envs/myenv/lib/python2.7/site-packages/ipykernel_launcher.py:1: SettingWithCopyWarning: \n",
      "A value is trying to be set on a copy of a slice from a DataFrame.\n",
      "Try using .loc[row_indexer,col_indexer] = value instead\n",
      "\n",
      "See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy\n",
      "  \"\"\"Entry point for launching an IPython kernel.\n"
     ]
    }
   ],
   "source": [
    "df1['offline']=df1[\"detail\"].str.contains('False')\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 240,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/home/n323388/.conda/envs/myenv/lib/python2.7/site-packages/ipykernel_launcher.py:1: SettingWithCopyWarning: \n",
      "A value is trying to be set on a copy of a slice from a DataFrame.\n",
      "Try using .loc[row_indexer,col_indexer] = value instead\n",
      "\n",
      "See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy\n",
      "  \"\"\"Entry point for launching an IPython kernel.\n"
     ]
    }
   ],
   "source": [
    "df1['online'] = (df1['detail'].str.contains('True') | df1['source'].str.contains('Action on Job'))\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 241,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df1=df1.drop(df1[(df1.offline==False) & (df1.online==False)].index)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 242,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df1.loc[df1.offline == True, 'Status'] = 'offline'\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 243,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df1.loc[df1.online == True, 'Status'] = 'online'"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 244,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "offline    52962\n",
       "online     22007\n",
       "Name: Status, dtype: int64"
      ]
     },
     "execution_count": 244,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df1['Status'].value_counts()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 245,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(6424,)"
      ]
     },
     "execution_count": 245,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df1['provider_id'].unique().shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 246,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df1=df1.drop_duplicates(subset=['provider_id','date','time'], keep=False)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 247,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(48584, 10)"
      ]
     },
     "execution_count": 247,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df1.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 248,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df2=df1[df1.time > datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'))]\n",
    "df3=df1[df1.time <= datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S'))]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 249,
   "metadata": {},
   "outputs": [],
   "source": [
    "df4=df3.groupby(['provider_id','date'])['time'].max()\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 250,
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(2699, 3)"
      ]
     },
     "execution_count": 250,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df5=df4.to_frame().reset_index()\n",
    "df5.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 251,
   "metadata": {},
   "outputs": [],
   "source": [
    "df6= pd.merge(df5,df1,on=['provider_id','date','time'],how='left')\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 252,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(2699, 10)"
      ]
     },
     "execution_count": 252,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df6.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 253,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "frames = [df2, df6]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 254,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df7 = pd.concat(frames)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 255,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "def get_status(df7): \n",
    "    if df7['time'] <=datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')): \n",
    "        return datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')) \n",
    "    elif df7['time'] >datetime.time(datetime.strptime('1900-01-01 08:00:00','%Y-%m-%d %H:%M:%S')): \n",
    "        return df7['time']\n",
    "\n",
    "df7['New_time'] = df7.apply(get_status, axis = 1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 256,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df8 = df7.sort_values(['provider_id', 'date', \"New_time\"])\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 257,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df8[\"offset_status\"] = df8[\"Status\"].shift(1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 258,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df8[\"offset_time\"] = df8[\"New_time\"].shift(1)\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 259,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df8[\"offset_date\"] = df8[\"date\"].shift(1)\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 260,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df8[\"offset_pid\"] = df8[\"provider_id\"].shift(1)\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 261,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "def  online_secs (x):\n",
    "    td_diff = timedelta(seconds=0)\n",
    "    if x[\"provider_id\"] == x[\"offset_pid\"]:\n",
    "        if x[\"date\"] == x[\"offset_date\"]:\n",
    "            if x[\"Status\"] == x[\"offset_status\"] and x[\"Status\"] == \"offline\":\n",
    "                td_diff = timedelta(seconds=0) \n",
    "            elif x[\"Status\"] == x[\"offset_status\"] and x[\"Status\"] == \"online\":\n",
    "                strt_time = datetime.combine(x[\"offset_date\"], x[\"offset_time\"])\n",
    "                end_time = datetime.combine(x[\"date\"], x[\"New_time\"])\n",
    "                td_diff = end_time-strt_time\n",
    "            elif x[\"Status\"] != x[\"offset_status\"] and x[\"Status\"] == \"online\" and x[\"offset_status\"] == \"offline\" :\n",
    "                td_diff = timedelta(seconds=0) \n",
    "            elif x[\"Status\"] != x[\"offset_status\"] and x[\"Status\"] == \"offline\" and x[\"offset_status\"] == \"online\" :\n",
    "                strt_time = datetime.combine(x[\"offset_date\"], x[\"offset_time\"])\n",
    "                end_time = datetime.combine(x[\"date\"], x[\"New_time\"])\n",
    "                td_diff = end_time-strt_time\n",
    "    \n",
    "    return td_diff.total_seconds()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 268,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style>\n",
       "    .dataframe thead tr:only-child th {\n",
       "        text-align: right;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: left;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Status</th>\n",
       "      <th>date</th>\n",
       "      <th>detail</th>\n",
       "      <th>event_time</th>\n",
       "      <th>eventtime</th>\n",
       "      <th>offline</th>\n",
       "      <th>online</th>\n",
       "      <th>provider_id</th>\n",
       "      <th>source</th>\n",
       "      <th>time</th>\n",
       "      <th>New_time</th>\n",
       "      <th>offset_status</th>\n",
       "      <th>offset_time</th>\n",
       "      <th>offset_date</th>\n",
       "      <th>offset_pid</th>\n",
       "      <th>seconds_online</th>\n",
       "      <th>start_time</th>\n",
       "      <th>end_time</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>53031</th>\n",
       "      <td>offline</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>{u'mme': False}</td>\n",
       "      <td>2017-09-01 17:37:59.000000</td>\n",
       "      <td>2017-09-01 17:37:59</td>\n",
       "      <td>True</td>\n",
       "      <td>False</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>decline_request</td>\n",
       "      <td>17:37:59</td>\n",
       "      <td>17:37:59</td>\n",
       "      <td>NaN</td>\n",
       "      <td>NaN</td>\n",
       "      <td>NaN</td>\n",
       "      <td>NaN</td>\n",
       "      <td>0.0</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>53035</th>\n",
       "      <td>offline</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>{u'mme': False}</td>\n",
       "      <td>2017-09-01 17:38:05.000000</td>\n",
       "      <td>2017-09-01 17:38:05</td>\n",
       "      <td>True</td>\n",
       "      <td>False</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>decline_request</td>\n",
       "      <td>17:38:05</td>\n",
       "      <td>17:38:05</td>\n",
       "      <td>offline</td>\n",
       "      <td>17:37:59</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>0.0</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>36530</th>\n",
       "      <td>offline</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>{u'mme': False}</td>\n",
       "      <td>2017-09-01 17:38:12.000000</td>\n",
       "      <td>2017-09-01 17:38:12</td>\n",
       "      <td>True</td>\n",
       "      <td>False</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>decline_request</td>\n",
       "      <td>17:38:12</td>\n",
       "      <td>17:38:12</td>\n",
       "      <td>offline</td>\n",
       "      <td>17:38:05</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>0.0</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>53042</th>\n",
       "      <td>offline</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>{u'mme': False}</td>\n",
       "      <td>2017-09-01 17:38:18.000000</td>\n",
       "      <td>2017-09-01 17:38:18</td>\n",
       "      <td>True</td>\n",
       "      <td>False</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>decline_request</td>\n",
       "      <td>17:38:18</td>\n",
       "      <td>17:38:18</td>\n",
       "      <td>offline</td>\n",
       "      <td>17:38:12</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>0.0</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>100422</th>\n",
       "      <td>offline</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>{u'mme': False}</td>\n",
       "      <td>2017-09-02 16:52:19.000000</td>\n",
       "      <td>2017-09-02 16:52:19</td>\n",
       "      <td>True</td>\n",
       "      <td>False</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>decline_request</td>\n",
       "      <td>16:52:19</td>\n",
       "      <td>16:52:19</td>\n",
       "      <td>offline</td>\n",
       "      <td>17:38:18</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>0002beae790876e71dfb99a904563c30</td>\n",
       "      <td>0.0</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "         Status        date           detail                  event_time  \\\n",
       "53031   offline  2017-09-01  {u'mme': False}  2017-09-01 17:37:59.000000   \n",
       "53035   offline  2017-09-01  {u'mme': False}  2017-09-01 17:38:05.000000   \n",
       "36530   offline  2017-09-01  {u'mme': False}  2017-09-01 17:38:12.000000   \n",
       "53042   offline  2017-09-01  {u'mme': False}  2017-09-01 17:38:18.000000   \n",
       "100422  offline  2017-09-02  {u'mme': False}  2017-09-02 16:52:19.000000   \n",
       "\n",
       "                 eventtime  offline  online                       provider_id  \\\n",
       "53031  2017-09-01 17:37:59     True   False  0002beae790876e71dfb99a904563c30   \n",
       "53035  2017-09-01 17:38:05     True   False  0002beae790876e71dfb99a904563c30   \n",
       "36530  2017-09-01 17:38:12     True   False  0002beae790876e71dfb99a904563c30   \n",
       "53042  2017-09-01 17:38:18     True   False  0002beae790876e71dfb99a904563c30   \n",
       "100422 2017-09-02 16:52:19     True   False  0002beae790876e71dfb99a904563c30   \n",
       "\n",
       "                 source      time  New_time offset_status offset_time  \\\n",
       "53031   decline_request  17:37:59  17:37:59           NaN         NaN   \n",
       "53035   decline_request  17:38:05  17:38:05       offline    17:37:59   \n",
       "36530   decline_request  17:38:12  17:38:12       offline    17:38:05   \n",
       "53042   decline_request  17:38:18  17:38:18       offline    17:38:12   \n",
       "100422  decline_request  16:52:19  16:52:19       offline    17:38:18   \n",
       "\n",
       "       offset_date                        offset_pid  seconds_online  \\\n",
       "53031          NaN                               NaN             0.0   \n",
       "53035   2017-09-01  0002beae790876e71dfb99a904563c30             0.0   \n",
       "36530   2017-09-01  0002beae790876e71dfb99a904563c30             0.0   \n",
       "53042   2017-09-01  0002beae790876e71dfb99a904563c30             0.0   \n",
       "100422  2017-09-01  0002beae790876e71dfb99a904563c30             0.0   \n",
       "\n",
       "        start_time  end_time  \n",
       "53031           17        18  \n",
       "53035           17        18  \n",
       "36530           17        18  \n",
       "53042           17        18  \n",
       "100422          16        17  "
      ]
     },
     "execution_count": 268,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df8[\"seconds_online\"] = df8.apply(online_secs, axis = 1)\n",
    "df8.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 269,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df8[\"start_time\"] = df8.New_time.apply(lambda x:int(str(x)[:2]))\n",
    "df8[\"end_time\"] = df8.New_time.apply(lambda x:int(str(x)[:2])+1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 270,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_final = df8.groupby([\"provider_id\", \"date\", \"start_time\", \"end_time\"])[\"seconds_online\"].sum()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 271,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_final1 = df_final.to_frame().reset_index()\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 266,
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style>\n",
       "    .dataframe thead tr:only-child th {\n",
       "        text-align: right;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: left;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>provider_id</th>\n",
       "      <th>date</th>\n",
       "      <th>start_time</th>\n",
       "      <th>end_time</th>\n",
       "      <th>seconds_online</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>42</th>\n",
       "      <td>004e25e0b5a3d2291caf267eff79f6d6</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3923.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>89</th>\n",
       "      <td>011ad7e6d66c66bec6fcac31cc57023e</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>8017.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>269</th>\n",
       "      <td>036311d234645b6de50bd96059f56d9f</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>19588.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>451</th>\n",
       "      <td>04a153f1d81330c8f2b1c7c868857263</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>3822.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>469</th>\n",
       "      <td>04cd3aeb42e370d7c2af6f0780ef97ee</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>10341.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>477</th>\n",
       "      <td>04cd3aeb42e370d7c2af6f0780ef97ee</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>8701.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>498</th>\n",
       "      <td>0503f1b4cd1f01165cc4fbfc0bcbd422</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>9953.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>616</th>\n",
       "      <td>06137a5417cfe667bb66d3871a08d775</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>5450.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>748</th>\n",
       "      <td>071aeff24666fc1d2f0550df11446165</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>7754.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1141</th>\n",
       "      <td>0a456b121c3360f05ae790d5c6ccc569</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>11</td>\n",
       "      <td>12</td>\n",
       "      <td>13009.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1142</th>\n",
       "      <td>0a456b121c3360f05ae790d5c6ccc569</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>6569.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1143</th>\n",
       "      <td>0a456b121c3360f05ae790d5c6ccc569</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>5580.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1264</th>\n",
       "      <td>0b09fe3f401e3154e41d6bb82c98efd7</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3657.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1380</th>\n",
       "      <td>0c5dbdd8e8409f5bee1afff6be8db051</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3773.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1766</th>\n",
       "      <td>10689b639cd6b3a8d6547708cd696c43</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>10</td>\n",
       "      <td>11</td>\n",
       "      <td>8964.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2154</th>\n",
       "      <td>151fa3ec1e55a934b823ecc11e59ad7b</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>10018.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2298</th>\n",
       "      <td>1606b1273c09bda54982bbad4d60bc7c</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>10447.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2299</th>\n",
       "      <td>1606b1273c09bda54982bbad4d60bc7c</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>12857.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2352</th>\n",
       "      <td>16882ffbe3e77db910f1f4c6e4e84609</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>10</td>\n",
       "      <td>11</td>\n",
       "      <td>4356.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2486</th>\n",
       "      <td>177b71e7c3f7d9e7f8bd0ceb4ee3363e</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>10279.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2581</th>\n",
       "      <td>18a99de86d75cbeb6b1713ad11bcd752</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>7621.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2874</th>\n",
       "      <td>1bf12f18e28e8d3d1f456643c5a26a2e</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>13765.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2900</th>\n",
       "      <td>1c1b6efab0943992e1fb93ef7796bdea</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>20819.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2969</th>\n",
       "      <td>1c85f0661e4a77b78c255bad5a714a04</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>10112.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2972</th>\n",
       "      <td>1c85f0661e4a77b78c255bad5a714a04</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>7386.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3113</th>\n",
       "      <td>1e5a3227e70e8a53bc7b9634452f248e</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>5076.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3120</th>\n",
       "      <td>1e5a3227e70e8a53bc7b9634452f248e</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3989.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3132</th>\n",
       "      <td>1e69d771dfffb679f3b2df0bd46a0a57</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "      <td>21632.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3329</th>\n",
       "      <td>1fd8288098a3ac382b050c374b287667</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>10</td>\n",
       "      <td>11</td>\n",
       "      <td>6796.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3426</th>\n",
       "      <td>20ce00390bfd8efc8c65d1cdcc3fca7b</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>4628.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>21968</th>\n",
       "      <td>cef542284309096fde147c0f9c0c1ffe</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "      <td>9300.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>22101</th>\n",
       "      <td>d02d656f058f75681b02644ac5a91682</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>4376.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>22497</th>\n",
       "      <td>d2c92ee210fd716d665389001f8bfa53</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>12545.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>22637</th>\n",
       "      <td>d3ea46f3b3662b2b1e59a1b8611ad453</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>8387.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>22798</th>\n",
       "      <td>d5753f4b6f13429945b5692f58568be0</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "      <td>26248.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>22936</th>\n",
       "      <td>d6cc4c6e9da8664a8981d1d92cd88ce7</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>6408.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>22966</th>\n",
       "      <td>d718207a3fc6691d3b799cbf38013013</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>12259.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>23399</th>\n",
       "      <td>dbedf854194f290abe90c3016ae5c54a</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "      <td>11051.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>23468</th>\n",
       "      <td>dc72baf1bffead7ad0608132c487bc8a</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>17555.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>23916</th>\n",
       "      <td>e0b8b8e7afb268a6485a1125d876087c</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>4526.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24112</th>\n",
       "      <td>e2b82eb9ae20a72c3c6ee11f7039c980</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>6231.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24232</th>\n",
       "      <td>e3d7a49b0de2ec35c52e6f321f968f3d</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>5398.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24504</th>\n",
       "      <td>e5e21ff2320fd6fe3118dc81012889bd</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>13524.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24570</th>\n",
       "      <td>e6824cf5b1439823a61e558becdf0016</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>13870.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24578</th>\n",
       "      <td>e6894f61ba140856663c48eb715abb5e</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>11</td>\n",
       "      <td>12</td>\n",
       "      <td>6409.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24605</th>\n",
       "      <td>e71547e3d491fac45d4603020a6fd4a9</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>6844.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24608</th>\n",
       "      <td>e71547e3d491fac45d4603020a6fd4a9</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>10</td>\n",
       "      <td>11</td>\n",
       "      <td>7805.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24670</th>\n",
       "      <td>e7eb2982f8008ffca52d3d6399c339fa</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>3839.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24738</th>\n",
       "      <td>e874aa86bb5eb45483639f57168e335f</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>10</td>\n",
       "      <td>11</td>\n",
       "      <td>8565.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24739</th>\n",
       "      <td>e874aa86bb5eb45483639f57168e335f</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>11</td>\n",
       "      <td>12</td>\n",
       "      <td>4683.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24864</th>\n",
       "      <td>e999b82bb0313e417fd3117803bcbf48</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>11</td>\n",
       "      <td>12</td>\n",
       "      <td>5841.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>24921</th>\n",
       "      <td>ea2a5fa389f567b0ca4cfe022237ad57</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3839.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25602</th>\n",
       "      <td>f14f3f8ae4624f9b9ec0a71a1a997360</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>7394.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25676</th>\n",
       "      <td>f283267a59e87510cdfb8b922f8222c5</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>14836.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25690</th>\n",
       "      <td>f285365b7b1e477dfbc12ef3ed795189</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3833.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26104</th>\n",
       "      <td>f75016d189faa0b4574467ef9f4ad69f</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>30089.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26169</th>\n",
       "      <td>f7bc4e348aca691d4d421e7840be5310</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>4160.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26242</th>\n",
       "      <td>f865d7a07071867691880860b88f5920</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>13676.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26691</th>\n",
       "      <td>fd72e38fc42ca61622e34ee04e4d90b3</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>9211.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26793</th>\n",
       "      <td>fe9df607f56f5519c36bf5b42c99967f</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>6645.0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>162 rows × 5 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "                            provider_id        date  start_time  end_time  \\\n",
       "42     004e25e0b5a3d2291caf267eff79f6d6  2017-09-04           9        10   \n",
       "89     011ad7e6d66c66bec6fcac31cc57023e  2017-09-01          16        17   \n",
       "269    036311d234645b6de50bd96059f56d9f  2017-09-02          14        15   \n",
       "451    04a153f1d81330c8f2b1c7c868857263  2017-09-02          15        16   \n",
       "469    04cd3aeb42e370d7c2af6f0780ef97ee  2017-09-01          12        13   \n",
       "477    04cd3aeb42e370d7c2af6f0780ef97ee  2017-09-03          16        17   \n",
       "498    0503f1b4cd1f01165cc4fbfc0bcbd422  2017-09-01          15        16   \n",
       "616    06137a5417cfe667bb66d3871a08d775  2017-09-03          15        16   \n",
       "748    071aeff24666fc1d2f0550df11446165  2017-09-03          16        17   \n",
       "1141   0a456b121c3360f05ae790d5c6ccc569  2017-09-03          11        12   \n",
       "1142   0a456b121c3360f05ae790d5c6ccc569  2017-09-03          13        14   \n",
       "1143   0a456b121c3360f05ae790d5c6ccc569  2017-09-03          15        16   \n",
       "1264   0b09fe3f401e3154e41d6bb82c98efd7  2017-09-04           9        10   \n",
       "1380   0c5dbdd8e8409f5bee1afff6be8db051  2017-09-04           9        10   \n",
       "1766   10689b639cd6b3a8d6547708cd696c43  2017-09-03          10        11   \n",
       "2154   151fa3ec1e55a934b823ecc11e59ad7b  2017-09-03          14        15   \n",
       "2298   1606b1273c09bda54982bbad4d60bc7c  2017-09-01          12        13   \n",
       "2299   1606b1273c09bda54982bbad4d60bc7c  2017-09-01          16        17   \n",
       "2352   16882ffbe3e77db910f1f4c6e4e84609  2017-09-01          10        11   \n",
       "2486   177b71e7c3f7d9e7f8bd0ceb4ee3363e  2017-09-01          15        16   \n",
       "2581   18a99de86d75cbeb6b1713ad11bcd752  2017-09-01          12        13   \n",
       "2874   1bf12f18e28e8d3d1f456643c5a26a2e  2017-09-03          14        15   \n",
       "2900   1c1b6efab0943992e1fb93ef7796bdea  2017-09-02          15        16   \n",
       "2969   1c85f0661e4a77b78c255bad5a714a04  2017-09-01          14        15   \n",
       "2972   1c85f0661e4a77b78c255bad5a714a04  2017-09-02          13        14   \n",
       "3113   1e5a3227e70e8a53bc7b9634452f248e  2017-09-02          14        15   \n",
       "3120   1e5a3227e70e8a53bc7b9634452f248e  2017-09-04           9        10   \n",
       "3132   1e69d771dfffb679f3b2df0bd46a0a57  2017-09-01          17        18   \n",
       "3329   1fd8288098a3ac382b050c374b287667  2017-09-01          10        11   \n",
       "3426   20ce00390bfd8efc8c65d1cdcc3fca7b  2017-09-02          13        14   \n",
       "...                                 ...         ...         ...       ...   \n",
       "21968  cef542284309096fde147c0f9c0c1ffe  2017-09-03          17        18   \n",
       "22101  d02d656f058f75681b02644ac5a91682  2017-09-02          12        13   \n",
       "22497  d2c92ee210fd716d665389001f8bfa53  2017-09-02          14        15   \n",
       "22637  d3ea46f3b3662b2b1e59a1b8611ad453  2017-09-02          13        14   \n",
       "22798  d5753f4b6f13429945b5692f58568be0  2017-09-01          17        18   \n",
       "22936  d6cc4c6e9da8664a8981d1d92cd88ce7  2017-09-02          15        16   \n",
       "22966  d718207a3fc6691d3b799cbf38013013  2017-09-03          12        13   \n",
       "23399  dbedf854194f290abe90c3016ae5c54a  2017-09-03          17        18   \n",
       "23468  dc72baf1bffead7ad0608132c487bc8a  2017-09-02          14        15   \n",
       "23916  e0b8b8e7afb268a6485a1125d876087c  2017-09-02          16        17   \n",
       "24112  e2b82eb9ae20a72c3c6ee11f7039c980  2017-09-03          13        14   \n",
       "24232  e3d7a49b0de2ec35c52e6f321f968f3d  2017-09-01          13        14   \n",
       "24504  e5e21ff2320fd6fe3118dc81012889bd  2017-09-03          14        15   \n",
       "24570  e6824cf5b1439823a61e558becdf0016  2017-09-02          14        15   \n",
       "24578  e6894f61ba140856663c48eb715abb5e  2017-09-03          11        12   \n",
       "24605  e71547e3d491fac45d4603020a6fd4a9  2017-09-02          12        13   \n",
       "24608  e71547e3d491fac45d4603020a6fd4a9  2017-09-03          10        11   \n",
       "24670  e7eb2982f8008ffca52d3d6399c339fa  2017-09-01          16        17   \n",
       "24738  e874aa86bb5eb45483639f57168e335f  2017-09-02          10        11   \n",
       "24739  e874aa86bb5eb45483639f57168e335f  2017-09-02          11        12   \n",
       "24864  e999b82bb0313e417fd3117803bcbf48  2017-09-03          11        12   \n",
       "24921  ea2a5fa389f567b0ca4cfe022237ad57  2017-09-04           9        10   \n",
       "25602  f14f3f8ae4624f9b9ec0a71a1a997360  2017-09-03          14        15   \n",
       "25676  f283267a59e87510cdfb8b922f8222c5  2017-09-02          12        13   \n",
       "25690  f285365b7b1e477dfbc12ef3ed795189  2017-09-04           9        10   \n",
       "26104  f75016d189faa0b4574467ef9f4ad69f  2017-09-03          16        17   \n",
       "26169  f7bc4e348aca691d4d421e7840be5310  2017-09-02          14        15   \n",
       "26242  f865d7a07071867691880860b88f5920  2017-09-02          15        16   \n",
       "26691  fd72e38fc42ca61622e34ee04e4d90b3  2017-09-02          14        15   \n",
       "26793  fe9df607f56f5519c36bf5b42c99967f  2017-09-02          13        14   \n",
       "\n",
       "       seconds_online  \n",
       "42             3923.0  \n",
       "89             8017.0  \n",
       "269           19588.0  \n",
       "451            3822.0  \n",
       "469           10341.0  \n",
       "477            8701.0  \n",
       "498            9953.0  \n",
       "616            5450.0  \n",
       "748            7754.0  \n",
       "1141          13009.0  \n",
       "1142           6569.0  \n",
       "1143           5580.0  \n",
       "1264           3657.0  \n",
       "1380           3773.0  \n",
       "1766           8964.0  \n",
       "2154          10018.0  \n",
       "2298          10447.0  \n",
       "2299          12857.0  \n",
       "2352           4356.0  \n",
       "2486          10279.0  \n",
       "2581           7621.0  \n",
       "2874          13765.0  \n",
       "2900          20819.0  \n",
       "2969          10112.0  \n",
       "2972           7386.0  \n",
       "3113           5076.0  \n",
       "3120           3989.0  \n",
       "3132          21632.0  \n",
       "3329           6796.0  \n",
       "3426           4628.0  \n",
       "...               ...  \n",
       "21968          9300.0  \n",
       "22101          4376.0  \n",
       "22497         12545.0  \n",
       "22637          8387.0  \n",
       "22798         26248.0  \n",
       "22936          6408.0  \n",
       "22966         12259.0  \n",
       "23399         11051.0  \n",
       "23468         17555.0  \n",
       "23916          4526.0  \n",
       "24112          6231.0  \n",
       "24232          5398.0  \n",
       "24504         13524.0  \n",
       "24570         13870.0  \n",
       "24578          6409.0  \n",
       "24605          6844.0  \n",
       "24608          7805.0  \n",
       "24670          3839.0  \n",
       "24738          8565.0  \n",
       "24739          4683.0  \n",
       "24864          5841.0  \n",
       "24921          3839.0  \n",
       "25602          7394.0  \n",
       "25676         14836.0  \n",
       "25690          3833.0  \n",
       "26104         30089.0  \n",
       "26169          4160.0  \n",
       "26242         13676.0  \n",
       "26691          9211.0  \n",
       "26793          6645.0  \n",
       "\n",
       "[162 rows x 5 columns]"
      ]
     },
     "execution_count": 266,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_final1[df_final1.seconds_online>3600]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 267,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style>\n",
       "    .dataframe thead tr:only-child th {\n",
       "        text-align: right;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: left;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>provider_id</th>\n",
       "      <th>date</th>\n",
       "      <th>start_time</th>\n",
       "      <th>end_time</th>\n",
       "      <th>seconds_online</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>31</th>\n",
       "      <td>0037dfffff8b03bbdf366a263735e84b</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>1760.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>42</th>\n",
       "      <td>004e25e0b5a3d2291caf267eff79f6d6</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3923.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>89</th>\n",
       "      <td>011ad7e6d66c66bec6fcac31cc57023e</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>8017.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>164</th>\n",
       "      <td>02268a4ad1f08ee094c61fb2d5657521</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>2046.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>177</th>\n",
       "      <td>024650358f1918bf254a14f960b5490b</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>56.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>193</th>\n",
       "      <td>02bb4143b597c4507773c38ec948fbc8</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>2669.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>194</th>\n",
       "      <td>02bb4143b597c4507773c38ec948fbc8</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>1978.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>269</th>\n",
       "      <td>036311d234645b6de50bd96059f56d9f</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>19588.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>326</th>\n",
       "      <td>039ff0381e9ef2edee519030b3ce73de</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>576.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>328</th>\n",
       "      <td>039ff0381e9ef2edee519030b3ce73de</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>81.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>451</th>\n",
       "      <td>04a153f1d81330c8f2b1c7c868857263</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>3822.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>469</th>\n",
       "      <td>04cd3aeb42e370d7c2af6f0780ef97ee</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>10341.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>476</th>\n",
       "      <td>04cd3aeb42e370d7c2af6f0780ef97ee</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>1484.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>477</th>\n",
       "      <td>04cd3aeb42e370d7c2af6f0780ef97ee</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>8701.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>498</th>\n",
       "      <td>0503f1b4cd1f01165cc4fbfc0bcbd422</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>9953.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>499</th>\n",
       "      <td>0503f1b4cd1f01165cc4fbfc0bcbd422</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>1644.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>503</th>\n",
       "      <td>0503f1b4cd1f01165cc4fbfc0bcbd422</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>2629.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>577</th>\n",
       "      <td>059988a086a8518fbf0f5b8fac2862e5</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>48.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>593</th>\n",
       "      <td>05c9a2a23135e81c281d608194da67a6</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>1196.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>616</th>\n",
       "      <td>06137a5417cfe667bb66d3871a08d775</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>5450.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>642</th>\n",
       "      <td>066d50597ba4c36d2417d09e2c2bd775</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>266.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>748</th>\n",
       "      <td>071aeff24666fc1d2f0550df11446165</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>7754.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>906</th>\n",
       "      <td>086117a0b40f5b676e9bfedf6de6a4ac</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>1746.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1081</th>\n",
       "      <td>09b8484fd23fa45717ae80f425faee7d</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>475.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1141</th>\n",
       "      <td>0a456b121c3360f05ae790d5c6ccc569</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>11</td>\n",
       "      <td>12</td>\n",
       "      <td>13009.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1142</th>\n",
       "      <td>0a456b121c3360f05ae790d5c6ccc569</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>6569.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1143</th>\n",
       "      <td>0a456b121c3360f05ae790d5c6ccc569</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>5580.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1227</th>\n",
       "      <td>0ac613121e461c30d2fe61cb34a13d46</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>2444.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1264</th>\n",
       "      <td>0b09fe3f401e3154e41d6bb82c98efd7</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3657.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1373</th>\n",
       "      <td>0c5dbdd8e8409f5bee1afff6be8db051</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>874.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25244</th>\n",
       "      <td>edad0d4f0a9e49da3cf3123c62f20551</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>2501.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25361</th>\n",
       "      <td>eeb5988caf7b600a46c4fcfc87c379b4</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>667.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25466</th>\n",
       "      <td>ef76e67d6420e76ac83bc8606580aabf</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>947.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25469</th>\n",
       "      <td>ef76e67d6420e76ac83bc8606580aabf</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>7.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25601</th>\n",
       "      <td>f14f3f8ae4624f9b9ec0a71a1a997360</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>168.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25602</th>\n",
       "      <td>f14f3f8ae4624f9b9ec0a71a1a997360</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>7394.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25603</th>\n",
       "      <td>f14f3f8ae4624f9b9ec0a71a1a997360</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>264.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25676</th>\n",
       "      <td>f283267a59e87510cdfb8b922f8222c5</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>14836.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25681</th>\n",
       "      <td>f283267a59e87510cdfb8b922f8222c5</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>1761.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25682</th>\n",
       "      <td>f285365b7b1e477dfbc12ef3ed795189</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>48.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25685</th>\n",
       "      <td>f285365b7b1e477dfbc12ef3ed795189</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>146.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25690</th>\n",
       "      <td>f285365b7b1e477dfbc12ef3ed795189</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>9</td>\n",
       "      <td>10</td>\n",
       "      <td>3833.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25779</th>\n",
       "      <td>f39aa01938a5732a2465c2a7918b33cc</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>119.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25782</th>\n",
       "      <td>f39aa01938a5732a2465c2a7918b33cc</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>851.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25812</th>\n",
       "      <td>f3f712d5abc1461e8a8bfe1687921dd6</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>2187.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26008</th>\n",
       "      <td>f62183065f9c740c882dd1162e2bd65d</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>24.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26012</th>\n",
       "      <td>f62183065f9c740c882dd1162e2bd65d</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>1602.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26104</th>\n",
       "      <td>f75016d189faa0b4574467ef9f4ad69f</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>30089.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26158</th>\n",
       "      <td>f7ba3a879736f134557d7c97a1ab0c70</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>16</td>\n",
       "      <td>17</td>\n",
       "      <td>1144.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26162</th>\n",
       "      <td>f7ba3a879736f134557d7c97a1ab0c70</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>181.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26168</th>\n",
       "      <td>f7bc4e348aca691d4d421e7840be5310</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>956.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26169</th>\n",
       "      <td>f7bc4e348aca691d4d421e7840be5310</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>4160.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26177</th>\n",
       "      <td>f7c7bca0118d9e8de245c18271112861</td>\n",
       "      <td>2017-09-01</td>\n",
       "      <td>17</td>\n",
       "      <td>18</td>\n",
       "      <td>265.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26242</th>\n",
       "      <td>f865d7a07071867691880860b88f5920</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>15</td>\n",
       "      <td>16</td>\n",
       "      <td>13676.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26413</th>\n",
       "      <td>fa24d96b582ddcc5a7a703ea906f6017</td>\n",
       "      <td>2017-09-03</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>2280.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26691</th>\n",
       "      <td>fd72e38fc42ca61622e34ee04e4d90b3</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>9211.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26793</th>\n",
       "      <td>fe9df607f56f5519c36bf5b42c99967f</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>13</td>\n",
       "      <td>14</td>\n",
       "      <td>6645.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26794</th>\n",
       "      <td>fe9df607f56f5519c36bf5b42c99967f</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>14</td>\n",
       "      <td>15</td>\n",
       "      <td>765.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26796</th>\n",
       "      <td>fe9df607f56f5519c36bf5b42c99967f</td>\n",
       "      <td>2017-09-04</td>\n",
       "      <td>8</td>\n",
       "      <td>9</td>\n",
       "      <td>41.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>26812</th>\n",
       "      <td>feb1faf59fdb0e412e342af7ae63eda3</td>\n",
       "      <td>2017-09-02</td>\n",
       "      <td>12</td>\n",
       "      <td>13</td>\n",
       "      <td>3502.0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>554 rows × 5 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "                            provider_id        date  start_time  end_time  \\\n",
       "31     0037dfffff8b03bbdf366a263735e84b  2017-09-04           8         9   \n",
       "42     004e25e0b5a3d2291caf267eff79f6d6  2017-09-04           9        10   \n",
       "89     011ad7e6d66c66bec6fcac31cc57023e  2017-09-01          16        17   \n",
       "164    02268a4ad1f08ee094c61fb2d5657521  2017-09-04           8         9   \n",
       "177    024650358f1918bf254a14f960b5490b  2017-09-04           8         9   \n",
       "193    02bb4143b597c4507773c38ec948fbc8  2017-09-02          13        14   \n",
       "194    02bb4143b597c4507773c38ec948fbc8  2017-09-02          14        15   \n",
       "269    036311d234645b6de50bd96059f56d9f  2017-09-02          14        15   \n",
       "326    039ff0381e9ef2edee519030b3ce73de  2017-09-03          12        13   \n",
       "328    039ff0381e9ef2edee519030b3ce73de  2017-09-04           8         9   \n",
       "451    04a153f1d81330c8f2b1c7c868857263  2017-09-02          15        16   \n",
       "469    04cd3aeb42e370d7c2af6f0780ef97ee  2017-09-01          12        13   \n",
       "476    04cd3aeb42e370d7c2af6f0780ef97ee  2017-09-03          13        14   \n",
       "477    04cd3aeb42e370d7c2af6f0780ef97ee  2017-09-03          16        17   \n",
       "498    0503f1b4cd1f01165cc4fbfc0bcbd422  2017-09-01          15        16   \n",
       "499    0503f1b4cd1f01165cc4fbfc0bcbd422  2017-09-02          12        13   \n",
       "503    0503f1b4cd1f01165cc4fbfc0bcbd422  2017-09-03          16        17   \n",
       "577    059988a086a8518fbf0f5b8fac2862e5  2017-09-03          12        13   \n",
       "593    05c9a2a23135e81c281d608194da67a6  2017-09-03          15        16   \n",
       "616    06137a5417cfe667bb66d3871a08d775  2017-09-03          15        16   \n",
       "642    066d50597ba4c36d2417d09e2c2bd775  2017-09-02          16        17   \n",
       "748    071aeff24666fc1d2f0550df11446165  2017-09-03          16        17   \n",
       "906    086117a0b40f5b676e9bfedf6de6a4ac  2017-09-04           8         9   \n",
       "1081   09b8484fd23fa45717ae80f425faee7d  2017-09-04           8         9   \n",
       "1141   0a456b121c3360f05ae790d5c6ccc569  2017-09-03          11        12   \n",
       "1142   0a456b121c3360f05ae790d5c6ccc569  2017-09-03          13        14   \n",
       "1143   0a456b121c3360f05ae790d5c6ccc569  2017-09-03          15        16   \n",
       "1227   0ac613121e461c30d2fe61cb34a13d46  2017-09-03          14        15   \n",
       "1264   0b09fe3f401e3154e41d6bb82c98efd7  2017-09-04           9        10   \n",
       "1373   0c5dbdd8e8409f5bee1afff6be8db051  2017-09-01          14        15   \n",
       "...                                 ...         ...         ...       ...   \n",
       "25244  edad0d4f0a9e49da3cf3123c62f20551  2017-09-04           8         9   \n",
       "25361  eeb5988caf7b600a46c4fcfc87c379b4  2017-09-02          12        13   \n",
       "25466  ef76e67d6420e76ac83bc8606580aabf  2017-09-02           8         9   \n",
       "25469  ef76e67d6420e76ac83bc8606580aabf  2017-09-04           8         9   \n",
       "25601  f14f3f8ae4624f9b9ec0a71a1a997360  2017-09-03          12        13   \n",
       "25602  f14f3f8ae4624f9b9ec0a71a1a997360  2017-09-03          14        15   \n",
       "25603  f14f3f8ae4624f9b9ec0a71a1a997360  2017-09-03          15        16   \n",
       "25676  f283267a59e87510cdfb8b922f8222c5  2017-09-02          12        13   \n",
       "25681  f283267a59e87510cdfb8b922f8222c5  2017-09-04           8         9   \n",
       "25682  f285365b7b1e477dfbc12ef3ed795189  2017-09-02          13        14   \n",
       "25685  f285365b7b1e477dfbc12ef3ed795189  2017-09-03           9        10   \n",
       "25690  f285365b7b1e477dfbc12ef3ed795189  2017-09-04           9        10   \n",
       "25779  f39aa01938a5732a2465c2a7918b33cc  2017-09-02          13        14   \n",
       "25782  f39aa01938a5732a2465c2a7918b33cc  2017-09-03          14        15   \n",
       "25812  f3f712d5abc1461e8a8bfe1687921dd6  2017-09-02          14        15   \n",
       "26008  f62183065f9c740c882dd1162e2bd65d  2017-09-01          12        13   \n",
       "26012  f62183065f9c740c882dd1162e2bd65d  2017-09-02          15        16   \n",
       "26104  f75016d189faa0b4574467ef9f4ad69f  2017-09-03          16        17   \n",
       "26158  f7ba3a879736f134557d7c97a1ab0c70  2017-09-02          16        17   \n",
       "26162  f7ba3a879736f134557d7c97a1ab0c70  2017-09-04           8         9   \n",
       "26168  f7bc4e348aca691d4d421e7840be5310  2017-09-02          13        14   \n",
       "26169  f7bc4e348aca691d4d421e7840be5310  2017-09-02          14        15   \n",
       "26177  f7c7bca0118d9e8de245c18271112861  2017-09-01          17        18   \n",
       "26242  f865d7a07071867691880860b88f5920  2017-09-02          15        16   \n",
       "26413  fa24d96b582ddcc5a7a703ea906f6017  2017-09-03          13        14   \n",
       "26691  fd72e38fc42ca61622e34ee04e4d90b3  2017-09-02          14        15   \n",
       "26793  fe9df607f56f5519c36bf5b42c99967f  2017-09-02          13        14   \n",
       "26794  fe9df607f56f5519c36bf5b42c99967f  2017-09-02          14        15   \n",
       "26796  fe9df607f56f5519c36bf5b42c99967f  2017-09-04           8         9   \n",
       "26812  feb1faf59fdb0e412e342af7ae63eda3  2017-09-02          12        13   \n",
       "\n",
       "       seconds_online  \n",
       "31             1760.0  \n",
       "42             3923.0  \n",
       "89             8017.0  \n",
       "164            2046.0  \n",
       "177              56.0  \n",
       "193            2669.0  \n",
       "194            1978.0  \n",
       "269           19588.0  \n",
       "326             576.0  \n",
       "328              81.0  \n",
       "451            3822.0  \n",
       "469           10341.0  \n",
       "476            1484.0  \n",
       "477            8701.0  \n",
       "498            9953.0  \n",
       "499            1644.0  \n",
       "503            2629.0  \n",
       "577              48.0  \n",
       "593            1196.0  \n",
       "616            5450.0  \n",
       "642             266.0  \n",
       "748            7754.0  \n",
       "906            1746.0  \n",
       "1081            475.0  \n",
       "1141          13009.0  \n",
       "1142           6569.0  \n",
       "1143           5580.0  \n",
       "1227           2444.0  \n",
       "1264           3657.0  \n",
       "1373            874.0  \n",
       "...               ...  \n",
       "25244          2501.0  \n",
       "25361           667.0  \n",
       "25466           947.0  \n",
       "25469             7.0  \n",
       "25601           168.0  \n",
       "25602          7394.0  \n",
       "25603           264.0  \n",
       "25676         14836.0  \n",
       "25681          1761.0  \n",
       "25682            48.0  \n",
       "25685           146.0  \n",
       "25690          3833.0  \n",
       "25779           119.0  \n",
       "25782           851.0  \n",
       "25812          2187.0  \n",
       "26008            24.0  \n",
       "26012          1602.0  \n",
       "26104         30089.0  \n",
       "26158          1144.0  \n",
       "26162           181.0  \n",
       "26168           956.0  \n",
       "26169          4160.0  \n",
       "26177           265.0  \n",
       "26242         13676.0  \n",
       "26413          2280.0  \n",
       "26691          9211.0  \n",
       "26793          6645.0  \n",
       "26794           765.0  \n",
       "26796            41.0  \n",
       "26812          3502.0  \n",
       "\n",
       "[554 rows x 5 columns]"
      ]
     },
     "execution_count": 267,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_final1[df_final1[\"seconds_online\"] > 0.0]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 2",
   "language": "python",
   "name": "python2"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 2
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython2",
   "version": "2.7.13"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}



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
