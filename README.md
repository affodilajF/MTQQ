# mqtt (Message Queuing Telemetry Transport)

# Materials :
### 1) Introduction MQTT
### 2) Android and MQTT
### 3) MQTT Advanced

# Source :

      http://www.steves-internet-guide.com/mqtt-works/
      https://aws.amazon.com/id/what-is/mqtt/
      https://medium.com/swlh/android-and-mqtt-a-simple-guide-cb0cbba1931c
      https://www.emqx.com/en/mqtt-guide

# -----------------------------------------------------------
# 1) Introduction MQTT
- MQTT is message protocol to communicate between machine to machine also cloud to cloud.
- Smart sensor, IoT etc will send an data through network within limited resources and bandwidth.  
- IoT use MQTT to tranmize the data bc of easy to apply and efficient.


## A. Principe behind mqtt
- The MQTT protocol works on the principle of publish/subscribe model.
- Traditional network communication : The clients request resources or data from the server, then the server processes and sends back a response.
- sender (publisher) send a message to receiver (subscriber), broker will filter an incoming messages and distribute them correctly.
#### Note :
- Space Decoupling
  - Both publisher and subscriber **are not aware of each other's network location** and **do not exchange informations** (IP addresses or port numbers).
- Time Decoupling
  - The publisher and subscriber dont run or have netwotk conectivity 
- Sync Decoupling
  - Both publishers and subscribers can send or receive messages without interrupting each other. For example, the subscriber does not have to wait for the publisher to send a message.
- Messages are published to a broker on a topic.
- Clients never connect with each other, only with the broker.

## B. MQTT Components
### MQTT Client
- Is a any devoce from a server to a microcontroller (is a single integrated circuit) that runs an MQTT lib.
- Publisher => if the client act as a publisher.
- Recevier => is the client act as receiver.
- Any device that communicates using MQTT over a network can be called an MQTT client device.
  
### MQTT Broker
- Is the backend system which coordinates messages between the different clients.
- Responsibilities :
  - receiving and filtering msg
  - identifying clients subbscribed to each message and sending them the msg
  - auth mqtt clients
  - passing messages to other systems
  - handling missed messages and client sessions
- Popular Brokers : Eclipse Mosoquitto, HiveMQ, EMQX
    

## C. How does MQTT work?
<img src="https://github.com/user-attachments/assets/0986fa94-4b9c-4c81-82f0-3a5e8cba7a2b" width="400" alt="Screenshot 2024-09-04 124557">
<img src="https://github.com/user-attachments/assets/d1075273-5d87-45e8-bcab-65f5c35f4440" width="400" alt="Screenshot 2024-09-04 124557">
<img src="https://github.com/user-attachments/assets/72cfc7b5-5b90-45d5-9406-ad5f526d4c67" width="350" alt="Screenshot 2024-09-04 124557">

In MQTT context, an MQTT client is a device that connects to an MQTT broker over a network. The service provided by the MQTT broker (server) is the possibility to publish and/or subscribe on one or many topics.
In MQTT.

1) Before starting the message exchange over topics, the client needs to initiate the communication by sending the **CONNECT message** to the broker. The MAIN informations/connection parameters as follow :
    - **`ClientID`**:
      - This identifier is used by the broker to recognize the client and store session information. An `empty ClientID` signifies an "anonymous" connection, meaning the broker will not retain any client-specific information.
      - **Due to the uniqueness nature of the Client ID, if two clients connect to the same broker with the same Client ID, the client connects later will force the one connected earlier to go offline.**
   - **`CleanSession`**
     - A clean session is one in which the broker isn’t expected to remember anything about the client when it disconnects. With a non clean session the broker will remember client subscriptions and may hold undelivered messages for the client (however it may depends on the QoS).
     - Set to `**false**` means to create a presistent session. When client disconnectes, the session remains and saves offline message until the session expires. It makes possible for the subscribe client to receive messages while it has gone offline. The duration of presistent sessions depends on the broker's settings, maybe 5 mins.
     - Note:
       **The premise of persistent session recovery is that the client reconnects with a fixed Client ID. If the Client ID is dynamic, then a new persistent session will be created.**
     - Set to `**true**` to create a new temporary session that is automatically destroyed when the client disconnects. 
   - **`KeepAlive`**
       - Defines the maximum period of time that broker and client can remain in contact without sending a message. **The client needs to send regular PING messages**, within the KeepAlive period, to the broker to maintain the connection alive. MQTT clients publish a keepalive message at regular intervals (usually 60 seconds) which tells the broker that the client is still connected.
   - **`Username and Pass (optional)`**
       - Client can send usn and pass to imprv communication secuirity.
       - But, if the **underlying transport layer is not encrypted, the username and pass will be transmitted in plaintext**, use `mqtts` or `wss` ptotocol is recommended. 
   - **`WillMessage (optional)`**
       - Is to notify a subscriber that the publisher is unavailable due to network outage.
       - The last will message is set by the publishing client, and is set on a per topic basis which means that each topic can have its own last will message. (This means that each topic can have its own last will message associated with it.) The message is stored on the broker and sent to any subscribing client (to that topic) if the connection to the publisher fails.
       - Contains Topic, Payload, QoS, Retain, etc.

2) Broker will send an **CONNACK** to the client whether to connection is ok or refused. Once has been connected, client can EITHER publish messages, subscribe to specific messages, or do both.  If the client does not receive a CONNACK packet from the broker in time (usually a configurable timeout from the client side), it may actively close the network connection.
3) When MQTT broker receives a message, it forwards its msg to subscribes who ARE INTERESTED.

Let’s break down the details for further understanding.
### MQTT Topic
- Is an **string** which used to identify category in where the message will be send and receive.
- Topics are organized hierarchy, similiar to a file/files in directory.
- Ex : `home/livingroom/kitchen` `home/livingroom/beedroom1` 
    
### MQTT Publish
- A client writes data on a topic using the **PUBLISH message**.
- MQTT clients publish messages that contain the topic and data in byte format.
- The clients determines data format (text data, bianry, xml, json)
- For example, sensor suhu mengirimkan data suhu ke topic home/livingroom/temperature.
- The message contains following information :
  - `Topic Name` => 
  - `Payload` => content of the message. An aplhanumeric string that needs to be interpreted by clients. 
  - `QoS Level` => indicates the Quality of Service Level (0, 1, 2)
  - `Retain Flag` => If set to true, the broker stores the last message sent on a topic and delivers it to any new subscriber to that topic.
- Bellow an example :

```
Topic name: myhome/groundfloor/kitchen
Payload: 21.5
QoS level: 1
Retain flag: false
```
  
### MQTT Subscribe
- MQTT clients send a **SUBSCRIBE message** to the MQTT broker, to receive messages on topics of interest.
- The message contains following informations :
  - `Topic Name`
  - `QoS Level`
- Bellow an example :
```
Topic name: myhome/groundfloor/kitchen
QoS level: 1
Topic name: myhome/groundfloor/dining
QoS level: 1
...
Topic name: myhome/groundfloor/bathroom
QoS level: 1
```

### Normal Subscription and Shared Subscription
- **Normal Subs** => Biasaa
- **Shared Subs**

  Steps =>
  - Subscriber sent a subscription request to broker using specific format (seperti `$share/<GroupID>/<Topic>`)
  - Broker sent subback
  - Each publish message will be sent randomly to one subscriber.
  - That subscriber will sent the publish message to anoter subscriber. 


### MQTT Unsubscribe
- To delete existing subscriptions to a topic, client sends a **UNSUBSCRIBE message** to the broker.
- Content is the same as the SUBSCRIBE message: a list of subscriptions.

### MQTT Disconnect
- MQTT Client is disconnect the connection to broker.
- The client sends a disconnect message to the broker, but the broker does not automatically send a confirmation after the connection is disconnected. Namun, jika klien perlu memastikan bahwa koneksi telah diputuskan, maka klien dapat menunggu beberapa detik sebelum memastikan bahwa koneksi telah selesai.

## D. MQTT over WSS?
- MQTT over WebSockets (WSS) is an MQTT implementation to receive data DIRECTLY INTO A WEB BROWSER. _
- The MQTT protocol defines a JS client to provide WSS support for the browsers. (in this case, the protocol works as usual but it adds additional headers to the MQTT messages to also support the WWS protocol.
- You can think of it as the MQTT message payload wrapped in a WSS envelope.

## E. Is MQTT Secure?
- Yes, it uses SSL protocol to protect sensitive data transmitted by IoT devices.
- We can implememnt identity, authc, authr, between clients and the broker using SSL certifiactes and/or passwords.
- The MQTT broker typically authenticates clients using their passwords as well as unique client identifiers it allocates to each client.

## F. Is MQTT RESTful? NO
- In contrast, MQTT uses the publish/subscribe model of communication in the application layer and requires a standing TCP connection to transmit messages in a push manner.
-  HOWEVER, MQTT ver 5 provides a new requset/response method to act in a way similiar to REST. The publisher can attach a special response topic, which the receiver processes and generated appropiate response.

## G. MQTT vs HTTP
<img src="https://github.com/user-attachments/assets/4273d641-a3d3-4359-9ddb-4110280fb96f" width="500">


## H. Quality Of Service (QoS)
- Is an agreement between sender and receiver on the guarantee of delivering a message.
- Why its matter> improves reliability.
- By raising the QoS level, you will increase the reliability of the communication, but you will decrease the performance.
- Three levels
- ### 0 - At most once (called "fire and forget")
  - No guarantee of message delivery.
  - Use it when have stable communication channel and when the loss of message is acceptable.
  - Acknowledgement => NONE gaada gaperlu 
- ### 1 - At least once
  - Guarantees that a message is delivered at least one time to the receiver.
  - The publisher will send messages repeatdly to the broker until the broker sends a **PUBACK** to the publisher to confirm that the message has been received by the broker. 
  - Use it when clients can tolerate duplicate messages. It’s the most used.
  - Acknowledgement => PUBACK
- ### 2 - Exactly Once
  - Guarantees that message is received only once by the receiver.
  - Use it when your application is critical and you cannot tolerate loss and duplicate messages.
  - Acknowledgement => PUBREC PUBREL PUBCOMP
  - Step :
    - Publisher sends publish (message) to the broker
    - Broker sends **pubrec** to publisher for temporary confirmation
    - Broker sends message to subscriber
    - Subscriber sends **pubrel** to broker
    - Broker sends **pubcomp** for final confirmation. 

## I. ACKNOWLEDGEMENT
0 => Sucesses 

128 or others => failed 
- ### CONNACK
  - To confirm whether the **connection** was successfully established or not.
  - Sent by broker, received by client.
- ### PUBACK
  - To confirm whether the **message** was successfully received or nah, as a result of the publisher's message-sending process.
  - Sent by broker, received by publisher
- ### SUBBACK
  - To confirm whether the subscription request was successfully processed by the subscriber.
  - Sent by broker, received by subscriber. 



# -----------------------------------------------------------
# 2) Android and MQTT
- MQTT works on TCP/IP stack, this means that the only requirement of the mobile device is the capability to connect to the internet.
- TCP is a connection orientated protocol with error correction and guarantees that packets are received in order. Consider a TCP/IP connection to be similar to a telephone connection. Once a telephone connection is established you we talk over it until one party hangs up.
- Most MQTT clients will connect to the broker and remain connected even if they aren’t sending data.


### Problems that could arise :
#### 1. Energy Consumption Issues
##### Issues :
- **`Continuous Connection`** => Apps maintain a constant connection to the MQTT broker, could result to darin battery life. 
- **`Network Usage`** => As data transmission requires power from the device's network components. 
##### Optimization Strategies :
- **`Periodic Connections`**
- **`Quality of Service (QoS) Settings`**
  - choose the appropriate QoS level.
- **`Network Resource Management`**
  - Such as reducing the frequency of updates/combining multiple messages into a single transmission to decrease the number of network interactions. 

#### 2. Background Service Issues : 
##### Issues : 
- When using MQTT in an Android app, we might need to keep the app connected to the MQTT broker even when the app is not actively being used. This is where background services come into play.
- Android limits how apps run in the background to save battery and system resources, so we need to handle this properly.
##### Strategies : 
- **`Foreground Service`**
  - Keep the MQTT connection active
  - Show a notification that the service is running
- **`JobScheduler`**
  - Seheduling periodic tasks when continous connectivity isnt needed.
- **`WorkManager`**
  - Use for reliable background tasks that **need to run with certain conditions**.
  - Use for handle device restarts.  


# -----------------------------------------------------------
# 3) MQTT Advanced 

## A. Retained Messages
## B. Will Messages 
## C. Request/response 
## D. User Properties 
## E. Topic Alias
## F. Payload Format Indicator & Content Type 
## G. Shared Subscriptions 
## H. Subscriptions Options 
## I. Subscriptions Identifier
## J. Keep Alive
## K. Message Expiry Interval 
## L. Maximum Packet Size
## M. Reason Codes & Quick Reference
## N. Enhanced Auth
## O. Control Packets



  


