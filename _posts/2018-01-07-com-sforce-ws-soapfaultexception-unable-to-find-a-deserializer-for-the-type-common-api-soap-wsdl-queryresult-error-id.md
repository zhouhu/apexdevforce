---
title: com.sforce.ws.SoapFaultException Unable to find a deserializer for the type common.api.soap.wsdl.QueryResult Error Id
tags: [SoapFaultException, deserializer]
categories: [Integration]
---

com.sforce.ws.SoapFaultException: Unable to find a deserializer for the type common.api.soap.wsdl.QueryResult Error Id: 883671539-69937 (-428048666)

Resolve:

1. Check the Query String

    ```text
    QueryResult accountQueryResult = enterpriseConnection.query("SELECT Id, (SELECT Id FROM Contacts), Name, FROM Account WHERE Id = \'" + acctId + "\' LIMIT 1");
    ```

2. Notice the sub-query (SELECT Id FROM Contacts), the result of the above query has nested contact objects information.

3. Now start to use the account query result by casting it to Account acct = (Account)accountQueryResult.getRecords()[0];

4. Start using 'acct' as Account object, (remember it has nested contact object information), the ERROR may occurs if you use this 'acct' directly in other SOAP methods, as other methods may require a Account object without the nested Contact objects (Unable to find a deserializer for the type common.api.soap.wsdl.QueryResult). So process the 'acct' to remove the sub contact objects before using it in other methods.