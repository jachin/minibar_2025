# Building a Smart Door Bell and E-ink Home Dashboard
---

# Amateur
## Former Full Time Software Engineer
### The frontend of web apps

- A long time ago I was a full stack web developer
- Long and slow migration to web frontend
- Long and slow migration to functional programming and strongly typed languages
- Mostly a stay-at-home Dad now
- Less time in front of my computer
- More variety of technology projects

***
# The Smart Home
## My Personal Goals
- Things should work when the network is down
- Things should work when the Internet is down
- Network traffic should stay in my home
- My phone should stay in pocket

;; Note: These are aspirational. I buy things that don't help me achieve these goals, but only when I can't find a good option
*** :: master: title1

![](ring_battery_doorbell_satin_nickel_1500x1500.jpg)

# The Door Bell
## Why Not Just get a "Ring"

- It didn’t fit on our door frame
- Not in alignment with my goals
- I'm not opposed to getting a video doorbell, but I'm not sure I want one either


*** :: master: regular2, alignment: right
# Inspiration
## Lots of YouTube
***

![](AnotherMakerYouTubeVideo.png)

;; Note: This video was great. It was short. It was exactly the kind of doorbell I already had. It has some cool extra stuff like, powering the board off the AC power.

***

![](Screenshot_2025-05-02_at_9.53.54 AM.png)

***
# That looks easy (and fun)

--- :: alignment: vertical, master: title2
# It was fun
***
# The Process
## Lots of starts and stops
- About 6 months
- Shopping at least 4 times
***
# The Process
## The Steps
 - Find an ESP32 board 
    - there are a lot of choices
 - Find the sensor
     - Assemble the sensor
 - Find out which breaker the Door Bell is on
     - Should have done it with a friend
 - Testing
    - Should have it with a friend
 - 3D print a "case"
***

![](IMG_3395.HEIC.jpg)

***

![](door-bell-buzzer.jpeg)

***

![](door-bell-press-detection-system-hanging-out-of-the-cieling.jpeg)

***
![](three-revisions-of-mounting-plate.jpeg)


*** :: alignment: vertical

![](door-bell-fully-armed-and-operational.jpeg)

***
# Things I learned
- The baud rate was set wrong (for programming the board)
- Make it easy to test
- Read the documentation on your board, especially what the pins do
- Talk to an LLM about terms you don't know
- Not much code
***
# The Code (Part 1)
## Setting the table
```
#include <ETH.h>❶
#include <HTTPClient.h>❶

const int sensorPin = 35;❷
int senseDoorbell = 0;              // variable to hold doorbell sensor reading❷
int debounce = 1000;   ❸             // only allow one DingDong per second❸
unsigned long currentMillis = 0;    // how many milliseconds since the Arduino booted❹
unsigned long prevRing = 0;         // The last time the doorbell rang❹

const char* serverName = "https://hubitat.lan/apps/api/358/devices/256/push/1?access_token=#######";❺
```

*** :: master: title2
# The Code (Part 2)
## Setup Function
```
void setup() {❶
  Serial.begin(115200);❷
  ETH.begin(); // Initialize Ethernet with DHCP❷
  Serial.println("Attempting to connect to the network."); // Wait for connection❷
  while (!ETH.linkUp()) {❷
    Serial.print(".");❷
    delay(1000);❷
  }
}❶
```


*** :: master: title2

# The Code (Part 3)
## Loop Function
```
void loop() {❶
  currentMillis = millis();   // Get the time since the Arduino booted❷
  // Only check the doorbell if it hasn't been hit in the last second❷
  if(currentMillis - prevRing >= debounce){❷
    senseDoorbell = analogRead(sensorPin);// Read the doorbell sensor❸
    if (senseDoorbell > 1) { // Adjust the threshold based on your sensor❸
      Serial.println("DingDong");❸
      prevRing = currentMillis; // Engage debounce mode❸
      sendHTTPGET();❸
    }❸
  }❷
}❶
```

*** :: master: title2

# The Code (Part 4)
## Http Request
```
void sendHTTPGET() {
  if (ETH.linkUp()) { // Check if the Ethernet connection is active
    HTTPClient http;
    http.begin(serverName);  // Specify the URL
    int httpCode = http.GET(); // Make the request
    if (httpCode > 0) {
      if (httpCode == HTTP_CODE_OK) {String payload = http.getString();}
    } else {
      Serial.printf("error: %s\n", http.errorToString(httpCode).c_str());
    }
    http.end(); // Close the connection
  } else { Serial.println("Ethernet not connected!"); }
}
```

*** :: master: title2

![](c7-image_a0da6496-0e12-4e3a-93fc-755655c8e46c.png)

# The Hubitat
- Webhook (easy to setup)
- Do anything with the door bell as I could with a button
- Send a notification to phones
- Plays "Ding Dong" on the Sonos speakers


*** :: master: regular2, alignment: right
# Dashboard
## E-Ink on the Fridge


***

![](iPads_in_picture_frames.jpg)


# Why not get an iPad
## Maybe in a picture frame

*** :: alignment: right, master: title1

![](iPad_in_a_picture_frame_from_the_back.jpg)

# iPad
## Pros and Cons
- Nice Screen ✅
- Touch Screen 👎
- Expensive 👎 💸
- Lots of software ✅
- Already over committed to the Apple Ecosystem. ✅ 👎
- Power 👎 🪫
- Could show video 🎥 😐

*** :: master: title2, alignment: right

![](skylight.jpg)

# Why not get a Skylight
## So big! So pretty🤩

*** :: alignment: right, master: title1

![](skylight_on_a_counter.jpg)

# Skylight
## Pros and Cons
- Touch Screen 👎
- Looks Really Nice ✅
- Power 👎
- Subscription 👎
- No API for my own apps 👎
- The Apps it does have probably work pretty well. ✅
*** :: alignment: right, master: title2

![](pi-dashboard-front.png)

# Why not make a Digital Sign
## With a Raspberry Pi
*** :: alignment: right

![](pi-dashboard-front-2.png)

# Raspberry Pi
## Pros and Cons
- A friend did this already 👍
- Sounds like a lot of work 👎
- Many options to add my own software ✅
- Probably would not want to run it off Batteries 👎 🪫
- Would make a great Minnebar talk ✅
- Lots of Display Options ✅
- Another Computer to Manage 👎 👍
*** :: alignment: right

![](inky-frame.png)

# Rolling my own device with an E-Ink Display
## Sounds like fun but a lot of work

*** :: alignment: right

![](inky-frame-2.png)

# Building my own E-Ink Display
## Pros and Cons
- The Hardware APIs look simple (just an image) ✅
- What to do for a case? 😱
- Running off a battery is probably fine 🔋
- Could go color 🎨
- Many options to add my own software ✅
- Would make a great Minnebar talk ✅
- Sounds like a lot of work 👎

;; Note: Pimoroni has an Inky Frame Project that looks like a lot of fun to make
*** :: master: regular2, alignment: right
# If only someone else had already done a bunch of the work...
***

![](Screenshot_2025-04-28_at_11.42.22 AM.png)

# TRMNL
 ## Someone else _did_ a bunch of the work
 
*** :: alignment: right

![](TRMNL_Back.png)
# TRMNL
## Pros and Cons
- Not a lot of work ✅
- Battery that lasts a long time ✅
- 3D printable mount for fridge (no holes in the wall, not taking up counter space) ✅
- I can make my own apps ✅
- I can flash my own firmware ✅
- I could build my own hardware ✅
- Not a touch screen ✅
- Not real time 👎
- $140 + $20
*** :: alignment: right, master: regular2

![](IMG_3408_1.jpeg)

***
# How does the TRMNL Work
## Simple you say
- The API just wants a picture and the time to update next
- Fewer updates means longer battery life
- ESP32 (on a custom board)
    - That’s the same chip in the door bell
- Small battery that can be replaced

*** :: alignment: vertical
# Sure, but did you do anything custom?
## Plugging in a consumer electronic, reeeeeeeal exciting. 🙄
- Backyard Weather
- ~~TODOist integration~~
- 3D printed magnetic mount (for refrigerator)
- Degree Days

***
# Making a "Private Plugin"
## It’s CSS/HTML

***

![](Screenshot_2025-04-28_at_11.47.44 AM.png)

***

![](Screenshot_2025-04-28_at_11.48.39 AM.png)

***

![](IMG_3410.jpeg)

# Backyard Weather Station
## Ambient Weather WS-2902D Web G
*** :: alignment: right
# Web Services and APIs
## Back in my Wheel House
- Weather Station Base Unit sends the current weather to
- Weather Data Collector
    - Sqlite Database
    - APIs for collecting the weather data
    - APIs for getting the current weather
    - Expose this on a URL where the TRMNL Cloud Service can see it
***
# JSON for the Private Plugin
```
{
  "uv": 0,
  "tempf": 52.9,
  "battout": 1,
  "dateutc": "2025-04-22 04:34:47",
  "tempinf": 71.8,
  "winddir": 78,
  "humidity": 49,
  ...
 "solarradiation": 0,
  "human_friendly_time": "Monday 11:34 PM"
}
```

*** :: alignment: vertical, master: title2
# HTML for the Private Plugin
```
<div class="title_bar">
  <span class="title">Weather Station</span>
  <span class="instance">{{ current_weather.human_friendly_time }}</span>
</div>
<div class="item border--h-1 mb--3 mt--3">
  <div class="content">
    <span class="title title--small">Outside Tempature</span>
    <span class="value value--large value--tnums">{{ current_weather.tempf }}F</span>
  </div>
</div>
```

*** :: master: title2
# TODOist Private Plugin
## Webhooks!
- When I was setting up my Dashboard there was no plugin for TODOist so I rolled m own
- It used web hooks, which was cool because I did not need to expose an API
- Now there’s a built in plugin for TODOist
*** :: master: title2

![](TRMNL_Fridge_Mounts.png)
# Fridge Mounts
## An Email from Ryan
- “If private feedback is more your style, just reply to this email.”
- We sent some emails, turns out his wife had made some 3D Printable mounts
- I had bought a 3D printer a couple months ago
- I did have to buy some magnets
*** :: alignment: right
# Degree Days 🌱 Talk to me later and maybe a talk at Minnebar 2026
***
# How is it going?
## Living with these projects for a couple months
- They both work pretty well
- I have ideas to make both of them better and more useful
- I’m happy with how well they are working right now
- Both work better than other aspects of my “smart home”
- I’d recommend both projects to beginners like me

***
# Questions?
# What have you made?
***
# Thank you
## jachin on Github
## jachin@jachin.rupe.name
## https://jachin.rupe.name