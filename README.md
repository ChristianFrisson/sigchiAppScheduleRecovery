# SIGCHI app schedule recovery

Export your schedule made with the SIGCHI app to CSV files.

Created by [Christian Frisson](http://frisson.re) on May 7 2018.

This tutorial has currently been tested with SIGCHI app 0.6.3 installed on a rooted Android phone and a computer running macOS.

Disclaimer: use this tutorial at your own risk. This tutorial is not meant to infrige copyrights. The primary aim of this tutorial is to recover user-generated data that can not be directly exported from the SIGCHI app.

Feel free to submit issues and make pull requests.

## Pull the database from your (rooted Android) phone

Prerequisites:
- A rooted Android phone
- Android Platform tools: with Homebrew on macOS: 
```
brew cask install android-platform-tools
```

Connect your rooted Android phone to your computer and open a terminal.

Open a remote shell with the Android Debug Bridge (adb) tool:
```
adb shell
```

Change directory to a writable directory, for instance your SD card:
```
cd /sdcard
```

Gain super user access (to be able to browse apps data):
```
su
```

Copy the database:
```
cp /data/data/org.sigchi/databases/conference_db .
```

Exit super user access:
```
exit
```

Exit adb:
```
exit
```

Pull the database into your computer:
```
adb pull /sdcard/conference_db .
```

## Export the database to CSV files

Prerequisites:
- sqlite3: with Homebrew on macOS:
```
brew install sqlite3
```

### Export your schedule

Note: `PAPER_MODEL.CONFERENCE_ID = 10004` is for CHI 2018

Open the interactive sqlite shell on the database, set output mode to 'csv':
```
sqlite3 -csv conference_db
```

Turn display of headers on:
```
.headers on
```

Output to CSV file sigchiAppSchedule.csv:
```
.output sigchiAppSchedule.csv
```

Export your CHI2018 schedule in a single sql query:
```
select 
MY_PAPER_SCHEDULE_MODEL.SESSION_ID as SessionId,
SESSION_MODEL.NAME as SessionName,
MY_PAPER_SCHEDULE_MODEL.PAPER_ID as PaperId,
PAPER_MODEL.TITLE as PaperTitle,
PAPER_MODEL.SIMPLE_AUTHOR_LIST as Authors,
PAPER_MODEL.ABSTRACT_FIELD as Abstract,
SESSION_MODEL.START_DATE as SessionStartDate,
SESSION_MODEL.END_DATE as SessionEndDate,
MY_PAPER_SCHEDULE_MODEL.UPDATED_DATE as ScheduledSince 
from 
MY_PAPER_SCHEDULE_MODEL 
left join PAPER_MODEL on MY_PAPER_SCHEDULE_MODEL.PAPER_ID = PAPER_MODEL.EXTERNAL_ID 
left join SESSION_MODEL on MY_PAPER_SCHEDULE_MODEL.SESSION_ID = SESSION_MODEL.EXTERNAL_ID 
WHERE MY_PAPER_SCHEDULE_MODEL.IS_ADDED = 1 AND PAPER_MODEL.CONFERENCE_ID = 10004 
order by 
SESSION_MODEL.START_DATE ASC
;
```

### Export the full conference schedule

Note: `PAPER_MODEL.CONFERENCE_ID = 10004` is for CHI 2018

Open the interactive sqlite shell on the database, set output mode to 'csv':
```
sqlite3 -csv conference_db
```

Turn display of headers on:
```
.headers on
```

Output to CSV file sigchiAppFullSchedule.csv:
```
.output sigchiAppFullSchedule.csv
```

Export the full CHI2018 schedule in a single sql query:
```
select 
SESSION_MODEL.EXTERNAL_ID as SessionId,
SESSION_MODEL.NAME as SessionName,
PAPER_MODEL.EXTERNAL_ID as PaperId,
PAPER_MODEL.TITLE as PaperTitle,
PAPER_MODEL.SIMPLE_AUTHOR_LIST as Authors,
PAPER_MODEL.ABSTRACT_FIELD as Abstract,
SESSION_MODEL.START_DATE as SessionStartDate,
SESSION_MODEL.END_DATE as SessionEndDate 
from 
PAPER_MODEL 
left join SESSION_MODEL on PAPER_MODEL.SESSION_ID = SESSION_MODEL.ID 
WHERE PAPER_MODEL.CONFERENCE_ID = 10004 
order by 
SESSION_MODEL.START_DATE ASC
;
```
