# mqtt
### Message Queuing Telemetry Transport

# Topics
### 1) Introduction MTQQ
### 2) Android and MQTT

      https://aws.amazon.com/id/what-is/mqtt/
      https://medium.com/swlh/android-and-mqtt-a-simple-guide-cb0cbba1931c

# -----------------------------------------------------------
# 1) Introduction
- MTQQ is message protocol to communicate between machine to machine also cloud to cloud.
- Smart sensor, IoT etc will send an data through network within limited resources and bandwidth.  
- IoT use MQTT to tranmize the data bc of easy to apply and efficient.


## Principe behind mqtt
- The MQTT protocol works on the principle of publish/subscribe model.
- Traditional network communication : The clients request resources or data from the server, then the server processes and sends back a response.
- sender (publisher) send a message to receiver (subscriber), broker will filter an incoming messages and distribute them correctly.
#### Rules :
- Space Decoupling
  - Both publisher and subscriber are not aware of each other's network location and do not exchange informations (IP addresses or port numbers).
- Time Decoupling
  - The publisher and subscriber dont run or have netwotk conectivity 
- Sync Decoupling
  - Both publishers and subscribers can send or receive messages without interrupting each other. For example, the subscriber does not have to wait for the publisher to send a message.

## MTQQ Components
### MTQQ Client
- Is a any devoce from a server to a microcontroller (is a single integrated circuit) that runs an MQTT lib.
- Publisher => if the client act as a publisher.
- Recevier => is the client act as receiver.
- Any device that communicates using MQTT over a network can be called an MQTT client device.
  
### MTQQ Broker
- Is the backend system which coordinates messages between the different clients.
- Responsibilities :
  - receiving and filtering msg
  - identifying clients subbscribed to each message and sending them the msg
  - auth mqtt clients
  - passing messages to other systems
  - handling missed messages and client sessions
    
### MQTT Connection
- Clients and brokers begin communicating by using an MQTT connection.
- Clients initiate the connection by sending a CONNECT message to the MQTT broker.
- The broker confirms that a connection has been established by responding with a CONNACK message.
- Both the MQTT client and the broker require a TCP/IP stack to communicate.
- Clients never connect with each other, only with the broker.

## How does MQTT work?
![Screenshot 2024-09-04 124557](https://github.com/user-attachments/assets/0986fa94-4b9c-4c81-82f0-3a5e8cba7a2b)

In MQTT context, an MQTT client is a device that connects to an MQTT broker over a network. The service provided by the MQTT broker (server) is the possibility to publish and/or subscribe on one or many topics.
In MQTT.

1) Before starting the message exchange over topics, the client needs to initiate the communication by sending the CONNECT message to the broker. The MAIN informations as follow :
    - **`ClientID`**:
      This identifier is used by the broker to recognize the client and store session information. An `empty ClientID` signifies an "anonymous" connection, meaning the broker will not retain any client-specific information.
   - **`CleanSession`**
   - **`KeepAlive`**
       Defines the maximum period of time that broker and client can remain in contact without sending a message. The client needs to send regular PING messages, within the KeepAlive period, to the broker to maintain the connection alive.
   - **`Username and Pass (optional)`**
       Client can send usn and pass to imprv communication secuirity. 
   - **`WillMessage (optional)`**
2) Once has been connected, client can EITHER publish messages, subscribe to specific messages, or do both.
3) When MQTT broker receives a message, it forwards its msg to subscribes who ARE INTERESTED.

Let’s break down the details for further understanding.
#### MQTT Topic
- Is an **string** which used to identify category in where the message will be send and receive.
- Topics are organized hierarchy, similiar to a file/files in directory.
- Ex : `home/livingroom/kitchen` `home/livingroom/beedroom1` 
    
#### MQTT Publish
- A client writes data on a topic using the **PUBLISH message**.
- MQTT clients publish messages that contain the topic and data in byte format.
- The clients determines data format (text data, bianry, xml, json)
- For example, sensor suhu mengirimkan data suhu ke topic home/livingroom/temperature.
- The message contains following information :
  - `Topic Name` => 
  - `Payload` => content of the message. An aplhanumeric string that needs to be interpreted by clients. 
  - `QoS Level` => indicates the Quality of Service Level (0, 1, 2)
  - `Retain Flag` =>
- Bellow an example :

```
Topic name: myhome/groundfloor/kitchen
Payload: 21.5
QoS level: 1
Retain flag: false
```
  
#### MQTT Subscribe
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
#### MTQQ Unsubscribe
- To delete existing subscriptions to a topic, client sends a **UNSUBSCRIBE message** to the broker.
- Content is the same as the SUBSCRIBE message: a list of subscriptions.

## MTQQ over WSS?
- MTQQ over WebSockets (WSS) is an MQTT implementation to receive data DIRECTLY INTO A WEB BROWSER. _
- The MTQQ protocol defines a JS client to provide WSS support for the browsers. (in this case, the protocol works as usual but it adds additional headers to the MQTT messages to also support the WWS protocol.
- You can think of it as the MQTT message payload wrapped in a WSS envelope.

## Is MQTT Secure?
- Yes, it uses SSL protocol to protect sensitive data transmitted by IoT devices.
- We can implememnt identity, authc, authr, between clients and the broker using SSL certifiactes and/or passwords.
- The MQTT broker typically authenticates clients using their passwords as well as unique client identifiers it allocates to each client.

## Is MQTT RESTful? NO
- In contrast, MQTT uses the publish/subscribe model of communication in the application layer and requires a standing TCP connection to transmit messages in a push manner.
-  HOWEVER, MQTT ver 5 provides a new requset/response method to act in a way similiar to REST. The publisher can attach a special response topic, which the receiver processes and generated appropiate response.

## MQTT vs HTTP
![image](https://github.com/user-attachments/assets/4273d641-a3d3-4359-9ddb-4110280fb96f)



# -----------------------------------------------------------
# 2) Android and MQTT




  



  


