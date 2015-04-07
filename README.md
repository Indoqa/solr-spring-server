# Indoqa Solr Spring Server

This project offers a Spring based implementation of a FactoryBean for communicating with Apache Solr servers. 

The SolrServerFactory allows to communicate with Solr either embedded, via http or Apache ZooKeeper for SolrCloud installations.

The desired behavior is configured with the supplied url:

* file:// - uses the EmbeddedSolrServer
* http:// - uses the HttpSolrServer
* cloud:// - uses the CloudSolrServer


## Installation

### Requirements

  * Apache Solr 4.10+
  * Spring Beans 3.1+
  * Java 6+
  
### Build

  * Download the latest release via maven

```xml

    <dependency>
      <groupId>com.indoqa.solr</groupId>
      <artifactId>spring-server</artifactId>
    </dependency>
    
```

  * Download source
  * run "maven clean install"
  
## Configuration

### Initialize the SolrServerFactory for tests with this snippet
```java

    SolrServerFactory solrServerFactory = new SolrServerFactory();
    solrServerFactory.setUrl("file:///tmp/solr-spring-server/embedded-test-core");
    solrServerFactory.setEmbeddedSolrConfigurationDir("./src/test/resources/solr/test-core");
    solrServerFactory.initialize();

    SolrServer solrServer = solrServerFactory.getObject();

    QueryResponse response = solrServer.query(new SolrQuery("*:*"));

    ...

    solrServer.shutdown();
    solrServerFactory.destroy();

```
The url can either be a relative or absolute directory, the embeddedSolrConfigurationDir must include the usual Solr configuration files:

* schema.xml
* solrconfig.xml

### To communicate with a single Solr server use this snippet

```java

    SolrServerFactory solrServerFactory = new SolrServerFactory();
    solrServerFactory.setUrl("http://localhost:8983/test-core");
    solrServerFactory.initialize();
```

### To communicate with a SolrCloud cluster use this snippet

 ```java

    SolrServerFactory solrServerFactory = new SolrServerFactory();
    solrServerFactory.setUrl("cloud://zkHost1:2181,zkHost2:2182?collection=test-collection");
    solrServerFactory.initialize();

```
The syntax uses zkHost1:2181,zkHost2:2182 for the ZooKeeper instances in your ZooKeeper ensemble, followed by the collection to be used. 
This ensures that the communication for update requests will be established against the leader of the collection to minimize communication overhead.

### Solr Spring server integration based on xml configuration

 ```xml

    <bean name="solrServerFactory" class="com.indoqa.solr.server.factory.SolrServerFactory">
      <property name="url" value="file://./target/solr/embedded-test-core" />
      <property name="embeddedSolrConfigurationDir" value="./src/test/resources/solr/test-core" />
    </bean>

 ```
