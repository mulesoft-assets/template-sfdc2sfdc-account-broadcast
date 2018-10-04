
# Template: Salesforce Org to Org Account Broadcast

Broadcast changes to accounts from one Salesforce Org to another in real time. The detection criteria, and fields to move are configurable. Additional systems can be easily added to be notified of changes. Real time synchronization is achieved via outbound notifications or configurable rapid polling of Salesforce. 

This template leverages watermarking functionality to ensure that only the most recent items are synchronized and batch to efficiently process many records at a time.

![9e14edc4-1619-4401-8f84-21bd020d020e-image.png](https://exchange2-file-upload-service-kprod.s3.us-east-1.amazonaws.com:443/9e14edc4-1619-4401-8f84-21bd020d020e-image.png)

**Note:** Any references in the video to DataMapper have been updated in the template with DataWeave transformations.

[//]: # (![]\(https://www.youtube.com/embed/gAMgMc8Aen8?wmode=transparent\)
[![YouTube Video](http://img.youtube.com/vi/gAMgMc8Aen8/0.jpg)](https://www.youtube.com/watch?v=gAMgMc8Aen8)

# License Agreement

This template is subject to the conditions of the [MuleSoft License Agreement](https://s3.amazonaws.com/templates-examples/AnypointTemplateLicense.pdf).

Review the terms of the license before downloading and using this template. You can use this template for free with the Mule Enterprise Edition, CloudHub, or as a trial in Anypoint Studio.

# Use Case

As a Salesforce administrator I want to synchronize accounts between two Salesforce organizations.

This template serves as a foundation for setting an online sync of accounts from one Salesforce instance to another. Each time there is new account or a change in an existing one, the integration  polls for changes in Salesforce source instance and it's responsible for updating the account on the target org.

Requirements have been set not only to be used as examples, but also to establish a starting point to adapt your integration to your requirements.

As implemented, this template leverages the Mule batch module.

The batch job is divided into Process and On Complete stages.

The integration is triggered by a scheduler defined in the flow that triggers the application, querying newest Salesforce updates or creations matching a filter criteria and executes the batch job.

During the Process stage, each Salesforce account is filtered depending on if it has an existing matching account in the Salesforce Org B.

The last step of the Process stage groups the accounts and creates or updates them in Salesforce Org B.

Finally during the On Complete stage the template logs output statistics data to the console.

# Considerations

To make this template run, there are certain preconditions that must be considered. All of them deal with the preparations in both source and destination systems, that must be made for all to run smoothly. Failing to do so can lead to unexpected behavior of the template.

## Salesforce Considerations

Here's what you need to know about Salesforce to get this template to work.

### FAQ

- Where can I check that the field configuration for my Salesforce instance is the right one? See: [Salesforce: Checking Field Accessibility for a Particular Field](https://help.salesforce.com/HTViewHelpDoc?id=checking_field_accessibility_for_a_particular_field.htm&language=en_US "Salesforce: Checking Field Accessibility for a Particular Field")
- Can I modify the Field Access Settings? How? See: [Salesforce: Modifying Field Access Settings](https://help.salesforce.com/HTViewHelpDoc?id=modifying_field_access_settings.htm&language=en_US "Salesforce: Modifying Field Access Settings")

### As a Data Source

If the user who configured the template for the source system does not have at least _read only_ permissions for the fields that are fetched, then an _InvalidFieldFault_ API fault displays.

```
java.lang.RuntimeException: [InvalidFieldFault [ApiQueryFault 
[ApiFault  exceptionCode='INVALID_FIELD'
exceptionMessage='
Account.Phone, Account.Rating, Account.RecordTypeId, Account.ShippingCity
^
ERROR at Row:1:Column:486
No such column 'RecordTypeId' on entity 'Account'. If you are attempting 
to use a custom field, be sure to append the '__c' after the custom 
field name. Reference your WSDL or the describe call for the appropriate names.'
]
row='1'
column='486'
]
]
```

### As a Data Destination

There are no considerations with using Salesforce as a data destination.

# Run it!

Simple steps to get Salesforce to Salesforce Account Broadcast running.

See below.

## Running On Premises

In this section we detail how to run your template on your computer.

Once your app is all set and started, there is no need to do anything else. The application polls an account to know if there are any newly created or updated objects and synchronizes them.

### Where to Download Anypoint Studio and the Mule Runtime

If you are a newcomer to Mule, here is where to get the tools.

- [Download Anypoint Studio](https://www.mulesoft.com/platform/studio)
- [Download Mule runtime](https://www.mulesoft.com/lp/dl/mule-esb-enterprise)

### Importing a Template into Studio

In Studio, click the Exchange X icon in the upper left of the taskbar, log in with your

Anypoint Platform credentials, search for the template, and click **Open**.

### Running on Studio

After you import your template into Anypoint Studio, follow these steps to run it:

- Locate the properties file `mule.dev.properties`, in src/main/resources.
- Complete all the properties required as per the examples in the "Properties to Configure" section.
- Right click the template project folder.
- Hover your mouse over `Run as`
- Click `Mule Application (configure)`
- Inside the dialog, select Environment and set the variable `mule.env` to the value `dev`
- Click `Run`

### Running on Mule Standalone

Complete all properties in one of the property files, for example in mule.prod.properties and run your app with the corresponding environment variable. To follow the example, this is `mule.env=prod`. 

## Running on CloudHub

While creating your application in CloudHub (or you can do it later as a next step), you need to go to Deployment > Advanced to set all environment variables detailed in "Properties to Configure" as well as in the **mule.env**. 

Once your app is all set and started, there is no need to do anything else. Every time an account is created or modified, it's automatically synchronized to Salesforce Org B as long as it has an email.

### Deploying your Anypoint Template on CloudHub

Studio provides an easy way to deploy your template directly to CloudHub, for the specific steps to do so check this

## Properties to Configure

To use this template, configure properties (credentials, configurations, etc.) in the properties file or in CloudHub from Runtime Manager > Manage Application > Properties. The sections that follow list example values.

### Application Configuration

**Application Configuration**

- http.port `9090` 
- page.size `100` 
- scheduler.frequency `60000`
- scheduler.start.delay `0`
- watermark.default.expression `YESTERDAY`
- trigger.policy `push` | `poll`

**Salesforce Connector Configuration for Company A**

- sfdc.a.username `bob.dylan@orga`
- sfdc.a.password `DylanPassword543`
- sfdc.a.securityToken `avsfwCUl7apQs56Xq2AKi3X`

**Salesforce Connector Configuration for Company B**

- sfdc.b.username `joan.baez@orgb`
- sfdc.b.password `JoanBaez456`
- sfdc.b.securityToken `ces56arl7apQs56XTddf34X`

# API Calls

Salesforce imposes limits on the number of API calls that can be made. Therefore calculating this amount is important. The template calls to the API can be calculated using the formula:

_**1 + X + X / 200**_

_**X**_ is the number of accounts to be synchronized on each run. 

Divide by _**200**_ because by default, accounts are gathered in groups of 200 for each upsert API call in the commit step. Also consider that calls are executed repeatedly every polling cycle.    

For instance if 10 records are fetched from the origin instance, then 12 API calls are made (1 + 10 + 1).

# Customize It!

This brief guide intends to give a high level idea of how this template is built and how you can change it according to your needs.

As Mule applications are based on XML files, this page describes the XML files used with this template.

More files are available such as test classes and Mule application files, but to keep it simple, we focus on these XML files:

- config.xml
- businessLogic.xml
- endpoints.xml
- errorHandling.xml

## config.xml

Configuration for connectors and configuration properties are set in this file. Even change the configuration here, all parameters that can be modified are in properties file, which is the recommended place to make your changes. However if you want to do core changes to the logic, you need to modify this file.

In the Studio visual editor, the properties are on the _Global Element_ tab.

## businessLogic.xml

The functional aspect of the template is implemented in this XML file, directed by a flow that's responsible for Salesforce creations or updates. The message processors constitute four high level actions that fully implement the logic of this template:

1. During the Input stage, the template goes to the Salesforce Org A and queries all the existing accounts that match the filter criteria.
2. During the Process stage, each Salesforce account is filtered depending on if it has an existing matching account in the Salesforce Org B.
3. The last step of the Process stage groups the accounts and creates or updates them in Salesforce Org B.
4. Finally during the On Complete stage, the template logs output statistics data on the console.

## endpoints.xml

This file contains three flows:

1. The **push** flow contains an HTTP endpoint that listens for notifications from Salesforce. Each notification is processed and updates or creates accounts, and then executes the batch job process.
2. The **scheduler** flow contains the Scheduler endpoint that periodically triggers the **sfdcQuery** flow and executes the batch job process.
3. The **sfdcQuery** flow contains watermarking logic that queries Salesforce for updated or created accounts that meet the defined criteria in the query since the last polling. The last invocation timestamp is stored by using Object Store Component and updated after each Salesforce query.

The property **trigger.policy** is the one in charge of defining from which endpoint the template  receives data. The property can only assume one of two values `push` or `poll`. Any other value results in the template ignoring all messages.

## errorHandling.xml

This is the right place to handle how your integration reacts depending on the different exceptions.

This file provides error handling that is referenced by the main flow in the business logic.
