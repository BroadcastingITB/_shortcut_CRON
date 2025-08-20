# _shortcut_CRON
## Introduction

CRON is a shortcut that uses iOS Alarms as a job scheduler (like the Linux command `cron`). It can run shortcuts at a date/time in the future, run them repeatedly (x times, or until {date}), use runtime parameters and predefined definitions. 

## Functional Overview
### creating jobs
CRON jobs are created using the shortcut `CRON_Submit_Request` with a JSON structure which defines:
- the delay until a shortcut should run
- the shortcut to be run amd any parameters
- any repeat/resubmission options

When the request is submitted, an alarm is created that will trigger an automation when it sounds. The alarm has a label with this structure:

`DD/MM/YY`\_CRON\_`NNN`\_`Label1`_`Label2`...\
where:
- `DD/MM/YY` = date alarm will run
- `NNN` = Request_ID
- `LabelX` = helpful labels 

#### Request Structure
The request will have either a Shortcut_Name (parameters in the Input) or a Program_ID (Shortcut Name and parameter defaults stored in Data Jar). This is the strcuture:

```
{
"Request_Parameters":
  {
    "Program_ID":"XXX" […XOR…]
    "Shortcut_Name":”Xxxxx",
    "Date_Time": "{Date}" […XOR…]
    "Delay":"NN hrs NN mins",
    "Announce":[Bool, optional]
  },
"Program_Parameters":
  {
    "Param1":"Value",
    …
  }
"Resubmission": [optional]
    { 
        "Delay":"NN hrs NN mins",
        "Until": "{Date}" […OR…]
        "Repeats":  NN
    }
}
```

### Resubmission choices
- If `"Resubmission":{...}` is not included, it runs once.
- If resubmission has Delay and “Repeats”:N, it repeats N times.
- If resubmission has Delay, but Repeats is omitted, it runs perpetually (until the alarm is deleted or disabled, either manually or by another process).
- If “until” date/time is given and would be exceeded then don’t resubmit (even if Repeats has not been exceeded). 

### When an alarm triggers
When any alarm triggers the automation (including non-CRON alarms) it runs `[CRON]`. This runs a sub routine twice, once with the perimeter "CURRENT" and once with "OTHERS". This ensures that the alarm which triggered the shortcut is dealt with first, so that the alarm can be deleted quickly (stopping vibration and sound). The subroutine is the shortcut `[CRON_Process_Alarms]`.

### Failure handling
If CRON calls a shortcut which fails, Cron will stop. To cope with this, CRON creates a temporary `\_CRON\_OnFail\_` alarm before running the shortcut, and deletes it after the shortcut has run. Therefore, if the shortcut fails, that alarm will trigger CRON later. `OnFail` is defined in the `\_CRON.Requests` Data Jar area with `Shortcut_Name = [CRON_OnFail]`.  

## Installation
### Summary of Components
1. CRON Setup `shortcut`
2. CRON `shortcut`
1. [CRON_Process_Alarms] `shortcut`
1. [CRON_OnFail] `shortcut`
1. CRON_Submit_Request `shortcut`
1. `automation` When any alarm goes off, run [CRON] immediately, don't notify

### Prerequisites
- Data Jar `iOS App` [Download](https://apps.apple.com/gb/app/data-jar/id1453273600)
- Data Jar - Data Setup `shortcut`

### Instructions

#### Install prerequisites
1. Data Jar `iOS App` [Download]
2. Data Jar - Data Setup [Install](https://www.icloud.com/shortcuts/435822740ac349919f2d1e41629b3890)

#### Install CRON shortcuts
2. CRON Setup [Install](https://www.icloud.com/shortcuts/e11a4ef814034dee9f01a1c49ecdd9da)
2. CRON [Install](https://www.icloud.com/shortcuts/111b82f888fd4c0693aa363d66b746bd)
1. [CRON_Process_Alarms] [Install](https://www.icloud.com/shortcuts/4fc3d10e8ddc420fa509faa99776a3a3)
1. [CRON_OnFail] [Install](https://www.icloud.com/shortcuts/909be7629e864a4e95f086809e51abb6)
2. CRON_Submit_Request [Install](https://www.icloud.com/shortcuts/94b08be2763e4173a1c9e71ab1b3f5c4)

#### Create automation
![Automation](images/automation_definition.jpeg)
