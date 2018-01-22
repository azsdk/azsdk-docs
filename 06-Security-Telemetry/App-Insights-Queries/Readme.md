## Contents

- [Overview](#overview)
- [Subscriptions monitoring](#subscriptions-monitoring)

## Overview
[TODO]

## Subscriptions monitoring
[TODO]

#### Attestation conflicts in different versions 

``` AIQL
let newVersion = <newVersion>; //e.g. "2.9.0"
let oldVersion = <oldVersion>; //e.g. "2.8.1"

let combinedResult = customEvents
| where timestamp >= ago(3d)
| where customDimensions.ScannerVersion == newVersion
| where  customDimensions.ScanSource =="Runbook" and name == "Control Scanned" 
| summarize arg_max(timestamp, *) by tostring(customDimensions.SubscriptionId), tostring(customDimensions.SubscriptionName), tostring(customDimensions.ControlId)
| project tostring(customDimensions.SubscriptionId), tostring(customDimensions.SubscriptionName),tostring(customDimensions.ResourceId), tostring(customDimensions.ControlId), oldresult =tostring(customDimensions.AttestationStatus)
| join (customEvents
| where timestamp >= ago(3d)
| where customDimensions.ScannerVersion == oldVersion
| where  customDimensions.ScanSource =="Runbook" and name == "Control Scanned" 
| summarize arg_max(timestamp, *) by tostring(customDimensions.SubscriptionId), tostring(customDimensions.SubscriptionName), tostring(customDimensions.ControlId)
| project tostring(customDimensions.SubscriptionId), tostring(customDimensions.SubscriptionName),tostring(customDimensions.ResourceId), tostring(customDimensions.ControlId), newresult = tostring(customDimensions.AttestationStatus)) on customDimensions_SubscriptionId, customDimensions_SubscriptionName,customDimensions_ResourceId, customDimensions_ControlId
| project tostring(customDimensions_SubscriptionId), tostring(customDimensions_SubscriptionName),tostring(customDimensions_ResourceId), tostring(customDimensions_ControlId),oldresult,newresult;

let oldScan = combinedResult
| summarize oldScanCount = count() by tostring(customDimensions_ControlId),oldresult;

let newScan = combinedResult
| summarize newScanCount = count() by tostring(customDimensions_ControlId),newresult;

 
oldScan | join (newScan) on customDimensions_ControlId,$left.oldresult==$right.newresult
| project ControlId=tostring(customDimensions_ControlId),AttestationStatus=oldresult,StatusCountInOldModule=oldScanCount,StatusCountInNewModule=newScanCount
| where StatusCountInOldModule != StatusCountInNewModule

```

#### CA job errors occurred after specific date

``` AIQL
let notBeforeTimestamp = <notBeforeTimestamp>; //e.g. datetime(2018-01-16T05:30:00)

customEvents 
| where timestamp >= notBeforeTimestamp
| where name has "CA" and name has "Error"
| distinct tostring(customDimensions.SubscriptionId),name,tostring(customDimensions.ErrorRecord)
```

#### CA job errors occurred after specific date

``` AIQL
let notBeforeTimestamp = <notBeforeTimestamp>; //e.g. datetime(2018-01-16T05:30:00)

customEvents 
| where timestamp >= notBeforeTimestamp
| where name has "CA" and name has "Error"
| distinct tostring(customDimensions.SubscriptionId),name,tostring(customDimensions.ErrorRecord)
```

#### Controls reporting 'Error' status in last 24 hours

``` AIQL
let moduleVersion = <moduleVersion>; //e.g. "2.9.0"
let controlResult = "Error";

customEvents 
| where customDimensions.ScannerVersion == moduleVersion
| where customDimensions.VerificationResult == controlResult
| summarize ScanCount = count() by tostring(customDimensions.SubscriptionId),tostring(customDimensions.ControlId),tostring(customDimensions.SubscriptionName)
| sort by ScanCount
```

#### Discrepancy in control scan results between different versions

``` AIQL
let notBeforeTimestamp = <notBeforeTimestamp>; //e.g. datetime(2018-01-16T05:30:00)
let oldVersion = <oldVersion>; //e.g. "2.8.1"
let newVersion = <newVersion>; //e.g. "2.9.0"

let combinedResult = customEvents
| where timestamp >= notBeforeTimestamp
| where customDimensions.ScannerVersion == newVersion
| where  customDimensions.ScanSource =="Runbook" and name == "Control Scanned" 
| summarize arg_max(timestamp, *) by tostring(customDimensions.SubscriptionId), tostring(customDimensions.SubscriptionName), tostring(customDimensions.ControlId)
| project tostring(customDimensions.SubscriptionId), tostring(customDimensions.SubscriptionName),tostring(customDimensions.ResourceId), tostring(customDimensions.ControlId), ControlResult =tostring(customDimensions.VerificationResult)
| join (customEvents
| where timestamp >= notBeforeTimestamp
| where customDimensions.ScannerVersion == oldVersion
| where  customDimensions.ScanSource =="Runbook" and name == "Control Scanned" 
| summarize arg_max(timestamp, *) by tostring(customDimensions.SubscriptionId), tostring(customDimensions.SubscriptionName), tostring(customDimensions.ControlId)
| project tostring(customDimensions.SubscriptionId), tostring(customDimensions.SubscriptionName),tostring(customDimensions.ResourceId), tostring(customDimensions.ControlId), newresult = tostring(customDimensions.VerificationResult)) on customDimensions_SubscriptionId, customDimensions_SubscriptionName,customDimensions_ResourceId, customDimensions_ControlId
| project tostring(customDimensions_SubscriptionId), tostring(customDimensions_SubscriptionName),tostring(customDimensions_ResourceId), tostring(customDimensions_ControlId),ControlResult,newresult;

let oldScan = combinedResult
| summarize OldScanCount = count() by tostring(customDimensions_ControlId),ControlResult;

let newScan = combinedResult
| summarize NewScanCount = count() by tostring(customDimensions_ControlId),newresult;

 
oldScan | join (newScan) on customDimensions_ControlId,$left.ControlResult==$right.newresult
| project tostring(customDimensions_ControlId),ControlResult,OldScanCount,NewScanCount
| where OldScanCount != NewScanCount

```
#### Subscriptions in which CA upgrade to newer version is successful

``` AIQL
let moduleVersion = <moduleVersion>; //e.g. "2.9.0"
customEvents
| where timestamp >= ago(2d)
| where name == "Control Scanned"
| where customDimensions.ScanSource == "Runbook"
| where customDimensions.ScannerVersion == moduleVersion
| distinct tostring(customDimensions.SubscriptionId)  
```
#### CA certificate expiry check

``` AIQL
let startDate = <startDate>; //e.g. datetime(2018-01-1)
let endDate = <endDate>; //e.g. datetime(2018-01-15)

customEvents
| where  name contains "Error" and timestamp between (startDate..endDate)
| where  customDimensions.ErrorRecord  contains "Run Login-AzureRmAccount to login"
| summarize arg_min(timestamp,*) by tostring(customDimensions.SubscriptionId)
| project FirstTimeLoginErrorTime=timestamp, customDimensions_SubscriptionId
| join kind= inner (
    customEvents
| where  name contains "Error" and timestamp between (startDate..endDate)
| where  customDimensions.ErrorRecord  contains "Run Login-AzureRmAccount to login"
| summarize arg_max(timestamp,*) by tostring(customDimensions.SubscriptionId)
) on customDimensions_SubscriptionId 
| join kind= leftouter (
    customEvents
| where name == "Control Scanned" and timestamp between (startDate..endDate) and customDimensions.ScanSource =="Runbook"
| summarize arg_max(timestamp,*) by  tostring(customDimensions.SubscriptionId)
) on customDimensions_SubscriptionId 
| order by  timestamp1 desc
| project customDimensions_SubscriptionId, SubscriptionName= customDimensions.SubscriptionName,LastLoginErrorTime=timestamp,LastControlScanTime = timestamp1,FirstTimeLoginErrorTime
| extend IsCertificateStillExpired = iff((LastControlScanTime < LastLoginErrorTime ),"Yes","No") 
```
