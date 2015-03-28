---
title: SAIFE Endpoint Library v2.0.0 Documentation

language_tabs:
  - shell
  - java
  - C++

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Overview

Welcome to the SAIFE® Endpoint Library! This document will guide you in integrating the SAIFE platform and features into your applications, using either C++ or Java. The Endpoint Library provide a framework for  ensuring the authenticity of all your endpoints, whether small sensors, smartphones, laptops, or smart refrigerators, as well as the integrity of all your data. Feel free to check out the recently released [SAIFE Management API documentation](http://saifeinc.com/developers/libraries/management/), for creating custom management dashboards, and refer to [saifeinc.com](http://saifeinc.com/), our [developer page](http://saifeinc.com/developers/), or our [support forums](https://saife.zendesk.com/hc/en-us) for a a thorough introduction to the SAIFE system architecture.

SAIFE is a patented security platform that provides trusted routing over untrusted networks for any IP-based endpoint. SAIFE uses a secure, distributed, global network to create secure tunnels between SAIFE-enabled devices. All traffic is signed, bi-directionally authenticated, and encrypted.

The SAIFE Endpoint Library allows you to create your own self-contained public key infrastructure (PKI). Every endpoint and network component is given a unique public certificate, which is validated and signed by [the SAIFE Dashboard](https://dashboard.saifeinc.com/). The Dashboard also allows you to provision endpoints and create secure groups, ensuring that communication only occurs between the specified, trusted endpoints within a secure group. This version of the Endpoint Library provides 128 bit encryption; if 256 bit encrpytion is required, please [contact](mailto:sales@saifeinc.com) our export control team.

This document is divided in the following sections:

* [Getting Started](#Getting Started) - for those of you who want to start compiling and running immediately.
* [API Overview](#API Overview) - a brief description of the key API classes.
* [Anatomy of a SAIFE Application](#Anatomy of a SAIFE Application) - provides an in-depth explanation of the anatomy of a SAIFE application.
* [Release Notes](#Release Notes) - latest updates.
* [Acknowledgements](#Acknowledgements) - thanks, copyright information, and GitHub link.

#Getting Started

##Downloading the Endpoint Source Code
@TODO. Directions to store.saifeinc.com and instructions for downloading the right library & a copy of this documentation.

##Installation

Once the APIs are downloaded, the next step is to make a directory to unpack the SAIFE header files and shared library files. You may also unpack them to a system location such as /usr/local.

mkdir ~/Development/Saife
cd ~/Development/Saife
tar -xzf  libSaife-1.0.0-documentation.tar.gz
tar -xzf  libSaife-1.0.0-linux_x86_64_gnu.tar.g

```shell
mkdir ~/Development/Saife
cd ~/Development/Saife
tar -xzf  libSaife-1.0.0-documentation.tar.gz
tar -xzf  libSaife-1.0.0-linux_x86_64_gnu.tar.gz
```

The following is a sketch of the resulting directory structure:

doc/ .............. Saife C++ Library documentation for offline usage 
examples/ ......... Example Saife enabled applications
    hello_world ...... Very basic Saife enabled application showing usage of Secure Messaging
include/ ............. Saife C++ Library header files
lib/ ................  All the Saife C++ Libraries needed to link to your application


##Developer Account

You will need the following developer information from SAIFE support before you can use your software or the provided examples with the SAIFE network.

SAIFE Dashboard URL (i.e., https://management.tiprnet.net), domain name, user name and password.

##Start Coding

Please refer to these sample applications on github, which showcase the features of the APIs: https://github.com/saifeinc/examples-saife-endpoint-lib

SaifeMessageDemo - A simple secure chat application using SAIFE Endpoint Library.
The Anatomy of a SAIFE Application guide provides an in-depth explanation of the anatomy of a SAIFE application.

###Simple Build Command

Use the following command to build a single file application. Set the environment variable to the location of SAIFE Endpoint Library.

export SAIFE_HOME=~/Development/Saife
g++ -o hello_world hello_world.cc -I$SAIFE_HOME/include -L$SAIFE_HOME/lib/saife -lsaife -lCecCryptoEngine -lroxml


#API Overview

##SaifeInterface Class

Your application will use saife::SaifeInterface to command and control the SAIFE® Endpoint Library. The saife::SaifeInterface is an aggregation of sub-interfaces, each of which encapsulates a specific set of functions. The sub-interfaces and their respective functionality are listed below.

saife::SaifeManagementInterface - this interface contains the methods used to manage the state of the SAIFE Endpoint Library. See States for a description of the various states.
saife::SaifeMessagingInterface - this interface contains the methods used to secure messages between two SAIFE-enabled endpoints.
saife::SaifeContactServiceInterface - this interface contains the methods needed to be able to address other SAIFE-enabled endpoints.

##SaifeFactory Class

SAIFE Endpoint Library uses the factory method design pattern to create instances of saife::SaifeInterface. The saife::SaifeFactory is used to create different flavors of saife::SaifeInterface. Within the saife::SaifeFactory, there are different "Construct" methods available depending on your application.

##States

In order to understand the life-cycle of a SAIFE endpoint, it is necessary to describe the possible values of saife::SaifeManagementState. These states are manipulated via the saife::SaifeManagementInterface.

@TODO: insert image diagram, if possible

* SAIFE_UNINITIALIZED - after constructing the SaifeInterface via the SaifeFactory, the SaifeInterface starts in the Unitialized state. Initializing the SaifeInterface with saife::SaifeManagementInterface::Initialize while in this state will take it to either the Unkeyed or Initialized state, depending on whether the endpoint has a keypair.
* SAIFE_UNKEYED - in this state, the SAIFE endpoint has not yet generated a keypair. Generating a keypair (including formation of a Certificate Signing Request or CSR) using saife::SaifeManagementInterface::GenerateKeys causes a transition to the Initialized state.
* SAIFE_INITIALIZED - a SAIFE endpoint is initialized and ready to used for provisioning, messaging and other services.


#Anatomy of a SAIFE Application
A SAIFE application begins its life cycle by first obtaining an instance of the SaifeInterface via the saife::SaifeFactory::ConstructLocalSaife. This instance begins in the saife::SAIFE_UNINITIALIZED state and follows the following simple state machine.

@TODO: insert that same image diagram again.

##Initialization
The first step after obtaining a saife::SaifeInterface instance is to call saife::SaifeManagementInterface::Initialize. This call moves the state machine from saife::SAIFE_UNINITIALIZED state into either the saife::SAIFE_INITIALIZED or saife::SAIFE_UNKEYED state. The transition to saife::SAIFE_UNKEYED state occurs when the interface is instantiated for the first time and a valid persisted keystore doesn't exist. The transition to saife::SAIFE_INITIALIZED state occurs if a persisted keystore is found and loaded successfully. The call to saife::SaifeManagementInterface::Initialize returns the appropriate state value.

`try {
   state = saife_ptr->Initialize(store, hosts, ports, false);
 } catch (SaifeException& se) {
   std::cerr << "Failed to initialize with error: " << se.error() << std::endl;
   return (1);
 } catch (...) {
   std::cerr << "Failed to initialize library with unexpected error" << std::endl;
   return (1);
 }`

##The UNKEYED state

This saife::SAIFE_UNKEYED state indicates that a keypair does not exist and the applicaiton is not trusted by the SAIFE network. This is the case when the application starts for the first time. It may also occur if the keystore has been removed as a result of wipe or revocation. The code sample below is an example of how to generate a keypair and become trusted by the SAIFE network.

The saife::SaifeManagementInterface::GenerateKeys function generates the keystore and registers and establishes trust with the SAIFE network. It requires several parameters including a password to secure the keypair and credentials for use with SAIFE Dashboard. The state machine will be in saife::SAIFE_INITIALIZED state upon successful completion.

The saife::SaifeManagementCredentials class requires the creation of a SAIFE® developer account. The developer must contact SAIFE support (support@saifeinc.com) to obtain the credentials necessary for instantiating the saife::SaifeManagementCredentials class. SAIFE Management Library uses these credentials to authenticate the endpoint during registration with the SAIFE Dashboard. Upon successful registration with the SAIFE Dashboard the endpoint becomes a trusted entity within the SAIFE network and is capable of performing secure communications.

`const std::string sms_url = "http://your.url.com";
const std::string sms_domain = "your.domain";
const std::string sms_user = "your.admin";
const std::string sms_password = "yourreallylongpassword";
const SaifeManagementCredentials sms_creds(sms_url, sms_domain, sms_user, sms_password);
// The alias chosen here should be unique within the application's namespace. The SAIFE network enforces uniqueness of alias names
const std::string alias("HelloWorldApp");
const DistinguishedName dn(alias);
saife_ptr->GenerateKeys(dn, password, sms_creds, address_list);`

###Addressing

The application can optionally define one or more addresses to include as metadata to other SAIFE applications as part of key generation and registering with the SAIFE network. An address could be a UID of some kind, an URL/URI, a name, or any other metadata that can be represented as a string. The following code snippet highlights the ability of the application to specify its own addressing scheme.

`SaifeAddress app_id;
app_id.set_address("067e6162-3b6f-4ae2-a171-2470b63dff00"); // An example UID. Should really be generated in a way that it is unique for each app instance
app_id.set_address_type(kUidAddressType);
address_list.push_back(app_id);
SaifeAddress app_uri;
app_uri.set_address("urn:oasis:names:specification:docbook:dtd:xml:4.1.2"); // Taken from the example of a URN in RFC 3986
app_uri.set_address_type(kUriAddressType);
address_list.push_back(app_id);`

##Provisioning

After generating the keystore and registering with the SAIFE network, the application can download data from the SAIFE network. This code snippet is only showing one call to saife::SaifeManagementInterface::UpdateSaifeData. However, it should be called periodically to poll for updates from the SAIFE network. It is up to the application to determine the period based on power, performance, bandwidth and other considerations.

A list of SAIFE contacts is part of the data downloaded from the SAIFE network. The SAIFE Dashboard is used to group the contacts in the list and manage them. The application can securely communicate with only the trusted contacts in its contact list.

`// Refresh data from SAIFE network`
` try {`
`   saife_ptr->UpdateSaifeData();`
` } catch (SaifeException& se) {`
`   std::cerr << "Failed to sync with SAIFE network. Error: " << se.error() << std::endl;`
`   return (1);`
` } catch (...) {`
`   std::cerr << "Failed to with SAIFE network with unexpected error" << std::endl;`
`   return (1);`
` }`

##Send Secure Messages

The SAIFE application uses saife::SaifeMessagingInterface::SendMessage to send secure messages to a contact in its contact list. The message will reside in the SAIFE network until retrieved by the contact or until the specified time to live, whichever is shorter. A delivery confirmation may also be requested if relevent to the application.

The following code snippet demonstrates how to get the saife::SaifeContact using a lookup by alias function and securely send a message to it. A custom address may also be provided to this function if the application has defined an alternate addressing scheme.

`SaifeContact contact = saife_ptr->GetContactByAlias(backup_server_alias);`
`saife_ptr->SendMessage(msg_bytes, kMessageType, contact, ttl_secs, tts_msecs, false);`

##Receive Secure Messages

The SAIFE application uses saife::SaifeMessagingInterface::Subscribe to register with the SAIFE network to receive messages. Subscription keeps a connection active with the SAIFE network so that it may deliver messages immediately. Once subscribed, saife::SaifeMessagingInterface::GetMessages is called periodically to see if any messages were received from the SAIFE network. It is up to the application to determine the best period. The saife::SaifeMessagingInterface::Unsubscribe function is called to unscubribe for messages and close the connection with the SAIFE network. This is useful for applications that don't want to maintain a persistent connection with the SAIFE network.

`saife_ptr->Subscribe();`
`std::vector<SaifeMessagingInterface::SaifeMessageData*> message_ptrs;`
`adapter_->GetMessages(kMessageType, &message_ptrs);`
`if ( 0 == message_ptrs.size()) {`
`  std::cout << "There are no messages " << std::endl;`
`} else {`
`  std::cout << "Received a message" << std::endl;`
`}`


#Release Notes

* Release 2.0.0 of the SAIFE® Endpoint Library was created on March 25, 2015.
* Release 1.0.0 of the SAIFE® Endpoint Library was created on September 15, 2014.



#Acknowledgements

The SAIFE® Endpoint Library is built upon several open-source technologies. SAIFE Inc. is committed to the open source community and maintains a public [GitHub account](https://github.com/saifeinc/) where all modifications to the open source code are posted.

The following is a complete list of open source projects packaged with our SAIFE products:

* **libroxml** - [http://code.google.com/p/libroxml/](http://code.google.com/p/libroxml/ LGPL) LGPL, forked to [https://github.com/saifeinc/libroxml_cecfork](https://github.com/saifeinc/libroxml_cecfork)
* **BOOST** - [http://www.boost.org](http://www.boost.org) Boost Software License Version 1.0
* **gtest** - [http://code.google.com/p/googletest/](http://code.google.com/p/googletest/) New BSD License
* **protobuf** - [http://code.google.com/p/protobuf/](http://code.google.com/p/protobuf/) BSD New
* **libcurl** - [http://curl.haxx.se/libcurl/](http://curl.haxx.se/libcurl/) - [http://curl.haxx.se/docs/copyright.html](http://curl.haxx.se/docs/copyright.html)
* **rapidjson** - [http://miloyip.github.io/rapidjson/](http://miloyip.github.io/rapidjson/) - The MIT License



