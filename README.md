--------------------- Producer ------------------------
package kafka;

import java.util.Properties;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.log4j.Logger;

public class ProducerApp {
	
	final static Logger logger = Logger.getLogger(ProducerApp.class);

	public static void main(String[] args) {
		long start;
		long end;
		Properties props = new Properties();
		
		props.put("bootstrap.servers", "localhost:9092");
		props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
		props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
		
		KafkaProducer<String, String> myProducer = new KafkaProducer<String, String>(props);
		
		logger.debug("################ Producer App Stats Starts #############");
		logger.debug("Starting Loop");
		start = System.currentTimeMillis();
		logger.debug("System Time in Milliseconds: "+start);
		
		//Message Sending loop
		try{
			for(int i=0; i<10000; i++){
				myProducer.send(new ProducerRecord<String, String>("TestTopic", "Message No. "+i));
			}
			
			logger.debug("Loop Ended");
			end = System.currentTimeMillis();
			logger.debug("System Time in Milliseconds: "+end);
			logger.debug("Time required for publishing was "+Stats.getstats(start, end)+" milliseconds");
			logger.debug("############### Producer App Stats Ends ###################");
			
		}catch(Exception e){
			logger.error("Here is the error: "+e);
			
		}finally{
			myProducer.close();
		}

	}

}
-------------------------------------- Async Producer -------------------------------
package kafka;

import java.util.Properties;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.log4j.Logger;

public class AsyncProducerApp {
	
	final static Logger logger = Logger.getLogger(SyncProducerApp.class);

	public static void main(String[] args) {
		long start;
		long end;
		Properties props = new Properties();
		
		props.put("bootstrap.servers", "localhost:9092");
		props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
		props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
		
		KafkaProducer<String, String> myProducer = new KafkaProducer<String, String>(props);
		
		logger.debug("**************** Async Producer App Stats Starts ****************");
		logger.debug("Starting Loop");
		start = System.currentTimeMillis();
		logger.debug("System Time in Milliseconds: "+start);
		
		//Messages Sending loop
		try{
			for(int i=0; i<10000; i++){
				myProducer.send(new ProducerRecord<String, String>("TestTopic", "Message No. "+i), new CallbackImplementer());
			}
			
		logger.debug("Loop Ended");
		end = System.currentTimeMillis();
		logger.debug("System Time in Milliseconds: "+end);
		logger.debug("Time required for publishing was "+Stats.getstats(start, end)+" milliseconds");
		logger.debug("**************** Async Producer App Stats Ends ****************");
		
			
		}catch(Exception e){
			logger.error("Here is the error: "+e);
			
		}finally{
			myProducer.close();
		}

	}

}

-------------------------------------- Sync Producer ----------------------------------
package kafka;

import java.util.Properties;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.log4j.Logger;

public class SyncProducerApp {
	
	final static Logger logger = Logger.getLogger(SyncProducerApp.class);

	public static void main(String[] args) {
		long start;
		long end;
		Properties props = new Properties();
		
		props.put("bootstrap.servers", "localhost:9092");
		props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
		props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
		
		KafkaProducer<String, String> myProducer = new KafkaProducer<String, String>(props);
		
		logger.debug("=============== Sync Producer App Stats Starts ================");
		logger.debug("Starting Loop");
		start = System.currentTimeMillis();
		logger.debug("System Time in Milliseconds: "+start);
		
		//Message Sending loop
		try{
			for(int i=0; i<10000; i++){
				myProducer.send(new ProducerRecord<String, String>("TestTopic", "Message No. "+i)).get();
			}
			
		logger.debug("Loop Ended");
		end = System.currentTimeMillis();
		logger.debug("System Time in Milliseconds: "+end);
		logger.debug("Time required for publishing was "+Stats.getstats(start, end)+" milliseconds");
		logger.debug("=============== Sync Producer App Stats Ends================");
		
			
		}catch(Exception e){
			logger.error("Here is the error: "+e);
			
		}finally{
			myProducer.close();
		}

	}

}

---------------------------------- CallbackImplementer -------------------------------
package kafka;

import org.apache.kafka.clients.producer.Callback;
import org.apache.kafka.clients.producer.RecordMetadata;
import org.apache.log4j.Logger;

public class CallbackImplementer implements Callback {
	final static Logger logger = Logger.getLogger(CallbackImplementer.class);

	public void onCompletion(RecordMetadata rm, Exception ex) {
		if(ex != null) logger.error(ex);
	}

}

--------------------------- log4j.properties ------------------------
# Root logger option
log4j.rootLogger=DEBUG, stdout, file

# Redirect log messages to console
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

# Redirect log messages to a log file, support file rolling.
log4j.appender.file=org.apache.log4j.RollingFileAppender
log4j.appender.file.File=D:\\Workspaces\\KafkaSample\\kafka\\Logs\\Kafka.logs
log4j.appender.file.MaxFileSize=10MB
log4j.appender.file.MaxBackupIndex=10
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n



---------------------------------- pom.xml --------------------------
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.kaf</groupId>
  <artifactId>kafka</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>Sample Kafka </name>
  <description>Sample Kafka project</description>
  
  <dependencies>
  	<dependency>
  		<groupId>org.apache.kafka</groupId>
  		<artifactId>kafka-clients</artifactId>
  		<version>0.11.0.1</version>
  	</dependency>
  	<dependency>
  		<groupId>log4j</groupId>
  		<artifactId>log4j</artifactId>
  		<version>1.2.17</version>
  	</dependency>
  </dependencies>
</project>
