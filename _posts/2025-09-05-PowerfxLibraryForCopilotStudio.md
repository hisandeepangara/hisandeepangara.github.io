---
layout: post
title: Power fx Library for Copilot Studio
description: This blog curates practical scenarios where Power Fx expressions play a key role in Copilot Studio—demonstrating how they enable smarter logic, data handling, and automation within conversations and integrations. Over time, this library will grow with additional use cases, serving as a reference for practical applications
date: 05-09-2025
categories: [Copilot Studio, Power fx]
tags: [copilotstudio, powerfx]
---
![alt text](/assets/fxlibrary.jpg)

## Use Case #1: Create & Pass Records From Copilot Studio to Flows

### Problem:
![alt text](/assets/fx2.png)

When calling a Power Automae flow (or Agent flow) from a topic in Copilot Studio, we will pass input variables of different data types that a Power Automate supports; which are text, file, email, number, date and boolean. When we try to pass a record data type which is a collection of key-value pairs, or a table data type which is a collection of records, the above error pops up. To solve this problem, we can use Powerfx functions Text() and JSON(). Let's also look at how we create record and table structures and how to pass these to flows

### Creating a record
An empty record data type can be created as simple as using the curly braces. 

`{}`  - Creates an empty record

Below is an example of a record data type. Variable and string literals can be used for values. If using string literals enclose them with double quotes. Here our variable name is "userRecord". This variable in Powerfx expressions can be referenced using Topic.userRecord
```
{
    userName: System.User.DisplayName,
    userEmail: System.User.Email,
    language: System.User.Language
}
```
![alt text](/assets/fx1.png)

### Pass a record type to Flow
Following expression can be used to pass a record data type to a flow inputs

` Text(JSON(Topic.userRecord, JSONFormat.Compact)) `

This will convert a JSON record to JSON string, which a power automate flow can accept as a input parameter.

![alt text](/assets/fx3.png)


### Retrieving Records in Flow
To retrieve the passed record from copilot studio in a power automate flow, pass the input JSON string to a Parse JSON and generate the schema from a sample. Doing this, you will be able to retirve the record values as below:

![alt text](/assets/fx4.png)

The same formula applies for table structures, the only difference when creating a table structure is to enclose collection of records using "[ ]". Example below:
```
[
    {
        userName: "",
        userEmail: "",
        language: ""
    },
    {
        userName: "",
        userEmail: "",
        language: ""
    }
]
```

---
*Sandeep Angara*  
*August 23, 2025*
