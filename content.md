# Intro to SiriKit

  

This tutorial will walk you through everything you need to know about SiriKit in order to use it in your app. It consists of 3 parts: A brief overview about how SiriKit works, setting up SiriKit in your project, and a breakdown of the official sample code. This is an intermediate tutorial for experienced Swift developers.

### Prerequisites: 

1) An iOS device running iOS 10, and Xcode 8. 

2) Experience using Siri on iOS. 

3) Apple Developer Account.

  

# SiriKit Overview

### 1. What is SiriKit?

SiriKit is the API for integrating Siri in your app, so that people can use your app by speaking. For example, if you have a chat app, you can send messages via your app by telling Siri “Send a message on MyChatApp (or whatever your app name is)”. Besides sending messages, SiriKit also supports sending payments, looking up a photo, selecting a workout, booking a ride, using CarPlay, restaurant reservations and VoIP calling. These are 8 topics, or domains, that SiriKit supports. Inside each domain, there are tasks, or intents, that your app can handle. For example, for the domain of booking a ride, possible intents include getting a list of available rides, booking a ride, and getting the status of the ride you booked. More details about intents can be found [here](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)

  
### 2. How does SiriKit work?

Siri goes through 4 steps to complete a user request: Speech, Intent, Action, Response. Following the chat app example, each step is as following:

Speech: Siri translates user speech audio into text using built-in speech recognition.

Intent: Siri recognizes the domain of the user request (messaging), and the intent within the messaging domain (sending a message) with built in speech analysis. At this step, Siri will pass the information gathered from user to your app.

Confirm: Siri confirms the task with user before performing it, and resolve any possible misunderstandings.

Action: Your app gets confirmation from Siri and performs the task accordingly, then gives the response (with custom UI if needed) to Siri and to user. In this case, you can present the message view right before user sends it.

  

# Setting up your project in Xcode with SiriKit

As we mentioned before, users need to tell Siri which app they use by saying things like “Send a message using xyz app”. Therefore, it’s important to have an app name that’s easy to pronounce and potentially use as a verb for more versatile commands (example: “I will Google the best tacos around me.” “I will Airbnb in Berlin.”) 

### Configuring Provisioning Profile

Create a new single view application, and we will name it “MangoChat” for this tutorial. 

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_0.png)  
  

Now, we need to change the code signing settings in Xcode for SiriKit. First, uncheck “Automatically manage signing”.

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_1.png)  

Then, we need to create a provisioning profile that supports SiriKit. In order to do so, we need to create an App ID that enables SiriKit.
  

Head to apple developer portal (developer.apple.com &gt; Account &gt; Certificates, Identifiers & Profiles &gt; App IDs located on the left pane), and create a new App ID that supports SiriKit: 

Put a descriptive name on App ID name, such as MangoChat with Siri.

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_2.png)  

Select “WildCard App ID”, which lets you use this App ID for multiple apps for convenience. Put an asterisk in Bundle ID to make it generic.

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_3.png)  

In App Services, select SiriKit. 

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_4.png)  

Click continue. You should see something like this on your confirmation page: 

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_5.png)  

Finish the following steps. Now you have an App ID with SiriKit enabled!  

With this App ID, let’s create the provisioning profile.

Go back to the developer portal, and click on ‘All’ from ‘Provisioning Profiles’ on the left pane. Click the plus button on the upper right to create a new profile. Select iOS App Development: 

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_6.png)

On the next page, select the App ID we just created: 

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_7.png)

Then select the certificate you use, as well as the devices you want to use, and give your profile a descriptive name:

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_8.png)

You should now be able to download the profile: 

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_9.png)  

### Setting up Xcode

After downloading, we need to add this profile to Xcode. 

Click the dropdown menu of Provisioning profile and select Import profile. 

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_10.png)

Do the same thing for both Signing (debug) and Signing (release).

Then, click on Capabilities on the top bar and switch on Siri: 

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_11.png)
  

Last but not least, we need to add the communication layer between SiriKit and your app -- the intent extension -- to your app. Select File &gt; New &gt; Target, and select Intents extension.

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_12.png)

Then specify the name of your intent, and select “Include UI Extension”. We will explain how to use these in the next section.

Click “Activate” if you see windows like this:

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_13.png)

Now, you have completed setup for SiriKit for your app!

# How UnicornChat Sample code works

Let’s dive into code.  

Most of the work you will be doing for integrating SiriKit is conforming to relevant SiriKit protocols. If you open your IntentHandler.swift (in the folder of the intent you just created), you will see lots of useful method stubs.

![](https://raw.githubusercontent.com/wustladela/SiriKit-Tutorial/master/assets/image_14.png)

To better understand them, let’s have a look at Apple’s WWDC demo app: [UnicornChat](https://developer.apple.com/library/prerelease/content/samplecode/UnicornChat/UnicornChatExtendingYourAppswithSiriKit.zip)

First, run this app on your device and say commands like “Send a message on UnicornChat”. 

  

Then, let’s look at their code. Open UCIntentsHandler.swift, where func handler gets called immediately after Siri recognizes the domain and intent of the user request.

After listening to the user, Siri creates an intent object (in this case, INSendMessageIntent object) that includes attributes like recipient, content of the message, and optionally, if it requires unlocked device. More details of intent objects and attributes can be found [here](https://developer.apple.com/reference/intents/)

Let’s command-click the class name UCSendMessageIntentHandler() at the function return line to look at how this intent is being handled.  

To decipher each intent object, we conform to the INSendMessageIntentHandling protocol, which made up this entire file. These functions provide enough information for SiriKit to work with your app. Please take your time to read through IntentViewController.swift

Lastly, your app can present an optional UI for the intent. Open SiriUIExtension folder and you can see the view controller. Similarly, IntentViewController.swift conforms to relevant protocols and you job will be to fill out protocol functions.
  
## Summary

SiriKit Siri work with your app so user can use their voice to make requests. After conforming to relevant protocols, SiriKit passes intent objects to your app that performs actions accordingly. As a developer, the only thing you need to worry about is handling intents.
