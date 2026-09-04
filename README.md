[![LNRS](https://risk.lexisnexis.com/Areas/LNRS/img/logo.png)](https://risk.lexisnexis.com/products/one-time-password)
# LexisNexis Identity Proofing One-Time Passcode (OTP) Nodes
---
LexisNexis One Time Password is an out-of-band authentication method that provides business and government organizations the ability to have stronger authentication during a high risk, high value transaction with a customer. It offers a time-sensitive, unique random passcode via SMS text, email or phone and is ideal for companies that are interested in providing a multi-factor authentication solution for their customers. No hardware (electronic fob, etc.) other than the user's existing phone or personal computer is required. 

## Installation
For the on-premise PingAM / ForgeRock, LexisNexis One-Time Passcode (OTP) Nodes are packaged as a jar file that is to be installed within the web server. To deploy the jar file, perform the following:
- Download the jar from the releases tab on github [here](https://github.com/ForgeRock/lexisnexisrs-identity-proofing-docs/releases/latest). 
- Stop the web container to deploy the jar file
- Copy the jar into the `../web-container/webapps/openam/WEB-INF/lib` directory where PingAM / ForgeRock is deployed
- Restart the web container to pick up the new nodes
- Once restart is complete, the nodes will then appear in the authentication trees components palette.

## Compatibility

<table>
  <colgroup>
    <col>
    <col>
  </colgroup>
  <thead>
  <tr>
    <th>Product</th>
    <th>Compatible?</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td><p>Ping Advanced Identity Cloud (PingAIC)</p></td>
    <td><p><span>Yes</span></p></td>
  </tr>
  <tr>
    <td><p>Ping Access Management (PingAM) (self-managed)</p></td>
    <td><p><span>Yes</span></p></td>
  </tr>
  </tbody>
</table>

## Backwards Compatibility
LexisNexis One-Time Passcode (OTP) Nodes have been tested with PingAM / ForgeRock v8.0, with backwards compatibility to v7.3, v7.4 and v7.5. Due to changes in the APIs, the LexisNexis OTP Nodes are not compatible with versions prior to v7.3.

## Quick Start Guide
In order to get started with the LexisNexis One-Time Passcode (OTP) Nodes, we have prepared Quick Start Guides:
- Click [here](./docs/LNRS-OTP-Nodes-Getting-Started-Guide-PingAIC.pdf) to download a copy of the quick start guide for PingOne AIC / ForgeRock. 
- Click [here](./docs/LNRS-OTP-Nodes-Getting-Started-Guide-PingAM.pdf) to download a copy of the quick start guide for PingAM / ForgeRock.

## Release Notes
To get the latest version of the LexisNexis One-Time Passcode (OTP) Nodes release notes, click [here](./docs/LNRS-OTP-Nodes-Release-Notes.pdf) 


# Node Overview
---
LexisNexis One-Time Passcode (OTP) Nodes provide the following:
- LexisNexis OTP Selector
- LexisNexis OTP Send
- LexisNexis OTP Collector
- LexisNexis OTP Decision

## LexisNexis OTP Selector
This node will display a list of selections to the user for available methods to send an OTP code. LexisNexis OTP Nodes provide for email/OTP, SMS/OTP and voice/OTP. Upon user selection, the node will place the selected value into shared state variable <code>otp.type_selected</code>. By default, this is the shared state variable that the LexisNexis OTP Send node accepts to determine which type of OTP to send to the end user. If only a single OTP type is configured, the node just sends the <code>otp.type_selected</code> without any user interaction.

This node is optional within a authentication tree/journey. The node is intended as a helper for an orchestration to retrieve the end user desired OTP type. The LexisNexis OTP Send node just requires the field <code>otp.type_selected</code> which can be placed into shared state by any other node in the authentication tree/journey.

### Input
The LexisNexis OTP Selector node does not have any required/optional input attributes.

### Configuration
The LexisNexis OTP Selector node has the following configuration parameters:
* **Send Email** - When selected, this will display an Email Button on the selector interface.
* **Send SMS** - When selected, this will display an SMS Button on the selector interface.
* **Send Voice** - When selected, this will display an Voice Button on the selector interface.

### Output
The LexisNexis OTP Selector node has the following outputs placed into shared state: 
* **OTP Type Selected** - The user selected OTP type will be contained in the state variable `otp.type_selected`. This String value is further processed by the LexisNexis OTP Send node.
* **Failure Reason** - If an error outcome is generated, the state variable `otp.failure_reason` will contain text associated with the error condition.

### Callbacks
The LexisNexis OTP Selector node has the following callbacks:
* When more than one OTP Type is configured, the following callbacks for the user interface are returned:
  * `ConfirmationCallback` - Contains a button for each active configured OTP Type. The label in the button is based on the configured String value.
  
### Outcomes 
The LexisNexis OTP Selector node has the following outcomes:
* **Success** - This outcome is triggered when a valid OTP Type is selected by the user.
* **Error** - This outcome is triggered when there is a fundamental integration error. First attempt to fix the integration error by looking at debug log files for the node to determine if the integration error is due to configuration. If the configuration looks accurate, then open a support case with LexisNexis Risk Solutions.


## LexisNexis OTP Sender
This node will send an API request to the LexisNexis Dynamic Decision Platform (DDP) authentication hub that sends an OTP code to the end user. There is no interface displayed to the user by this node. The OTP Type selected attribute in shared state variable <code>otp.type_selected</code> defines the delivery method, along with the attribute configuration to locate the destination (e.g. user email address or phone number). The Attribute Source defines where the attribute mapping information will be fetched at runtime, mainly either User Directory or Shared State. When the user directory is configured as the source for attributes, the node will assume that the username is contained in shared state from a previous node in the authentication tree/journey and use that username to query the user directory for the user's destination. When the shared state is configured as the source for attributes, the node will inspect shared state for the user's destination.

### Input
The LexisNexis OTP Send node retrieves the following from the journey shared state based upon the configuration of the `Attribute Source` parameter.
* **OTP Type Selected** - Required in shared state variable `otp.type_selected` to define the OTP delivery method. The LexisNexis OTP Selector node can be included in a authentication tree/journey to place this state variable into shared state, or it can be written by another node.
* **AM Username (`username`)** - Required in shared state, or `objectAttributes`, to resolve the desired AM identity when the `Attribute Source = User Directory`. The AM identity user record is queried for the OTP destination (e.g. user email address or phone number).
* **`objectAttributes`** - Required when the `Attribute Source = Shared State`. Shared state is queried for the OTP destination (e.g. user email address or phone number). The attribute to query is based on the configuration parameter **Email Attribute**, **SMS Attribute**, or **Voice Attribute**.

### Configuration
The LexisNexis OTP Send node has the following configuration parameters:
* **Org ID** - Org ID is the unique id associated your organization on the Dynamic Decision Platform (DDP).
* **API Key** - This is the unique API key generated via DDP Portal associated to the Org ID.
* **Base URL** - Defines the domain URL for the DDP/TMX region where API Requests are to be sent.  The default value is the global region.
* **Policy** - The DDP Portal policy to be used to integrate the DDP Authentication Hub with OTP.
* **OTP Length** - Length of the OTP. Valid values are between 6 and 10 characters.
* **OTP Expire** - Expiration time in minutes for the OTP. Valid values are between 1 and 60 minutes.
* **Email Title** - When Email/OTP is triggered, this will be the title of the email sent to the user.
* **Email Message** -When Email/OTP is triggered, this will be the message body of the email sent to the user.
* **SMS Message** - When SMS/OTP is triggered, this will be the message body of the SMS text message sent to the user. The SMS message has a maximum length of 160 characters.
* **Attribute Source** - Defines where the attributes for sending the OTP code is fetched at runtime. This is a dropdown list that contains the options User Directory and Shared State. User Directory will look for attribute in the Identity Store, and Shared State looks in the shared memory.
* **Email Attribute** - When Email/OTP is defined by the OTP Type, the attribute defined in this configuration parameter will be fetched by the name of the attribute provided based on the Attribute Source defined.
* **SMS Attribute** - When SMS/OTP is defined by the OTP Type, the attribute defined in this configuration parameter will be fetched by the name of the attribute provided based on the Attribute Source defined.
* **Voice Attribute** - When Voice/OTP is defined by the OTP Type, the attribute defined in this configuration parameter will be fetched by the name of the attribute provided based on the Attribute Source defined.

### Output
The LexisNexis OTP Send node has the following outputs placed into shared state: 
* **OTP API Request ID** - The Request ID (e.g. attribute <code>request_id</code>) associated to the DDP Authentication Hub API Response for OTP will be placed into state variable `otp.request_id`. This String value is used by the LexisNexis OTP Decision node to invoke an API Request with OTP passcode entered by the user for validation.
* **OTP Sent To** - Contains the user's email address or phone number based on OTP delivery method. The information is stored in state variable `otp.sent_to` and displayed by the LexisNexis OTP Collector node as part of the user interface.
* **OTP Policy** - Contains the DDP Policy that invoked the OTP Auth Hub. The state variable `otp.policy` places the policy name into shared state for the LexisNexis OTP Decision node to consume, which is a requirement for the same policy to be used.
* **OTP URL** - Contains the DDP URL that was configured in this node. The state variable `otp.url` is used by the LexisNexis OTP Decision node to send the API request for validation.
* **Failure Reason** - If an error outcome is generated, the state variable `otp.failure_reason` will contain text associated with the error condition.

### Callbacks
The LexisNexis OTP Send node does not have any callbacks as there is no user interface displayed.

### Outcomes
The LexisNexis OTP Send Node has the following outcomes:
* **Success** - This outcome is triggered when the OTP code is successfully generated for the user. It is worth mention that generation does not guarantee delivery to the users device. Thus, the LexisNexis OTP Collector Node allows for retry in the event the user never receives the OTP code.
* **API Error** - This outcome is triggered when there is an issue with the API Request such as a network timeout or the service is unavailable.
* **OTP Fail** - This outcome is triggered when the API Request is rejected by the LexisNexis DDP Authentication Hub service. Within the debug logging for the node, the actual error codes will be written for offline analysis and triage of the issue.
* **Error** - This outcome is triggered when there is a fundamental integration error. First attempt to fix the integration error by looking at debug log files for the node to determine if the integration error is due to configuration. If the configuration looks accurate, then open a support case with LexisNexis Risk Solutions.


## LexisNexis OTP Collector
This node collects the OTP code sent to the user. The interface enables the user to submit an OTP code for decision and validation, as well as the user can request the OTP code to be resent.

### Input
The LexisNexis OTP Collector node retrieves the following from the journey shared state:
* **OTP Type Selected** - Required in shared state variable `otp.type_selected` to define the OTP delivery method. This nodes uses the value to customize the user interface message prompting for OTP Passcode entry.
* **OTP Sent To** - Contains the user's email address or phone number based on OTP delivery method. The information is stored in state variable `otp.sent_to` and displayed by the LexisNexis OTP Collector node as part of the user interface.
* **OTP Error** - State variable `otp.error` contains a code to define whether or not a error message should be displayed to the user. Values include False, Blank and True.

### Configuration
The LexisNexis OTP Collector Node has the following configuration parameters:
* **Message Body** - This is the message displayed to the user on the collector interface.  The variable ${otpDestination} will contain either an email address or phone number depending on the method selected by the user via the Lexis OTP Sender.
* **Help Text** - This is the help text displayed in the OTP text entry box for the user.
* **OTP Error Message** - Message displayed if user enters an incorrect OTP code
* **OTP Blank Message** - Message displayed if user attempts to submit a blank OTP code

### Output
The LexisNexis OTP Collector node has the following outputs placed into shared state: 
* **OTP Passcode** - Contains the user submitted OTP passcode for validation. The associated String is placed into state variable `otp.passcode_entered`. This value is further processed by the LexisNexis OTP Decision node.
* **Failure Reason** - If an error outcome is generated, the state variable `otp.failure_reason` will contain text associated with the error condition.

### Callbacks
The LexisNexis OTP Collector node has the following callbacks:
  * `TextOutputCallback` - When an error occurs and needs to be displayed to the user, this first `TextOutputCallback` container will contain the error message to display to the user. The container will be marked as `TextOutputCallback.ERROR`. The following state variable `otp.error` values indicate that the object should be created:
    * Blank - Fills the text based on the **OTP Blank Message**
    * True - Fills the text based on the **OTP Error Message**
  * `TextOutputCallback` - Contains the configured **Message Body**
  * `NameCallback` - Container to collect the user entered OTP Passcode. The configured **Help Text** is displayed in the text entry box.
  * `ConfirmationCallback` - Contains buttons for the user interface, mainly Submit and Retry buttons.

### Outcomes
The LexisNexis OTP Collector Node has the following outcomes:
* **Submit** - This outcome is triggered when the user selects the “Submit” button. When selected, this should then link to the LexisNexis OTP Decision node to validate the OTP Code being submitted. If the LexisNexis OTP Decision node detects a blank or invalid OTP code, then this node will be linked from the decision node to re-prompt the user for OTP code entry, as well as display the appropriate error message. The outcomes review and challenge from the LexisNexis OTP Decision are to link back to the LexisNexis OTP Collector.
* **Retry** - This outcome is triggered when the user wants to get a new OTP code by selecting the "Retry" button on the interface. This outcome should route back to the beginning of the OTP user workflow.
* **Error** - This outcome is triggered when there is a fundamental integration error. First attempt to fix the integration error by looking at debug log files for the node to determine if the integration error is due to configuration. If the configuration looks accurate, then open a support case with LexisNexis Risk Solutions.


## LexisNexis OTP Decision
This node verifies OTP codes entered by the user. In a typical authentication tree, the LexisNexis OTP Collector will precede this node.  The collector places the user entered and submitted OTP in shared state. Additionally, the LexisNexis OTP Sender will precede this node and the LexisNexis Collector Node that places the characteristics of the type of OTP into shared state.

### Input
The LexisNexis OTP Decision node retrieves the following from the journey shared state:
* **OTP Passcode** - Contains the user submitted OTP passcode for validation. The associated String is placed into state variable `otp.passcode_entered`.
* **OTP API Request ID** - The Request ID (e.g. attribute <code>request_id</code>) associated to the DDP Authentication Hub API Response for OTP is fetched from state variable `otp.request_id`. This String value is used by the LexisNexis OTP Decision node to invoke an API Request with OTP passcode entered by the user for validation.
* **OTP Policy** - Contains the DDP Policy that invoked the OTP Auth Hub. The state variable `otp.policy` is used to invoke the API Request to DDP Auth Hub.
* **OTP URL** - State variable `otp.url` is used by the LexisNexis OTP Decision node to send the API request for validation.

### Configuration
The LexisNexis OTP Decision Node has the following configuration parameters:
* **Org ID** - Org ID is the unique id associated your organization on the Dynamic Decision Platform (DDP).
* **API Key** - This is the unique API key generated via DDP Portal associated to the Org ID.

### Outputs
The LexisNexis OTP Decision node has the following outputs placed into shared state: 
* **Failure Reason** - If an error outcome is generated, the state variable `otp.failure_reason` will contain text associated with the error condition.

The LexisNexis OTP Decision node cleans up and removes data from shared state upon an outcome of pass, reject or error: 
* **OTP Request ID** - The Request ID associated to the DDP Authentication Hub API Response from the IIDQA Get Quiz node contained state variable `otp.request_id`.
* **OTP Passcode** - Removes state variable `otp.passcode_entered`.
* **OTP Policy** - Removes state variable `otp.policy`.
* **OTP URL** - Removes state variable `otp.url`.
* **OTP Error** - Removes state variable `otp.error`.

### Callbacks
The LexisNexis OTP Decision node does not have any callbacks as there is no user interface displayed.

### Outcomes 
The LexisNexis OTP Decision Node has the following outcomes:
* **Pass** - This outcome is triggered when the OTP code submitted by the user is validated so that MFA via OTP has passed.
* **Challenge** - This outcome is triggered when the OTP code fails to validate, and the number of retires has not been violated.
* **Review** - This outcome is triggered when the OTP code fails to validate due to blank OTP code entered.
* **Reject** - This outcome is triggered when OTP code validation is failed.
* **Error** - This outcome is triggered when there is a fundamental integration error. First attempt to fix the integration error by looking at debug log files for the node to determine if the integration error is due to configuration. If the configuration looks accurate, then open a support case with LexisNexis Risk Solutions.


# Configuring LexisNexis One-Time Passcode (OTP) Nodes
---
## Example Journey/Tree
The example depicted here is showing how to configure LexisNexis OTP Nodes. LexisNexis OTP Nodes are used for Identity proofing as well as a multi-factor authentication (MFA) of a user. This example workflow displays the OTP Selector to the user, sends the OTP code, collects the OTP code value from the user and then validates if the value is correct.

The detailed workflow is as follows:
- Page Node to display the OTP Method selection interface using the LexisNexis OTP Selector node. This configuration wraps the selector with page node so that detailed messages can be displays to the user as part of the interface. Success from the selector will place the variable <code>otp.type_selected</code> into shared state.
- LexisNexis OTP Sender node will generate the OTP code to the end user. This node will inspect shared state for <code>username</code> and <code>otp.type_selected</code>.  This combination of information defines the type of OTP to send to the defined user. The configuration of the node will define how additional attributes for the API request are fulfilled. For example, if the OTP type is email, then the Email Attribute will be fetched from either the User Directory or Shared state based on the configuration of the Attribute Source.
- Page Node to display the OTP Collection interface using the LexisNexis OTP Collector Node. This configuration wraps the collector with page node so that detailed messages can be displays to the user as part of the interface.
- LexisNexis OTP Decision Node to determine if the OTP collected from the user is valid.
- Page Nodes with messages and a single OK button that will display the results and/or error conditions.
 
![OTP_JOURNEY](./images/lnrs-otp-tree.png)


## Example Journey/Tree
One-Time Passcode (OTP) technology is typically used to augment an orchestration for Identity Proofing or user MFA.  The example journey/tree above provides an example of the OTP workflow, which is an orchestrated workflow. 

A simple framework to exercise the authentication tree is to configure a page node with a username collector which will fulfill the dependency to have the attribute “username” being in shared state. Then an Inner Tree Evaluator Node can integrate the OTP journey/tree for a final outcome of authentication.
 
![AUTH_OTP_JOURNEY](./images/lnrs-auth-otp-tree.png)
