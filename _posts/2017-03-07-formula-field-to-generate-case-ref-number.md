---
layout: post
title: Salesforce Formula field to generate Case ref number
tags: [Formula, Case]
categories: [AppBuilder]
---

V1

```javascript
TRIM(" [ ref:" + LEFT( $Organization.Id, 4) + RIGHT($Organization.Id, 4) +"."+ LEFT( Id, 4) + SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(Id, RIGHT( Id, 4), ""), LEFT( Id, 4), ""), "0", "") + RIGHT( Id, 4) + ":ref ] ")
```

V2 Case ref number used in Email communication.

```javascript
"ref:_"&LEFT( $Organization.Id,5)&SUBSTITUTE(RIGHT($Organization.Id,10),"0","")&"._"&LEFT(Id,5)&SUBSTITUTE(LEFT(RIGHT(Id,10),5),"0","")&RIGHT(Id,5)&":ref"
```
