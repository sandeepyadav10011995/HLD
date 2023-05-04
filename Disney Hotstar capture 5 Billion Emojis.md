# How does Disney Hotstar capture 5 Billion Emojis during a tournament?
![image](https://user-images.githubusercontent.com/22426280/236129999-6634df01-c3f1-4e7c-b21b-d9cdb8b7ba35.png)

## Here is my understanding of how the system works by reading the Hotstar engineering blog.
1. Clients send emojis through standard HTTP requests. 
    1. You can think of Golang Service as a typical Web Server. 
    2. Golang is chosen because it supports concurrency well. Threads in GoLang are lightweight.

2. Since the write volume is very high, Kafka (message queue) is used as a buffer.

3. Emoji data are aggregated by a streaming processing service called Spark.   
    1. It aggregates data every 2 seconds, which is configurable. 
    2. There is a trade-off to be made based on the interval. 
    3. A shorter interval means emojis are delivered to other clients faster but it also means more computing resources are needed.

4. Aggregated data is written to another Kafka.

5. The PubSub consumers pull aggregated emoji data from Kafka.

6. Emojis are delivered to other clients in real-time through the PubSub infrastructure.

**The PubSub infrastructure is interesting. Hotstar considered the following protocols: Socketio, NATS, MQTT, and gRPC, and settled with MQTT.**

## Note -: A similar design is adopted by LinkedIn which streams a million likes/sec.


