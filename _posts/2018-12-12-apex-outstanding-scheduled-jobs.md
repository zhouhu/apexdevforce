---
layout: post
title: Apex get outstanding scheduled jobs
tags: [Apex, Scheduled Job]
categories: [Apex]
---


Get outstanding scheduled Apex jobs

```java
public static Integer getOutstandingJobs(String jobName){
  // Job Types:
  // Data Export (0)
  // Dashboard Refresh (3)
  // Analytic Snapshot (4)
  // Scheduled Apex (7)
  // Report Run (8)
  // Batch Job (9)

  // The current state of the job. The job state is managed by the system. Possible values are:
  // WAITING: The job is waiting for execution.
  // ACQUIRED: The job has been picked up by the system and is about to execute.
  // EXECUTING: The job is executing.
  // COMPLETE: The trigger has fired and is not scheduled to fire again.
  // ERROR: The trigger definition has an error.
  // DELETED: The job has been deleted.
  // PAUSED: A job can have this state during patch and major releases. After the release has finished, the job state is automatically set to WAITING or another state.
  // BLOCKED: Execution of a second instance of the job is attempted while one instance is running. This state lasts until the first job instance is completed.
  // PAUSED_BLOCKED: A job has this state due to a release occurring. When the release has finished and no other instance of the job is running, the job’s status is set to another state.
  
  // hardcoded to type=7, as it is the schedule job
  CronTrigger[] cts = [SELECT Id, CronExpression, StartTime, EndTime, PreviousFireTime, NextFireTime, 
             State, CronJobDetail.Id, CronJobDetail.Name, CronJobDetail.JobType 
             FROM CronTrigger 
             WHERE CronJobDetail.JobType = '7' 
             AND State = 'WAITING'
             AND CronJobDetail.Name like :jobName+'%' ];
  // this query check for any "waiting" job that match the jobname... doesn't really care when it is fired
  // this check make sure we don't schdule more than 1 job with the same name
  //for(CronTrigger c: cts){system.debug(c);}
  return cts.size();
}
```
