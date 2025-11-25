# Water Level Monitor with Arduino and AWS Integration

<img src="https://img.shields.io/badge/Arduino-00979D?style=for-the-badge&logo=Arduino&logoColor=white"> <img src="https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54"> <img src="https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white">

## About the Project

The project consists of a **water level monitor using an Arduino board with AWS cloud integration**.

For this purpose, an **ultrasonic sensor** was used to calculate the water level at regular intervals. The collected metrics are passed to a **Python application** that sends the data to the **SQS messaging service on AWS**.

Data processing is handled by a **Lambda function**, which subscribes to the queue topic to receive the collected metrics. The function sends an **email through an SNS topic** if a change is detected in the water level, and the collected data is also recorded in a **DynamoDB table for historical records**.

## Project Structure

```
├── arduino
    └── sketch.ino
├── funcao_lambda
    └── lambda_function.py
└── funcao_lambda
    └── monitor-nivel-agua.py
```

## Materials

- Breadboard
- Arduino Mega 2560
- Ultrasonic sensor
- USB 2.0 B cable for Arduino connection

## AWS Architecture

```
+-----------------+      +-----------------------------------+
| Arduino Mega    |      |           AWS Cloud               |
| 2560            |----->|                                   |
+-----------------+      |   +-------+                       |
                         |   |  SQS  |                       |
                         |   +-------+                       |
                         |       |                           |
                         |   +-------+                       |
                         |   |Lambda |                       |
                         |   +-------+                       |
                         |       ├─────────────┐             |
                         |       |             |             |
                         |   +-------+     +-----------+     |
                         |   |  SNS  |     | DynamoDB  |     |
                         |   +-------+     +-----------+     |
                         +-----------------------------------+
                                   |
                         +---------+
                         | E-mail  |
                         +---------+
```

### Creation and Configuration of AWS Resources

1. Created a **Standard SQS queue** to **receive metrics collected by the sensor** related to water level
2. Created an **SNS topic and email subscription** to **receive notifications** about changes in water measurements
3. Created a **DynamoDB table** to **store collection records**, with the primary key being the date and time of collection
4. Created the **Lambda function** and added the following permissions to its execution role policy:

```
"sqs:DeleteMessage",
"sqs:Get*",
"sqs:ReceiveMessage",
"sns:Publish",
"dynamodb:BatchWriteItem",
"dynamodb:PutItem"
```

5. Added the **SQS queue** to serve as a **trigger for the created Lambda function**
6. Created a **user** with the necessary permissions to **send messages to SQS**

## References
* [Como conectar o Sensor Ultrassônico HC-SR04 ao Arduino](https://www.makerhero.com/blog/sensor-ultrassonico-hc-sr04-ao-arduino/)
* [Como Conectar o Arduino com o Python](https://blog.eletrogate.com/como-conectar-o-arduino-com-o-python/)
* [Integrating Lambda to send and receive messages from SQS and SNS: Part 1](https://medium.com/@aaloktrivedi/integrating-lambda-to-send-and-receive-messages-from-sqs-and-sns-part-1-5b359f0f1678)
* [Integrating Lambda to send and receive messages from SQS and SNS: Part 2](https://medium.com/@aaloktrivedi/integrating-lambda-to-send-and-receive-messages-from-sqs-and-sns-part-2-6595133dd1a3)
