# mqtt
### Message Queuing Telemetry Transport

# Introduction
- MTQQ is message protocol to communicate between machine to machine also cloud to cloud.
- Smart sensor, IoT etc will send an data through network within limited resources and bandwidth.  
- IoT use MQTT to tranmize the data bc of easy to apply and efficient.


# Principe behind mqtt
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

# MTQQ Components
## MTQQ Client
- Is a any devoce from a server to a microcontroller (is a single integrated circuit) that runs an MQTT lib.
- Publisher => if the client act as a publisher.
- Recevier => is the client act as receiver.
- Any device that communicates using MQTT over a network can be called an MQTT client device.
  
## MTQQ Broker
- Is the backend system which coordinates messages between the different clients.
- Responsibilities :
  - receiving and filtering msg
  - identifying clients subbscribed to each message and sending them the msg
  - auth mqtt clients
  - passing messages to other systems
  - handling missed messages and client sessions
    
## MQTT Connection
- Clients and brokers begin communicating by using an MQTT connection.
- Clients initiate the connection by sending a CONNECT message to the MQTT broker.
- The broker confirms that a connection has been established by responding with a CONNACK message.
- Both the MQTT client and the broker require a TCP/IP stack to communicate.
- Clients never connect with each other, only with the broker.

# How does MQTT work?
1) MTQQ client establish connection to MQTT broker
2) Once has been connected, client can EITHER publish messages, subscribe to specific messages, or do both.
3) When MQTT broker receives a message, it forwards its msg to subscribes who ARE INTERESTED.

Let’s break down the details for further understanding.
### MQTT Topic
- Istilah ‘topik’ mengacu pada kata kunci yang digunakan broker MQTT untuk memfilter pesan bagi klien MQTT.
- Topics are arraged hierarchically, similiar to a file/folder directory.
- Misalnya, mempertimbangkan sistem rumah pintar yang beroperasi di rumah bertingkat, dan rumah tersebut memiliki perangkat pintar yang berbeda di setiap lantainya. Dalam hal ini, broker MQTT dapat mengatur topik sebagai:
  - rumahkami/lantaidasar/ruangtamu/lampu
  - rumahkami/lantaipertama/dapur/suhu
    
### MQTT Publish
- MQTT clients publish messages that contain the topic and data in byte format.
- The clients determines data format (text data, bianry, xml, json)
- For example, a lamp in the smart home system may publish a message on for the topic livingroom/light.
  
### MQTT Subscribe
- MQTT clients send a SUBSCRIBE message to the MQTT broker, to receive messages on topics of interest



  


