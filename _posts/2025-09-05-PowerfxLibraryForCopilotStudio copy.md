---
layout: post
title: Power fx Library for Copilot Studio
description: This blog curates practical scenarios where Power Fx expressions play a key role in Copilot Studio—demonstrating how they enable smarter logic, data handling, and automation within conversations and integrations. Over time, this library will grow with additional use cases, serving as a reference for practical applications
date: 05-09-2025
categories: [Copilot Studio, Power fx]
tags: [copilotstudio, powerfx]
---
![alt text](/assets/fxlibrary.jpg)


## Use Case #2: Capturing References from Free Text

### Problem: 
When driving conversations through a topic in Copilot Studio, users often include important references or key pieces of information directly within their messages. Capturing these seamlessly from free-text input is challenging, as the agent needs a way to detect and extract them in real time

![alt text](/assets/fx5.png)


### Forced Input Approach:
In Copilot Studio, entities are utilized to identify specific pieces of information within user input. For instance, predefined entities can recognize names, dates, or locations, while custom entities, including regex-based ones, can be defined to capture specific patterns. This process often involves configuring question nodes to prompt users for specific information, which can interrupt the natural flow of conversation.

For a more detailed understanding of how entities and slot filling work in Copilot Studio, you can refer to the official documentation: https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-entities-slot-filling

However, this method may not align with the goal of creating a seamless user experience, as it requires setting up a question node and then recognizing the entity from the user’s response—while effective in structured scenarios, it interrupts the natural flow of conversation.

### Solution:
We can use Power Fx functions Match() and IsMatch() to detect and extract important references directly from free-text messages in real time.

- IsMatch() allows you to check whether the user input contains a specific pattern, returning true or false.

- Match() extracts the actual substring that matches the pattern, which can then be stored in a topic variable or passed to a flow for further processing.

This approach works dynamically, capturing relevant information whenever it appears in the conversation—without interrupting the flow or requiring explicit prompts. Multiple formats or patterns can be handled in a single regex expression, making it flexible and easy to maintain.

### Steps to implement this approach:
1. Create a topic with "A message is received" trigger.
2. Add a condition node and click 3 dots and switch to formula by selecting "Change to formula". This step checks whether there's a matching identifier of our interest in the user prompt.
![alt text](/assets/fx7.png)
3. Enter the below PowerFx and replace with required regex pattern. Here this regex defines anystring that starts with "SKU-" and followed by any number of digits. Also the third paramter in this function says it should ignore the case, so that it matches even when user types with lower case like: "sku-7789"

    ```
    IsMatch(System.Activity.Text, "SKU-\d+", MatchOptions.IgnoreCase)
    ```
    Note: System.Activity.Text is a system variable that holds the current message or prompt from the user

4. Create a new node "Set a variable". Switch to forumla in the "To Value" input box and enter the following Power Fx function. This expression extracts the matched sub-string of our interest.
    ```
    Match(System.Activity.Text, "SKU-\d+", MatchOptions.IgnoreCase).FullMatch

    ```
The topic setup is as follows

![alt text](/assets/fx6.png)


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

```
Text(JSON(Topic.userRecord, JSONFormat.Compact))
```

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
*September 20, 2025*
