---
layout: post
title: Salesforce Formula Calculate Years and Months Between 2 Dates
tags: [Formula]
categories: [AppBuilder]
---

To calculate Years and Months in Salesforce formula field between 2 dates.Use case:Calculate how many years and months work experience from an employee start work date.Formula Field, return Text:

```javascript
TEXT(YEAR(TODAY()) - YEAR(Start_Date__c)
- 
if((MONTH(TODAY()) < MONTH(Start_Date__c)) 
|| 
((MONTH(TODAY()) == MONTH(Start_Date__c)) && 
(DAY(TODAY()) < DAY(Start_Date__c)) 
) 
,1,0)) 

& " Years " & 

TEXT(MOD((MONTH(TODAY()) - MONTH(Start_Date__c) 
+ 12*(YEAR(TODAY())-YEAR(Start_Date__c)) 
- if(DAY(TODAY()) < DAY(Start_Date__c),1,0)) 
, 12)) 

& " Months"
```
