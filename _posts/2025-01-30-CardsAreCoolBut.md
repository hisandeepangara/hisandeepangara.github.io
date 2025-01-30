---
layout: post
title: Cards Are Cool, But Some People Just Wanna Type
description: Handle interactions that bypass Adaptive Card inputs in Copilot Studio
date: 30-01-2025
categories: [Copilot Studio, Adaptive Cards]
tags: [copilotstudio]
---
![alt text](/assets/image.png)

## Challenge
Makers and developers use Adaptive Cards to provide users with an interactive UI in Copilot Studio. While messages with Adaptive Cards work fine, issues arise when using them to ask questions. When the **Ask with Adaptive Card** node is used to collect input, the card naturally waits for the user to respond. However, users might not realize that the card is awaiting their input. As a result, some may start entering other prompts, trying to shift the conversation to a different topic or action within Copilot Studio. This can lead to failures if certain settings are not enabled and necessary tweaks are not made.


Here are the necessary settings and tweaks to ensure a smooth transition between a topic using Adaptive Cards for input and other topics in Copilot Studio

### Allow switching to another topic
1. Select the adaptive card node that asks questions / expect user to respond.

2. Click three dots and click on properties
3. Enable the **"Allow switching to another topic"** setting
4. In the **Only Selected Topics** dropdown, ideally select all the topics, else select only those topics that you feel users can be swithced to skipping the adaptive card.
5. For the setting **How many reprmpts** setting, it is recommended to have 'Dont repeat'

![alt text](/assets/SwitchTopics.png)

> The above settings will allow users to switch to any other selected topic, provided that the trigger phrase or intent of that topic is correctly recognized.

### End conversation

Now, users can successfully switch to another topic that the copilot recognizes based on intent. For example, if the user is in the Greeting topic, where an adaptive card is waiting for input, but they suddenly decide to provide feedback by entering a prompt like "Feedback" or "I want to provide Feedback," the system will redirect them to the Feedback topic, as long as it’s configured as mentioned in the settings above. However, when the feedback topic reaches its last node, it may prompt the same adaptive card where the user left off, which could create a poor experience. To avoid this, ensure that the **End Conversation** node is used appropriately in these topics to properly conclude the conversation.

![alt text](/assets/EndConvo.png)

### What if there's no matching topic and you would need conversation boosting here?

You might think we've already selected all the topics in the Adaptive Card properties, but the system topics are not included. Only custom topics can be selected in that setting. When the agent fails to match a custom topic for the user's prompt or query and there's no option to switch to conversation boosting, it triggers the **"Escalate"** topic.

We can customize the "Escalate" topic by adding a "Generative Answers" node and selecting classic data or knowledge sources to respond to the user's query. To do this, follow the steps below:

1. Head to "Escalate" topic

2. Add an action Advanced > Generative answers
3. Provide the input "Activity.Text" from System variables
4. In the properties of this node, select either of Classic data or Knowledge sources you wish to answer the user's question. 

![alt text](/assets/Escalate.png)

![alt text](/assets/GAProps.png)

---
*Sandeep Angara*  
*Jan 30, 2025*