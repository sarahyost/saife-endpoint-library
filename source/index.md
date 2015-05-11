---
title: SAIFE Endpoint Library v2.0.1 Documentation

language_tabs:
  - cpp: C++
  - java: Java

toc_footers:
  - Version 2.0.1
  - <a href='https://saifeinc.com/developers'>Become a SAIFE Developer</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>
  - Copyright 2014-2015 SAIFE, Inc.
  - All Rights Reserved
  - <a href='https://github.com/saifeinc'>Fork on Github</a>

search: true
---

#Overview

**Welcome to the SAIFE® Endpoint Library Documentation!** 

SAIFE is a patented security platform that provides trusted routing over untrusted networks for any IP-based endpoint. SAIFE, Inc. created a secure, distributed, global network to create secure tunnels between SAIFE-enabled devices. All traffic is signed, bi-directionally authenticated, and encrypted.

Integrating the SAIFE Endpoint Library into your applications allows you to build your own self-contained public key infrastructure (PKI). Every endpoint and network component is given a unique public certificate, which is validated and signed by [the SAIFE Dashboard](https://dashboard.saifeinc.com/). The Dashboard also allows you to provision endpoints and create secure groups, ensuring that communication only occurs between the specified, trusted endpoints within a secure group. 

This document will guide you in integrating the SAIFE platform and features into your applications. The Endpoint Library ensures the authenticity of all your endpoints, whether small sensors, smartphones, laptops, or smart refrigerators, as well as the integrity of all your data. Two versions of the library are currently available, in C++ and Java, providing 128 bit encryption. If your project requires 256 bit encrpytion, please [contact us](mailto:sales@saifeinc.com).

Let's work together to secure the Internet of Things! Feel free to check out the recently released [SAIFE® Management API documentation](http://saifeinc.com/developers/libraries/management/) as well, for creating custom management dashboards, and refer to [saifeinc.com](http://saifeinc.com/), our [developer page](http://saifeinc.com/developers/), or our [support forums](https://saife.zendesk.com/hc/en-us) for a a thorough introduction to the SAIFE system architecture. 

This document is divided in the following sections:

* [Getting Started](#getting-started) - for those of you who want to start compiling and running immediately!
* [API Overview](#api-overview) - a brief description of the key API classes.
* [Anatomy of a SAIFE Application](#anatomy-of-a-saife-application) - provides an in-depth explanation of the anatomy of a SAIFE application.
* [Release Notes](#release-notes) - latest updates.
* [Acknowledgements](#acknowledgements) - thanks, copyright information, and GitHub link.



#Getting Started

##Downloading the Endpoint Source Code

The SAIFE® Endpoint library can be downloaded for free at [store.saifeinc.com](https://store.saifeinc.com/). You'll be able to select the Java version, C++ version, or both. The download packages come with a version of this guide, auto-generated code documentation, and our Developer Agreement.

As a note, SAIFE's technology is export-controlled - distribution outside of United States-approved countries can get tricky. Please refer to the Developer Agreement or [contact us](mailto:sales@saifeinc.com) for more information. 

Your downloaded files will include:

**C++:**
* libSaife-2.0.1-linux_x86_64_gnu.tar.gz (for 64 bit Linux; other versions available upon request)
* libSaife-2.0.1-documentation.tar.gz
* SAIFE_Developer_Agreement_v1.pdf

**Java:**
* java-lib-2.0.1-linux_x86_64.tar.gz (for 64 bit Linux; other versions available upon request)
* Java Doc: java-lib-2.0.1-javadoc.jar
* SAIFE_Developer_Agreement_v1.pdf



##Installation

Once the APIs are downloaded, the next step is to make a directory to unpack the SAIFE header files and shared library files. You may also unpack them to a system location such as `/usr/local`.

```c++
~$ mkdir ~/Development/Saife
~$ cd ~/Development/Saife
~$ tar -xzf  libSaife-1.0.0-documentation.tar.gz
~$ tar -xzf  libSaife-1.0.0-linux_x86_64_gnu.tar.gz
```
```java
~$ mkdir ~/Development/Saife
~$ mkdir ~/Development/Saife/javadoc
~$ cd ~/Development/Saife
~$ tar -xzf  java-lib-2.0.1-linux_x86_64.tar.gz
~$ cd ~/Development/Saife/javadoc
~$ jar -xf  java-lib-2.0.1-javadoc.jar
```

<code>
~$ mkdir ~/Development/Saife </br>
~$ cd ~/Development/Saife </br>
~$ tar -xzf  libSaife-1.0.0-documentation.tar.gz </br>
~$ tar -xzf  libSaife-1.0.0-linux_x86_64_gnu.tar.g </br>
</code>

The following is a sketch of the C++ resulting directory structure:

   |    |
---|----|
doc/        | Saife C++ Library documentation for offline usage |
include/    | Saife C++ Library header files  |
lib/        | All the Saife C++ Libraries needed to link to your application  |

The following is a sketch of the Java resulting directory structure:

   |    |
---|----|
javadoc/    | Saife Java Library documentation for offline usage |
./          | All the Java jar files needed for your application |
lib/        | All the JNI native libraries needed to link to your application |


##Developer Account

You will need to set up a free **SAIFE® Dashboard** account to get started. Visit [dashboard.saifeinc.com](https://dashboard.saifeinc.com) and register with your email address. You may want to create an organization and a group at your first log-in - endpoint applications will require an organizationID to be provisioned and a groupID to communicate with other applications. 

Later, you can create your own custom management dashboard using SAIFE's Management Services API, but for now, the SAIFE Dashboard will make provisioning and testing applications much easier.


##Start Coding

Please refer to the sample applications on SAIFE's [Github account](https://github.com/saifeinc/examples-saife-endpoint-lib), which showcase the features of the Library.

 * **SaifeEchoDemo** - A simple, secure messaging and sessions application using SAIFE Endpoint Library.

The [Anatomy of a SAIFE Application](#anatomy-of-a-saife-application) section, below, provides an in-depth explanation of how a SAIFE messaging application can be built.


###Simple Build Command

Use the following command to build a single file application. Set the environment variable to the location of SAIFE Endpoint Library.

`~$ export SAIFE_HOME=~/Development/Saife`

`~$ g++ -o hello_world hello_world.cc -I$SAIFE_HOME/include -L$SAIFE_HOME/lib/saife -lsaife -lCecCryptoEngine -lroxml`



#API Overview

##Saife Interface

Your application will use a single interface to command and control the SAIFE® Endpoint Library. This is an aggregation of sub-interfaces, each of which encapsulates a specific set of functions. The sub-interfaces and their respective functionality are listed below.

```c++
Aggregation of the sub-interfaces that comprise the SAIFE Endpoint Library interface.
#include <saife_interface.h>
```
 * **Saife Management Interface** - this interface contains the methods used to manage the state of the SAIFE Endpoint Library and communication with the SAIFE network.
 * **Saife Messaging Interface** - this interface contains the methods used to secure messages between two SAIFE-enabled endpoints.
 * **Saife Contact Interface** - this interface contains the methods needed to be able to address other SAIFE-enabled endpoints.
 * **Saife Session Interface** - this interface contains the methods used to secure session between two SAIFE-enabled endpoints.

##SaifeFactory Class

SAIFE Endpoint Library uses the factory method design pattern to create instances of the Saife interface. 

#Anatomy of a SAIFE Application
A typical SAIFE application will inlcude both secure messaging and secure streaming via sessions. The following tasks will be performed:

1. Initialize
2. Generate keypair
3. Update SAIFE data
4. Unlock SAIFE
5. Subscribe for messages
6. Synchronize contacts 
7. Send/Receive messages
8. Enable presence 
9. Initiate/Accept sessions


##Initialize

```c++
try {
  // Create instance of SAIFE. A log manager may be optionally specified to redirect SAIFE logging.
  SaifeFactory factory;
  saife_ptr = factory.ConstructLocalSaife(NULL);

  // Set SAIFE logging level
  saife_ptr->SetSaifeLogLevel(LogSinkInterface::SAIFE_LOG_WARNING);

  // Initialize the SAIFE interface
  SaifeManagementState state = saife_ptr->Initialize(defaultKeyStore);
} catch (InvalidManagementStateException& e) {
  std::cerr << e.error() << std::endl;
} catch (SaifeInvalidCredentialException& e) {
  std::cerr << e.error() << std::endl;
} catch (...) {
  std::cerr << "Failed to initialize library with unexpected error" << std::endl;
}
```

```java
try {
  // final LogSinkManager logMgr = LogSinkFactory.constructFileSinkManager(defaultKeyStore + "/log");
  // final LogSinkManager logMgr = LogSinkFactory.constructConsoleSinkManager();

  // Create instance of SAIFE. A log manager may be optionally specified to redirect SAIFE logging.
  saife = SaifeFactory.constructSaife(null);

  // Set SAIFE logging level
  saife.setSaifeLogLevel(LogLevel.SAIFE_LOG_WARNING);

  // Initialize the SAIFE interface

  final ManagementState state = saife.initialize(defaultKeyStore);
} catch (final InvalidManagementStateException e) {
  e.printStackTrace();
} catch (final InvalidCredentialException e) {
  e.printStackTrace();
} catch (final IOException e) {
  e.printStackTrace();
}
```
Initializing the SAIFE Endpoint Library involves the following steps:

1. Construct an instance of SAIFE Endpoint library
2. Optionally set the desired logging level
3. Initialize SAIFE Endpoint Library

The initialize step returns one of the following states:

* ERROR
* INITIALIZED
* UNKEYED

The UNKEYED state indicates that a keypair does not exist and the application is not trusted by the SAIFE network. This is always the case when the application starts for the first time. It may also occur if the keystore has been removed as a result of wipe or revocation.  The application must create a key pair and restart.

The ERROR state indicates that SAIFE Endpoint Library failed to initialize correctly.  The log must be analyzed to find out why.

The INITIALIZED state indicates that the SAIFE Endpoint Library is ready for use.


##Generate keypair

```c++
if (state == saife::SAIFE_UNKEYED) {
  // The UNKEYED state is returned when SAIFE doesn't have a public/private key pair.

  // Setup the DN attributes to be used in the X509 certificate.
  const DistinguishedName dn("HelloWorldApp");

  // Setup an optional list of logical addresses associated with this SAIFE end point.
  const std::vector<SaifeAddress> address_list;

  // Generate the public/private key pair and certificate signing request.
  CertificateSigningRequest *certificate_signing_request = new CertificateSigningRequest();
  saife_ptr->GenerateSmCsr(dn, defaultPassword, address_list, certificate_signing_request);

  // Add additional capabilities to the SAIFE capabilities list that convey the application specific capabilities.
  std::vector< std::string > capabilities = certificate_signing_request->capabilities();
  capabilities.push_back("com::saife::demo::echo");

  // Provide CSR and capabilities (JSON string) to user for provisioning.
  // The application must restart from the UNKEYED state.
}
```

```java
if (state == ManagementState.UNKEYED) {
  // The UNKEYED state is returned when SAIFE doesn't have a public/private key pair.

  // Setup the DN attributes to be used in the X509 certificate.
  final DistinguishedName dn = new DistinguishedName("SaifeEcho");

  // Setup an optional list of logical addresses associated with this SAIFE end point.
  final List<Address> addressList = new ArrayList();

  // Generate the public/private key pair and certificate signing request.
  final CertificationSigningRequest csr = saife.generateSmCsr(dn, defaultPassword, addressList);

  // Add additional capabilities to the SAIFE capabilities list that convey the application specific capabilities.
  final List<String> capabilities = csr.getCapabilities();
  capabilities.add("com::saife::demo::echo");

  // Provide CSR and capabilities (JSON string) to user for provisioning.
  // The application must restart from the UNKEYED state.
}
```

The UNKEYED state indicates that a keypair does not exist and the application is not trusted by the SAIFE network. This is the case when the application starts for the first time. It may also occur if the keystore has been removed as a result of wipe or revocation. 

The following steps are necessary to generate the required data to become trusted on the SAIFE network.

* Prompt user for password.
* Specify the X509 DN attributes.  Common name is a required attribute.
* Optionally specify logical addresses to associate with the SAIFE endpoint.
* Generate key pair and certificate signing request (CSR).
* Augment generated capabilities with application-specific capabilities.

The application must restart after completing successfully completing the above steps.  The CSR and capablities (converted to a JSON string) are persisted or provided to the user to be used with the SAIFE Dashboard to make this SAIFE endpoint a trusted entity in the SAIFE network.  This process is referred to a provisioning.  Alternatively, the application may use the SAIFE Management Services RESTful API to automate the provisioning process.

##Update SAIFE Data

```c++
// Periodically update SAIFE data
try {
  saife_ptr->UpdateSaifeData();
} catch (InvalidManagementStateException e) {
  std::cerr << e.error() << std::endl;
} catch (SaifeIoException e) {
  std::cerr << e.error() << std::endl;
}
```
```java
// Periodically update SAIFE data
try {
  saife.updateSaifeData();
} catch (final InvalidManagementStateException e) {
  e.printStackTrace();
} catch (final IOException e) {
  e.printStackTrace();
}
```

The application must periodically download data from the SAIFE network to its signed certificate. This data will include revocation updates, new contact updates, and other security-critical information. It is up to the application to determine the period based on power, performance, bandwidth, and other considerations.

## Unlock SAIFE
```c++
// Unlock SAIFE library with user's credential
try {
  saife_ptr->Unlock(defaultPassword);
} catch (SaifeInvalidCredentialException e) {
  std::cerr << e.error() << std::endl;
} catch (InvalidManagementStateException e) {
  std::cerr << e.error() << std::endl;
} catch (AdminLockedException e) {
  std::cerr << e.error() << std::endl;
}
```
```java
// Unlock SAIFE library with user's credential
try {
  saife.unlock(defaultPassword);
} catch (final InvalidCredentialException e1) {
  e1.printStackTrace();
} catch (final InvalidManagementStateException e1) {
  e1.printStackTrace();
} catch (final AdminLockedException e1) {
  e1.printStackTrace();
}
```
The security of the application begins with a strong password from the user.  This password is used by SAIFE to unlock access to keys used to secure all data.  The application must prompt the user for the password and never persist it.  The SAIFE Endpoint Library cannot provide any service until it is unlocked.  The application may check the lock status and lock access if the user is inactive.

## Subscribe for messages
```c++
// Subscribe for SAIFE messages
saife_ptr->Subscribe();
```
```java
// Subscribe for SAIFE messages
saife.subscribe();
```
The application must subscribe for messages in order to receive messages from other applications.  The SAIFE Endpoint Library attempts to maintain the subscription as much as possible, however it is not guaranteed.  The application must monitor the subscription state and re-subscribe if the subscription state changes to UNSUBSCRIBED.

## Synchronize contacts
```c++
// Request a contact list re-sync
try {
  saife_ptr->SynchronizeContacts();
} catch (InvalidManagementStateException e) {
  std::cerr << e.error() << std::endl;
}
```
```java
// Request a contact list re-sync
try {
  saife.synchronizeContacts();
} catch (final InvalidManagementStateException e) {
  e.printStackTrace();
}
```
A list of SAIFE contacts is maintained by the SAIFE endpoint library and updates are downloaded from the SAIFE network. The SAIFE Dashboard is used to group the contacts in the list and manage them. The application can securely communicate with only the trusted contacts in its contact list.

When an application performs contact synchronization, the local contact list is deleted and a request is made to the SAIFE network for full download.

##Send Secure Messages

```c++
try {
  SaifeContact contact = saife_ptr->GetContactByAlias(sendTo);
  std::string sendMsg = "one";
  std::vector<uint8_t> msg_bytes(sendMsg.begin(), sendMsg.end());
  saife_ptr->SendMessage(msg_bytes, echoMsgType, contact, 30, 2000, false);
} catch (NoSuchContactException e) {
  std::cout << "Oops .. '" << sendTo << "' no such contact.  Go to the Dashboard to manage contacts." << std::endl;
} catch (SaifeIoException e) {
  std::cout << "Oops ... seems like we couldn't send message." << std::endl;
} catch (LicenseExceededException e) {
  std::cerr << e.error() << std::endl;
}
```
```java
try {
  final Contact contact = saife.getContactByAlias(sendTo);
  final String sendMsg = "one";
  saife.sendMessage(sendMsg.getBytes(), echoMsgType, contact, 30, 2000, false);
} catch (final NoSuchContactException e) {
  System.out.println("Oops .. '" + sendTo + "' no such contact.  Go to the Dashboard to manage contacts.");
} catch (final IOException e) {
  System.out.println("Oops ... seems like we couldn't send message.");
} catch (final LicenseExceededException e) {
  e.printStackTrace();
}
```

The SAIFE application can send secure messages to all contacts in its contact list. The messages will reside in the SAIFE network until retrieved by the intended recipient or until the specified time to live, whichever is shorter. A delivery confirmation may also be requested if desired by the application.

##Receive Secure Messages

```c++
    try {
      std::vector<SaifeMessagingInterface::SaifeMessageData *> msgs;
      saife_ptr->GetMessages(echoMsgType, &msgs);
      for (std::vector<SaifeMessagingInterface::SaifeMessageData*>::iterator iter = msgs.begin();
          iter != msgs.end(); ++iter) {
        SaifeMessagingInterface::SaifeMessageData *msg = *iter;
        std::string msgstr(msg->message_bytes.begin(), msg->message_bytes.end());
        std::cout << "M:" << msg->sender.alias() << " '" << msgstr << "'" << std::endl;
      }
    } catch (NoSuchContactException e) {
      std::cerr << e.error() << std::endl;
    } catch (SaifeIoException e) {
      std::cerr << e.error() << std::endl;
    } catch (InvalidManagementStateException e) {
      std::cerr << e.error() << std::endl;
    }
```
```java
try {
  final List<MessageData> msgs = saife.getMessages(echoMsgType);
  for (final MessageData msg : msgs) {
    System.out.println("M:" + msg.sender.getAlias() + " '" + new String(msg.message) + "'");
  }
} catch (final InterruptedException e) {
  break;
} catch (final IOException e) {
  e.printStackTrace();
} catch (final InvalidManagementStateException e) {
  e.printStackTrace();
}
```
The SAIFE application must be subscribed with the SAIFE network to receive messages. Subscription keeps a connection active with the SAIFE network so that it may deliver messages immediately to the SAIFE endpoint. Once subscribed, the application must call periodically check to see if any messages were received from the SAIFE network. It is up to the application to determine the best period. The application may unscubribe for messages which would close the connection with the SAIFE network. This is useful for applications that don't want to maintain a persistent connection with the SAIFE network.

## Enable presence
```c++
// Enable presence for the SAIFE application 
try {
  saife_ptr->EnablePresence();
} catch (InvalidManagementStateException e) {
  std::cerr << e.error() << std::endl;
} catch (UnlockRequiredException e) {
  std::cerr << e.error() << std::endl;
}
```
```java
// Enable presence for the SAIFE application
try {
  saife.enablePresence();
} catch (final InvalidManagementStateException e1) {
  e1.printStackTrace();
} catch (final UnlockRequiredException e1) {
  e1.printStackTrace();
}
```

The SAIFE application must enable presence in order to establish secure sessions.  Enabling presence allows the endpoint to be notified of incoming secure session requests.

## Initiate session
```c++
try {
  SaifeContact contact = saife_ptr->GetContactByAlias(sendTo);
  SaifeSecureSessionInterface *session = saife_ptr->ConstructSecureSession();
  session->Connect(contact, SaifeSecureSessionInterface::LOSSY, 10);

  std::string sendMsg = "one";
  std::vector<uint8_t> msg_bytes(sendMsg.begin(), sendMsg.end());
  session->Write(msg_bytes);
  std::cout << "Data >: '" << sendMsg << "'" << std::endl;
  try {
    std::vector< uint8_t > data;
    session->Read(&data, 1024, 5);
    std::string datastr(data.begin(), data.end());
    std::cout << "Data <: '" << datastr << "'" << std::endl;
  } catch (SessionTimeoutException e) {
    std::cout << "Huh ... No big deal." << std::endl;
  }
  session->Close();
  saife_ptr->ReleaseSecureSession(session);

} catch (SessionTimeoutException e) {
  std::cout << "Oops ... seems like we couldn't connect securely." << std::endl;
} catch (PresenceRequiredException e) {
  std::cout << "Oops ... Looks like presence isn't ready." << std::endl;
} catch (NoSuchContactException e) {
  std::cout << "Oops ... Looks like we aren't allowed to securely communicate with this contact yet." << std::endl;
} catch (SaifeIoException e) {
  std::cout << "Oops ... seems like we couldn't connect." << std::endl;
}
```

```java
try {
  final Contact contact = saife.getContactByAlias(sendTo);
  final SecureSession session = saife.constructSecureSession();
  session.connect(contact, TransportType.LOSSY, 10);
  String sendMsg = "one";
  session.write(sendMsg.getBytes());
  System.out.println("Data >: '" + sendMsg + "'");
  try {
    final byte[] data = session.read(1024, 5);
    System.out.println("Data <: '" + new String(data) + "'");
  } catch (final SessionTimeoutException e) {
    System.out.println("Huh ... No big deal.");
  }
  session.close();
  saife.releaseSecureSession(session);
} catch (final SessionTimeoutException e) {
  System.out.println("Oops ... seems like we couldn't connect securely.");
} catch (final PresenceRequiredException e) {
  System.out.println("Oops ... Looks like presence isn't ready.");
} catch (final NoSuchContactException e) {
  System.out.println("Oops ... Looks like we aren't allowed to securely communicate with this contact yet.");
} catch (final IOException e) {
  System.out.println("Oops ... seems like we couldn't connect.");
}
```
The SAIFE application can establish secure sessions to contacts in its contact list.  Both participants in the session must have presence enabled and established with the SAIFE network in order establish a secure session.  The application may specify a LOSSLESS (TCP) or LOSSY (UDP) transport type for the secure session.  It is up to the applications to properly frame data sent/received over secure sessions.

## Accept session
```c++
try {
  // Wait for SAIFE clients to connect securely
  SaifeSecureSessionInterface *session = saife_ptr->Accept();
  SaifeContact peer = session->GetPeer();
  std::cout << "Hey ... " << peer.alias() << " just connected." << std::endl;
  // Service session in a new thread
} catch (InvalidManagementStateException e) {
  std::cerr << e.error() << std::endl;
} catch (PresenceRequiredException e) {
  std::cout << "Oops ... Looks like presence isn't ready." << std::endl;
} catch (InvalidSessionState e) {
  std::cerr << e.error() << std::endl;
}
```

```java
try {
  // Wait for SAIFE clients to connect securely
  final SecureSession session = saife.accept();
  final Contact peer = session.getPeer();
  System.out.println("Hey ... " + peer.getAlias() + " just connected. sess: " + session);
  // Service session in a new thread
} catch (final InvalidManagementStateException e) {
  e.printStackTrace();
} catch (final PresenceRequiredException e) {
  System.out.println("Oops ... Looks like presence isn't ready.");
} catch (final InvalidSessionState e) {
  e.printStackTrace();
}
```
The SAIFE application can accept incoming secure sessions from contacts in its contact list.  Both participants in the session must have presence enabled and established with the SAIFE network in order establish a secure session.  The incoming session may have a LOSSLESS (TCP) or LOSSY (UDP) transport type.  It is up to the application to properly frame data sent/received over secure sessions.


# Questions & Additional Resources

* SAIFE's [support site](https://saife.zendesk.com/hc/en-us) contains forums, FAQs, and release notes for all of SAIFE, Inc.'s products, including the Libraries. You can also reach out to us directly at support@saifeinc.com.
* For a thorough introduction to the SAIFE system architecture, check out our [developer page](http://saifeinc.com/developers/). Or learn more via our [security blog](http://saifeinc.com/news/), or our [whitepapers](http://saifeinc.com/papers/) and instructional [videos](http://saifeinc.com/videos/).
* Please also check out the recently released [SAIFE® Management API documentation](http://saifeinc.com/developers/libraries/management/) for creating custom management dashboards.


# Release Notes

* **Release 2.0.1** of the SAIFE® Endpoint Library was created on May 08, 2015.
* **Release 2.0.0** of the SAIFE® Endpoint Library was created on March 25, 2015.
* **Release 1.0.0** of the SAIFE® Endpoint Library was created on September 15, 2014.


# Acknowledgements

The SAIFE® Endpoint Library is built upon several open-source technologies. SAIFE Inc. is committed to the open source community and maintains a public [GitHub account](https://github.com/saifeinc/) where all modifications to the open source code are posted.

The following is a complete list of open source projects packaged with our SAIFE Endpoint Library:

* **libroxml** - [http://code.google.com/p/libroxml/](http://code.google.com/p/libroxml/ LGPL) LGPL, forked to [https://github.com/saifeinc/libroxml_cecfork](https://github.com/saifeinc/libroxml_cecfork)
* **BOOST** - [http://www.boost.org](http://www.boost.org) Boost Software License Version 1.0
* **gtest** - [http://code.google.com/p/googletest/](http://code.google.com/p/googletest/) New BSD License
* **protobuf** - [http://code.google.com/p/protobuf/](http://code.google.com/p/protobuf/) BSD New
* **libcurl** - [http://curl.haxx.se/libcurl/](http://curl.haxx.se/libcurl/) - [http://curl.haxx.se/docs/copyright.html](http://curl.haxx.se/docs/copyright.html)
* **rapidjson** - [http://miloyip.github.io/rapidjson/](http://miloyip.github.io/rapidjson/) - The MIT License



