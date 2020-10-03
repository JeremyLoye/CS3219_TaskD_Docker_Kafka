# CS3219 Task D Docker Kafka

**Task:**

Create a 3 node Apache Kafka cluster using Docker that demonstrates the Pub-Sub messaging, with a Zookeeper ensemble created to manage the Kafka cluster.

### Instructions on how to set up and run the Kafka cluster:

<u>Pre-Requisite</u>: Have 3 servers/virtual machines running. Platforms such as Google Cloud Platform or Amazon Web Services allow for the creation of Virtual Machines.

<u>Set-up</u>: For each of the server instances, the dependencies `docker`, `docker-compose` and `kafkacat` need to be installed. For Linux environments, run the command `sudo apt-get install docker docker-compose kafkacat`.

#### Creation of Kafka Clusters

1. Fork and clone the Github Repository onto your local machine.  

2. Change the `{ipaddress}` for to the IP Address of each of your 3 servers in the `docker-compose.yml` file.

3. Copy the `docker-compose.yml` file into each of the 3 servers' directories.

4. Start up the Zookeeper and Kafka instances on each of your 3 servers by running the following commands:

   ```markdown
   # For Server 1
   docker-compose up -d zookeeper1 kafka1
   # For Server 2
   docker-compose up -d zookeeper2 kafka2
   # For Server 3
   docker-compose up -d zookeeper3 kafka3
   ```

5. Once the instances are started, you can use `docker ps` to check that both the zookeeper and kafka images are running on each server.

#### Creation of Topics and Publishing of Messages

1. Using any of the servers, create a topic on Kafka with the following command: 

   ```markdown
   --rm ches/kafka kafka-topics.sh \
   --create \
   --topic {topicname} \
   --replication-factor 3 \
   --partitions 1 \
   --zookeeper {ipaddress}:2181
   ```

   where `{topicname}` is a topic name of your choosing and `ipaddress` is the IP address of any of the 3 servers. For successful management of the failure of the master node, a replication factor of more than 1 is required.

2. Once the topic is successfully created, publishers and consumers can be run to send start sending messages. Publishers and consumers can be created on the same server or across the 3 servers with the following command:

   ```markdown
   # Creating a publisher
   kafkacat -P -b {ipaddress}:9092 -t {topicname}
   # Creating a consumer
   kafkacat -C -b {ipaddress}:9092 -t {topicname}
   ```

   where `{topicname}` is the name of the topic previously created and `{ipaddress}` is the IP address of any of the 3 servers running. 

3. On the publisher terminal, you can type messages and you will see it appear on the consumer terminal. ![PubSub%20Messages](./images/PubSub%20Messages.png)

#### Failure management of master node in the cluster

1. Running the command `kafka cat -L -b {ipaddress}:9092` will allow you to see the metadata for all topics, including its brokers and the 'leader' node of the topic.![Metadata%20with%203%20nodes](./images/Metadata%20with%203%20nodes.png)
2. To test for the successful handling of master node failure, take node of the leader node for the topic and run `docker stop {containername}` where `{containername}` is the name of the container running the node.
3. Running the command  `kafka cat -L -b {ipaddress}:9092` again will show that one of the other 2 nodes has taken over as the leader node and you can continue to publish and consume messages in the kafka topic. [Take note: Only the IP address of either of the 2 remaining running Kafka instances can be used for commands] ![Metadata%20with%202%20nodes](./images/Metadata%20with%202%20nodes.png)
