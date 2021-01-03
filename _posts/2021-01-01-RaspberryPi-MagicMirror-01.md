---
title: "[Raspberry Pi] Magic Mirror 만들기 - MagicMirror 설치"
categories: 
  - RaspberryPi,
  - MagicMirror,
  - SmarMirror
toc: true
toc_sticky: true
---

놀고 있는 Raspberry Pi를 액자로 만들었던 과정을 기록
Part 2 : MagicMirror 설치 및 모듈에 대한 설명


## MagicMirror 설치
[MagicMirror](https://docs.magicmirror.builders/getting-started/installation.html) 페이지에 잘 정리가 되어 있지만 간단하게 구축 과정을 설명하겠습니다.

다양한 방식으로 설치해서 사용 할 수 있지만, 저는 Manual Installation으로 진행해서 설치 했습니다.
아래와 동일하게 진행 한다면 별 문제 없이 구동 되는 것을 확인 할 수 있다. 
``` bash
# Download and install the latest Node.js version
$ curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
$ sudo apt install -y nodejs

# Clone the repository and check out the master branch: 
$ git clone https://github.com/MichMich/MagicMirror

# Enter the repository: 
$ cd MagicMirror/

# Install the application: 
$ npm install
# Make a copy of the config sample file: 
$ cp config/config.js.sample config/config.js

# Start the application: 
# For Server Only use: npm run server .
$ npm run start

```

## Module 설정
설치가 되면 우리가 원하는 모듈을 설치하고 설정 해야됩니다.

MagicMirror는 8가지 모듈도 함께 설치가 됩니다.

모듈을 설정하기 위해서 모듈 설정 파일에 대해 알아보겠습니다. 
설치 과정에서 복사한 파일 `config/config.js` 을 열어보면 아래처럼 modules를 확인 할 수 있습니다. 
modules array 블럭안에 사용할 모듈별 설정값을 셋팅 하면 됩니다.

``` json
// default module config 
modules: [
    {
        module: "alert",
    },
    {
        module: "updatenotification",
        position: "top_bar"
    },
    {
        module: "clock",
        position: "top_left"
    },
    {
        module: "calendar",
        header: "US Holidays",
        position: "top_left",
        config: {
            calendars: [
                {
                    symbol: "calendar-check",
                    url: "webcal://www.calendarlabs.com/ical-calendar/ics/76/US_Holidays.ics" 
                }
            ]
        }
    },
    {
        module: "compliments",
        position: "lower_third"
    },
    {
        module: "currentweather",
        position: "top_right",
        config: {
            location: "New York",
            locationID: "", //ID from http://bulk.openweathermap.org/sample/city.list.json.gz; unzip the gz file and find your city
            appid: "YOUR_OPENWEATHER_API_KEY"
        }
    },
    {
        module: "weatherforecast",
        position: "top_right",
        header: "Weather Forecast",
        config: {
            location: "New York",
            locationID: "5128581", //ID from http://bulk.openweathermap.org/sample/city.list.json.gz; unzip the gz file and find your city
            appid: "YOUR_OPENWEATHER_API_KEY"
        }
    },
            {
        module: "newsfeed",
        position: "bottom_bar",
        config: {
            feeds: [
                {
                    title: "New York Times",
                    url: "https://rss.nytimes.com/services/xml/rss/nyt/HomePage.xml"
                }
            ],
            showSourceTitle: true,
            showPublishDate: true,
            broadcastNewsFeeds: true,
            broadcastNewsUpdates: true
        }
    },
]
```

| Option | required | Description | value  |
| module | O | 모듈 이름 <br>(하위 드렉토리도 지정 가능 )|  |
| position | X | 모듈의 위치를 지정 한다. | `top_bar`, `top_left`, `top_center`, `top_right`, <br>`upper_third`, `middle_center`, `lower_third`, <br>`bottom_left`, `bottom_center`, `bottom_right`, `bottom_bar`, <br>`fullscreen_above`, `fullscreen_below` |
| classes | X | 모듈에 설정되는 css class | |
| header | X | 모듈 위에 디스플레이되는 값 | |
| disabled | X | 모듈 생성 스킵 | |
| config | X | 모듈에서 사용하는 설정 값 | |


