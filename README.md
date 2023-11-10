[![LNRS](https://risk.lexisnexis.com/Areas/LNRS/img/logo.png)](https://risk.lexisnexis.com/products/one-time-password)
# LexisNexis Identity Proofing One-Time Passcode (OTP) Nodes
---
LexisNexis One Time Password is an out-of-band authentication method that provides business and government organizations the ability to have stronger authentication during a high risk, high value transaction with a customer. It offers a time-sensitive, unique random passcode via SMS text, email or phone and is ideal for companies that are interested in providing a multi-factor authentication solution for their customers. No hardware (electronic fob, etc.) other than the user's existing phone or personal computer is required. 

## Installation
LexisNexis One-Time Passcode (OTP) Nodes are packaged as a jar file using the maven build toolset. To deploy the jar file for the nodes, perform the following:
- Download the jar from the releases tab on github [here](https://github.com/ForgeRock/lexisnexisrs-identity-proofing-docs/releases/latest). 
- Stop the web container to deploy the jar file
- Copy the jar into the `../web-container/webapps/openam/WEB-INF/lib` directory where AM is deployed
- Restart the web container to pick up the new nodes
- Once restart is complete, the nodes will then appear in the authentication trees components palette.

## Backwards Compatibility
LexisNexis One-Time Passcode (OTP) Nodes have been tested to be backwards compatible with ForgeRock AM v7.3.0, as well as 
available on the ForgeRock Identity Cloud. ForgeRock AM versions prior to version 7.3.0 are not supported due to changes in the ForgeRock core API.

## Quick Start Guide
In order to get started with the LexisNexis One-Time Passcode (OTP) Nodes, we have prepared a Quick Start Guides:
- Click [here](./docs/FGRK-LNRS-OTP-Nodes-Getting-Started-Guide-Cloud.pdf) to download a copy of the quick start guide for the ForgeRock Identity Cloud. 
- Click [here](./docs/FGRK-LNRS-OTP-Nodes-Getting-Started-Guide-OpenAM.pdf) to download a copy of the quick start guide for the ForgeRock Access Manager.

## Release Notes
To get the latest version of the LexisNexis One-Time Passcode (OTP) Nodes release notes, click [here](./docs/FGRK-LNRS-OTP-Nodes-Release-Notes.pdf) 


# Node Overview
---
LexisNexis One-Time Passcode (OTP) Nodes provide the following:
- LexisNexis OTP Sender
- LexisNexis OTP Collector
- LexisNexis OTP Decision

## LexisNexis OTP Sender
This node will display a list of selections to the user for available methods to send an OTP code. LexisNexis OTP Nodes provide for email/OTP, SMS/OTP and voice/OTP. The methods displayed depend upon two factors, mainly whether the capability is activated in the node and secondarily if the attribute is available from the user directory for the specific user identity.  

The LexisNexis OTP Sender does assume that the username is contained in shared state from a previous node in the authentication tree/journey.  Specifically the attribute username as provided by ForgeRock Nodes (e.g. Username Collector). At runtime, the username will be used to query the user directory to fetch the attributes as configured in the node.

The LexisNexis OTP Sender Node has the following configuration parameters:
* **Org ID** - Org ID is the unique id associated your organization on the Dynamic Decision Platform (DDP).
* **API Key** - This is the unique API key generated via DDP Portal associated to the Org ID.
* **OTP URL** - This is the URL for the DDP Authentication Hub API endpoint. The default URL is the Worldwide endpoint. This should be modified for specific regions such as EU, US or India.
* **Policy** - The DDP Portal policy to be used to integate the DDP Authentication Hub with OTP
* **OTP Length** - Length of the OTP
* **OTP Expire** - Expiration time in minutes for the OTP
* **Send Email** - Configuration toggle to enable/disable the method of delivery for Email/OTP
* **Email Title** - When Email/OTP is triggered, this will be the title of the email sent to the user
* **Email Message** -When Email/OTP is triggered, this will be the message body of the email sent to the user
* **Email Attribute** - When Email/OTP is enabled, the attribute defined in this configuration parameter will be inspected in the user directory to pull the email address for sending OTP code at runtime. If the value is present, then the method will be displayed to the user, otherwise the method will not be displayed.
* **Send SMS Text** - Configuration toggle to enable/disable the method of delivery for SMS/OTP
* **SMS Message** - When SMS/OTP is triggered, this will be the message body of the SMS text message sent to the user
* **SMS Attribute** - When SMS/OTP is enabled, the attribute defined in this configuration parameter will be inspected in the user directory to pull the mobile phone number for sending OTP code at runtime. If the value is present, then the method will be displayed to the user, otherwise the method will not be displayed.
* **Send Voice** - Configuration toggle to enable/disable the method of delivery for Voice/OTP
* **Voice Attribute** - When Voice/OTP is enabled, the attribute defined in this configuration parameter will be inspected in the user directory to pull the user's phone number to call and convery the OTP code via automated voice. If the value is present, then the method will be displayed to the user, otherwise the method will not be displayed.

The LexisNexis OTP Sender Node has the following outcomes:
* **Success** - This outcome is triggered when the OTP code is successfully generated for the user. It is worth mention that generation does not guarantee delivery to the users device. Thus, the LexisNexis OTP Collector Node allows for retry in the event the user never receives the OTP code.
* **None Available** - This outcome is triggered when no OTP delivery methods are available for a user at runtime. This would mean that the attributes required at runtime are not configured for the user and would need to be provisioned.
* **API Error** - This outcome is triggered when there is an issue with the API Request such as a network timeout or the service is unavailable.
* **OTP Fail** - This outcome is triggered when the API Request is rejected by the LexisNexis DDP Authentication Hub service. Within the debug logging for the node, the actual error codes will be written for offline analysis and triage of the issue.
* **Error** - This outcome is triggered when there is a fundamental integration error, or a new bug is discovered. First attempt to fix the integration error by looking at debug log files for the node to determine if the integration error is due to configuration. If the configuration looks accurate, then open a support case with LexisNexis.


## LexisNexis OTP Collector
This node collects the OTP code sent to the user. The interface allows for submitting a OTP code for decision and validation, as well as the user can request the OTP code to be resent.

The LexisNexis OTP Collector Node has the following configuration parameters:
* **Message Body** - This is the message displayed to the user on the collector interface.  The variable ${otpDestination} will contain either a email address or phone number depending on the method selected by the user via the Lexis OTP Sender.
* **Help Text** - This is the help text displayed in the OTP text entry box for the user.
* **OTP Error Message** - Message displayed if user enters an incorrect OTP code
* **OTP Blank Message** - Message displayed if user attempts to submit a blank OTP code

The LexisNexis OTP Collector Node has the following outcomes:
* **Submit** - This outcome is triggered when the user selects the "Submit" button. When selected, this should then link to the LexisNexis OTP Decision node to validate the OTP Code being submitted. IF the LexisNexis OTP Decision node detects a blank or invalid OTP code, then this node will be linked and the appropriate message will be displayed. The outcomes review and challenge from the LexisNexis OTP Decision are to link back to the LexisNexis OTP Collector.
* **Retry** - This outcome is triggered when the user wants to get a new OTP code by selecting the "Retry" button on the interface.
* **Error** - This outcome is triggered when there is a fundamental integration error, or a new bug is discovered. First attempt to fix the integration error by looking at debug log files for the node to determine if the integration error is due to configuration. If the configuration looks accurate, then open a support case with LexisNexis.


## LexisNexis OTP Decision
This node verifies OTP codes entered by the user. In a typical authentication tree, the LexisNexis OTP Collector will precede this node.  The collector places the user entered and submitted OTP in shared state. Additionally, the LexisNexis OTP Sender will precede this node and the LExisNexis Collector Node that places the characteristics of the type of OTP into shared state.

The LexisNexis OTP Decision Node has the following configuration parameters:
* **Org ID** - Org ID is the unique id associated your organization on the Dynamic Decision Platform (DDP).
* **API Key** - This is the unique API key generated via DDP Portal associated to the Org ID.

The LexisNexis OTP Decision Node has the following outcomes:
* **Pass** - This outcome is triggered when the OTP code submitted by the user is validated so that MFA via OTP has passed.
* **Challenge** - This outcome is triggered when the OTP code fails to validate, and the number of retires has not been violated.
* **Review** - This outcome is triggered when the OTP code fails to validate due to blank OTP code entered.
* **Reject** - This outcome is triggered when OTP code validation is failed.
* **Error** - This outcome is triggered when there is a fundamental integration error, or a new bug is discovered. First attempt to fix the integration error by looking at debug log files for the node to determine if the integration error is due to configuration. If the configuration looks accurate, then open a support case with LexisNexis.


# Configuring LexisNexis One-Time Passcode (OTP) Nodes
---
## Example Journey/Tree
The example depicted here is showing how to configure LexisNexis OTP Nodes. LexisNexis OTP Nodes are used for Identity proofing as well as a multi-factor authentication (MFA) of a user. The workflow starts with the LexisNexis OTP Sender node that has a dependency upon the attribute “username” being in shared state from another authentication tree workflow. The orchestration allows the user to define the method of OTP delivery, generate the OTP code, enter the OTP code and submit for validation.

The flow is as follows:
- Page Node to display the OTP Method selection interface using the LexisNexis OTP Sender Node
- Page Node to display the OTP Collection interface using the LexisNexis OTP Collector Node
- LexisNexis OTP Decision Node to determine if the OTP collected from the user is valid
- Page Nodes with messages and a single OK button that will display the results and/or error conditions
 
![OTP_JOURNEY](./images/lnrs-otp-tree.png)


## Example Journey/Tree
One-Time Passcode (OTP) technology is typically used to augment an orchestration for Identity Proofing or user MFA.  The example journey/tree above provides an example of the OTP workflow, which is an orchestrated workflow where the LexisNexis OTP Sender node has a dependency upon the attribute “username” being in shared state.

A simple framework to exercise the authentication tree is to configure a page node with a username collector which will fulfill the dependency to have the attribute “username” being in shared state. Then an Inner Tree Evaluator Node can integrate the OTP journey/tree for a final outcome of authentication.
 
![AUTH_OTP_JOURNEY](./images/lnrs-auth-otp-tree.png)
