---
permalink: /bash.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

9th Sep 2020

# Back to bash

Lets imagine that you have a front-end pure HTML/CSS/JS code or used a modern framework to build it. You have deployed it behind a file server like Nginx and you like to renew the data that is fed into the app from Google Sheets/similar source. 

It is the days of COVID19, your data source is an excel sheet on Google Docs. You want to process them into some other format and you are able to setup your `cron` jobs but just no time to configure Python/R/Node other to get something up and running.

What I mean is this: if you want to consume a CSV coming originally from a Google sheet you can not use it directly in your front end code. This will hit the famous CORS (Cross Origin Resource Sharing) policy of the browser. So, data has to come from your own server where your front end is served. How do you do this? Well you can download the Google sheet and turn it into CSV in Python/R or maybe even Node.

The alternative to these languages is doing it in bash. So, back to bash for me. Instead of installing an R package which then requires dependencies to download the sheet and convert it into CSV/JSON, and I did do this, you can get the data using bash, make some changes using `sed` (a true Swiss army knife) and serve the application using your modern JS bundle.

It is worth much more than a short article but I just wanted to point out how easy it is once your cron script is something basic like:

```sh
#!/bin/sh
# 1
cd desired/path
# git pull #| grep 'Already up to date.'
# or some docker instance
# 2
# update some part of front end file: for instance index.html file with a stamp
sed -i "s/Last*/Last: $(date '+%Y-%m-%d %H:%M')/" index.html
# 3
curl 'https://docs.google.com/spreadsheets/d/doc_id/export?exportFormat=csv' -o mycsv.csv
# do some loggin
# 4
echo $(date -u) "Last update" >> /update.log
```
This is a simple bash script that can run on a Unix machine and does the following:
1. Chand directory to the public directory
2. Download the file from Google Sheets (assuming link is enabled or set to public)
3. Make some local changes

Finally, you need to find your favourite tutorial to setup a cron job that runs this script on your desired intervals. If the sheet is edited on hourly basis then so be it.

What did do achieve?
* We no longer need a higher level programmin language to just grab data source files. 
* We do not need to manually timestamp or make some changes to source code before going live.
