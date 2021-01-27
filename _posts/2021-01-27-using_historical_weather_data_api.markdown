---
layout: post
title:      "Using Historical Weather Data API"
date:       2021-01-27 23:27:56 +0000
permalink:  using_historical_weather_data_api
---


Suggestion: [Visual Cross Weather Data Services](https://www.visualcrossing.com/weather/weather-data-services#/)

I was having a lot of trouble with how to get enough historical weather data for this project I'm working on. As I'm just learning about API and how to use `requests` and `json` in Python, what I'm pulling from the API is fairly basic.

### On any given day in the 2011 soccer* seanson was it raining in Berlin, Germany?
*I'm American, so football is soccer. I'm sorry




[Dark Sky API](https://darksky.net/dev) isn't taking any new sign ups, so I had to find other sources. The first one I found was [Open Weather Map](https://api.openweathermap.org) , and after I wrote out everything... I realized their historical data only goes back 5 days!!!! Since it's 2021, that's not going to work for data from 2011. Ultimately found [Visual Cross Weather Data Services](https://www.visualcrossing.com/weather/weather-data-services#/) and it works perfectly for what I need it for and will be a go to in the future for all kinds of historical weather data!

#### Some cool things I figured out along the way...

Making sure I'm getting a `200` response when calling APIs (see code below).

Coverting a datetime string into a UNIX timestamp. I don't have an exact time in this, but if you need an exact time... it has to first be converted to UTC time:
```
import time
import datetime
datetime_string = "2020-01-20"
dt = str(time.mktime(datetime.datetime.strptime(datetime_string, "%Y-%m-%d").timetuple()))[:9]
dt
```

Using `classes`. I'm still learning this one, but here's the first part:
```
class WeatherGetter():
    
    def __init__(self):
        self.secret_key = "[Personal API Key]"
        self.berlin_lat = "52.520007"
        self.berlin_lon = "13.404954"
        self.location = 'Berlin,GR'
        self.url_base = "https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline"
        
    def get_weather_data_for_date(self, datetime_string, verbose=True):
        full_url = "{}/{}%2C%20{}/{}?unitGroup=us&key={}"
				.format(self.url_base, self.berlin_lat, self.berlin_lon, datetime_string, self.secret_key)
        response = requests.get(full_url)
        if response.status_code == 200:
            if verbose:
                print(response.status_code)
            return response
        else: 
            raise ValueError("Error getting data from Visual Crossing API: Response Code {}"
						.format(response.status_code))
```

That's all I have for now.
