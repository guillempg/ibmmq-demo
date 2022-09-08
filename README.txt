## Instructions to get IBM MQ from this web:
## https://developer.ibm.com/tutorials/mq-connect-app-queue-manager-containers/
##
## Related GitHub IBM MQ repository: https://github.com/ibm-messaging/mq-container

## IBM has a tutorial on IBM MQ here: 
## https://developer.ibm.com/learningpaths/ibm-mq-badge

## Pull the latest IBM MQ docker image from IBM registry (dockerhub only has up to version 9.2.4, but latest is 9.3):
docker pull icr.io/ibm-messaging/mq:latest

## We don't want to persist it, as its main use will be for testing, so when the container is stopped all data is lost:
docker run --env LICENSE=accept --env MQ_QMGR_NAME=QM1 --publish 1414:1414 --publish 9443:9443 --detach --env MQ_APP_PASSWORD=passw0rd --name QM1 icr.io/ibm-messaging/mq:latest

## If we want to persist the data, then we need to create a docker volume:
docker volume create qm1data

## and mount that volume into the container with this command instead:
docker run --env LICENSE=accept --env MQ_QMGR_NAME=QM1 --volume qm1data:/mnt/mqm --publish 1414:1414 --publish 9443:9443 --detach --env MQ_APP_PASSWORD=passw0rd --name QM1 icr.io/ibm-messaging/mq:latest


## Access the web console for IBM MQ running in the docker container
##
## Username: admin Password: passw0rd
##
https://localhost:9443/ibmmq/console


## You can also clone this repository and test sending an consuming messages to the default created queues in that docker repository


## compile the source code with:
javac -cp ./com.ibm.mq.allclient-9.3.0.0.jar:./javax.jms-api-2.0.1.jar:./json-20220320.jar com/ibm/mq/samples/jms/JmsPut.java
javac -cp ./com.ibm.mq.allclient-9.3.0.0.jar:./javax.jms-api-2.0.1.jar:./json-20220320.jar com/ibm/mq/samples/jms/JmsGet.java
javac -cp ./com.ibm.mq.allclient-9.3.0.0.jar:./javax.jms-api-2.0.1.jar:./json-20220320.jar com/ibm/mq/samples/jms/JmsPutGet.java

## put 10 messages in the queue "DEV.QUEUE.1" of queue manager "QM1":
for i in {1..10}; do java -Djavax.net.ssl.trustStore=clientTruststore.p12 -Djavax.net.ssl.trustStorePassword=passw0rd -cp ./com.ibm.mq.allclient-9.3.0.0.jar:./javax.jms-api-2.0.1.jar:./json-20220320.jar:. com.ibm.mq.samples.jms.JmsPut; done;

## consume each message by running this:
java -Djavax.net.ssl.trustStore=clientTruststore.p12 -Djavax.net.ssl.trustStorePassword=passw0rd -cp ./com.ibm.mq.allclient-9.3.0.0.jar:./javax.jms-api-2.0.1.jar:./json-20220320.jar:. com.ibm.mq.samples.jms.JmsGet


