# schtasks-calendar (or schcal)

Google calendar events that contains a markdown link will be sync to Windows Task Scheduler so that the link will open automatically on time on your PC.

## Get Started

### 1. Suppose you need to add some events in the next few days.

Fill into the title field in the following format:
- `[ ...taskname ]( ...link )`
- `[ ...taskname ]( ...local_application_path )`

Example:
- `[ view schtasks-calendar ]( https://github.com/snomiao/schtasks-calendar )`

  ![]( images/view-schtasks-calendar.png)

### 2. Then go to the calendar settings.
<!-- TODO: NEED MORE IMAGES -->
Scroll down and copy the private ics url
  
![]( images/the-private-ics-url.png)

And you should get the ics url https://calendar.google.com/calendar/ical/xxxxxxxxxxxxxxxxxxx/private-cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/basic.ics


### 3. Then run using the command params.

```sh
npx schcal YOUR_ICS_URL
```

Example:
```sh
npx schcal https://calendar.google.com/calendar/ical/xxxxxxxxxxxxxxxxxxx/private-cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/basic.ics
```

You will see this

![]( images/npx%20schcal.png )

### 4. Check your schtasks

Press `Win + R` and type `taskschd.msc` to open Windows Task Scheduler

![]( images/Windows%20Tasks%20Scheduler%20SSAC%20task.png )

The task `SSAC-0820-0530-view schtasks-calendar-XXXXXX` corresponds to the event you just added to calendar and the link will be opened on time.

### 5. Configuring schtasks auto update (daily or whatever)

If you want to keep using this, you can configure the auto update (daily or whatever).

1. Run `mkdir schcal` in command line to create a directory.
2. Write the following to the config file
`schcal/config.yaml`
```yaml
ICS_URLS:
  - https://calendar.google.com/calendar/ical/xxxxxxxxxxxxxxxxxxx/private-cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/basic.ics
```

3. Write the following to the batch file 
`schcal/on-schtask.bat`
```bat
cd %~dp0
npx schcal > ./schcal.log
schtasks /Create /tn SSAC /sc daily /st 17:00 /tr %0 /F
```

4. Then run `on-schtask.bat` which will update your tasks at 17:00 every day (you can change this by editing the bat file)

5. Then run `schtasks /Run /tn SSAC` to test schtasks.

## Other Methods

### Run using command line.

```sh
npx schcal https://calendar.google.com/calendar/ical/xxxxxxxxxxxxxxxxxxx/private-cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/basic.ics
```

Or

```sh
npm i schtasks-calendar -g
schcal https://calendar.google.com/calendar/ical/xxxxxxxxxxxxxxxxxxx/private-cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/basic.ics
```

### Run using `config.yaml`

Make a `config.yaml` like this

```yaml
# Your ics urls, the order is not important
ICS_URLS:
  # snomiao's private calendar ( demo )
  - https://calendar.google.com/calendar/ical/snomiao%40gmail.com/private-d772b2790a1a73de26afb64188c5ca0a/basic.ics
  # a calendar
  - https://calendar.google.com/calendar/ical/xxxxxxxxxxxxxxxxxxx/private-cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/basic.ics
  # another calendar
  - https://calendar.google.com/calendar/ical/xxxxxxxxxxxxxxxxxxx/private-cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/basic.ics

# Optional, if you want cache the ics (usually for debuging)... default value is 0 (no cache and never save a cache file)
# CACHE_TIMEOUT: 3600e3 # one hour

# Optional, if you need a proxy for Google... Otherwise you can delete this line. default value is empty
HTTP_PROXY: http://localhost:1080

# Optional, how many days events will add to schtasks, default value is 7 (then this program will )
FORWARD_DAYS: 7        # TODO NEED A DIAGRAM
```

and run

```sh
npx schcal
```
in the working directory with `config.yaml` .

## Supported formats

You can put below into the title or description fields of events **which you want to launch the link or programs on the time.**

1. Web Links: `http://...` , `https://...` , `ftp://...` , `file://...`
1. Markdown Links: `[ ... ]( ... )`
1. Run Command: `RUN ...`

(supports urls, custom protocols and local files)

## TODOS

- [ ] Translate this into chinese version README.md 

## Q & A

- Q: I saw "Unexpected token ." when using `npx schcal`........
- A: You need to update your Nodejs to higher than v14.8.0  [Click to download](https://nodejs.org/en/download/)

## References

- [schtasks | Microsoft Docs]( https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/schtasks )
- [手把手教你使用nodejs编写cli(命令行) - 掘金]( https://juejin.im/post/6844903702453551111 )
- [PC Automation - IFTTT]( https://ifttt.com/applets/190903p-pc-automation )

