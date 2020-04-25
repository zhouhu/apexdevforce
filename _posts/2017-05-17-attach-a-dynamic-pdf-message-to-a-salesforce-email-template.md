---
layout: post
title: Attach a Dynamic PDF Message to a Salesforce Email Template
tags: [Apex, Visualforce, PDF, EmailTemplate]
categories: [Apex]
---

Attach a Dynamic PDF Message to a Salesforce Email Template.The Email Template can be used in Email Alerts that will be triggered by Workflow Rule / Process / Flow ... etc

### We will create following items:

- A Visualforce page that will be rendered as PDF. (e.g. Receipt)
- A Apex Controller Class for the above VF page. (e.g. ReceiptController)
- A VF Component that to be referenced by the Visualforce Email Template. (e.g. ReceiptAttachment)
- A Apex Controller Class for the above VF Component. (e.g. ReceiptAttachmentController)An Visualforce Email Template.

### Sample Code List

#### Receipt.page

```html
<apex:page controller="ReceiptController" applyHtmlTag="false" applyBodyTag="false" showHeader="false" sidebar="false">
<html>
<span>Dynamic PDF data:  <apex:outputText value="{!ContactName}" escape="false"/></span>
</html>
</apex:page>
```

#### ReceiptController.class

```java
public class ReceiptController {

    public String ContactID {
        get{
            if(ContactID == null && 
            ApexPages.currentPage().getParameters().get('contactId') != null){
                ContactID = ApexPages.currentPage().getParameters().get('contactId');
            }
            return ContactID;
        }
        set;
    }

    public String ContactName {
        get{
            return [SELECT Name FROM Contact WHERE ID = :ContactID LIMIT 1].Name;
        }
        set;
    }
}
```

#### ReceiptAttachment.component

```text
<apex:component controller="ReceiptAttachmentController" access="global">
    <apex:attribute name="contactId"

        description="Contact Id"

        assignTo="{!contactObjectId}"

        type="Id" />

    <apex:outputText value="{!PageContents}" escape="false" />
</apex:component>
```

#### ReceiptAttachmentController.class

```java
global class ReceiptAttachmentController {

  global String PageContents{ get; set; }
  global String contactObjectId{ 
    get; 
    set {
        UpdateContents(value);
    } 
  }

  public void UpdateContents(String contactObjectID) {
    try {
        PageReference pageRef = Page.Receipt;
        pageRef.getParameters().put('contactId', contactObjectID);
        PageContents = pageRef.getContent().toString().replace('<html style="display:none !important;">', '<html>');
    } catch(exception ex) { 
        PageContents = 'An error has occurred while trying to generate this invoice.  Please contact customer service.' + 
                       '\n\nError Message:' + ex.getMessage();
    }
  }
}
```

#### Visualforce Email Template

```text
<messaging:emailTemplate subject="Receipt Attached" recipientType="Contact" 
 relatedToType="Contact" replyTo="your@company.com">
    <messaging:attachment renderAs="PDF" filename="receipt.pdf">
        <c:ReceiptAttachment contactId="{!relatedTo.Id}"/>
    </messaging:attachment>
    <messaging:htmlEmailBody >
    <html>
        Please find your invoice attached.
    </html>
    </messaging:plainTextEmailBody>
</messaging:emailTemplate>
```

Now you will see "Visualforce Attachments" in the Email Template, File Name: receipt.pdf