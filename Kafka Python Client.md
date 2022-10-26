
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <link rel="stylesheet" href="https://unpkg.com/@highlightjs/cdn-assets@10.7.1/styles/default.min.css">
  </head>
  <body>
    <center>
      <img src="https://gitlab.com/ibm/skills-network/courses/placeholder101/-/raw/master/labs/module%201/images/IDSNlogo.png" width="300" alt="cognitiveclass.ai logo">
    </center>
    <h1>Kafka Python Client</h1>
    <p>Estimated time needed: <strong>30</strong> minutes</p>
    <h2>Objectives</h2>
    <p>After reading this article, you will be able to:</p>
    <ul>
      <li>
        <p>List the common Apache Kafka clients</p>
      </li>
      <li>
        <p>Use <code>kafka-python</code> to interact with Kafka server in Python</p>
      </li>
    </ul>
    <h1>Apache Kafka Clients</h1>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/kafka-clients.png" alt="">
    </p>
    <p>
      Kafka has a distributed client-server architecture. For the server side, Kafka is a cluster with many
      associated servers called broker, acting as the event broker to receive, store, and distribute events. All those brokers are managed by another distributed system called ZooKeeper to ensure all
      brokers work in an efficient and collaborative way.
    </p>
    <p>Kafka uses a TCP based network communication protocol to exchange data between clients and servers</p>
    <p>For the client side, Kafka provides different types of clients such as:</p>
    <ul>
      <li>Kafka CLI, which is a collection of shell scripts to communicate with a Kafka server</li>
      <li>Many high-level programming APIs such as Python, Java, and Scala</li>
      <li>REST APIs</li>
      <li>Specific 3rd party clients made by the Kafka community</li>
    </ul>
    <p>
      You can choose different clients based on your requirements. In this reading, we will be focusing on a Kafka Python client
      called <code>kafka-python</code>
    </p>
    <h1><code>kafka-python</code> package</h1>
    <p>
      <code>kafka-python</code> is a Python client for the Apache Kafka distributed stream processing system, which aims
      to provide similar functionalities as the main Kafka Java client.
    </p>
    <p>
      With <code>kafka-python</code>, you can easily interact with your Kafka server such as managing topics, publish, and consume
      messages in Python programming language.
    </p>
    <h2>Install <code>kafka-python</code></h2>
    <p>Install <code>kafka-python</code> is similar to other regular Python packages:</p>
    <pre><code class="hljs language-cmake">pip <span class="hljs-keyword">install</span> kafka-python
</code></pre>
    <p>Next, let's see the use cases of the main functions provided by the <code>kafka-python</code> package.</p>
    <h1><code>KafkaAdminClient</code> Class</h1>
    <p>
      The main purpose of <code>KafkaAdminClient</code> class is to enable fundamental administrative management operations
      on kafka server such as creating/deleting topic, retrieving, and updating topic configurations and so on.
    </p>
    <p>Let's check some concrete code examples:</p>
    <h2>Create a <code>KafkaAdminClient</code> object</h2>
    <p>To use <code>KafkaAdminClient</code>, we first need to define and create a <code>KafkaAdminClient</code> object:</p>
    <pre><code class="hljs language-python">admin_client = KafkaAdminClient(bootstrap_servers=<span class="hljs-string">"localhost:9092"</span>, client_id=<span class="hljs-string">'test'</span>)
</code></pre>
    <ul>
      <li>
        <code>bootstrap_servers="localhost:9092"</code> argument specifies the host/IP and port that the consumer should contact to
        bootstrap initial cluster metadata
      </li>
      <li><code>client_id</code> specifies an id of current admin client</li>
    </ul>
    <h2>Create new topics</h2>
    <p>Next, the most common usage of <code>admin_client</code> is managing topics such as creating and deleting topics.</p>
    <p>To create new topics, we first need to define an empty topic list:</p>
    <pre><code class="hljs language-python">topic_list = []
</code></pre>
    <p>
      Then we use the <code>NewTopic</code> class to create a topic with name equals <code>bankbranch</code>,
      partition nums equals to 2, and replication factor equals to 1.
    </p>
    <pre><code class="hljs language-python">new_topic = NewTopic(name=<span class="hljs-string">"bankbranch"</span>, num_partitions= <span class="hljs-number">2</span>, replication_factor=<span class="hljs-number">1</span>)
topic_list.append(new_topic)
</code></pre>
    <p>At last, we can use <code>create_topics(...)</code> method to create new topics:</p>
    <pre><code class="hljs language-python">admin_client.create_topics(new_topics=topic_list)
</code></pre>
    <p>Above <code>create</code> topic operation is equivalent to using <code>kafka-topics.sh --topic</code> in Kafka CLI client:</p>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">"kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">create</span> --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span>  --<span class="hljs-comment">partitions</span> <span class="hljs-comment">2</span> --<span class="hljs-comment">replication_factor</span> <span class="hljs-comment">1"</span>
</code></pre>
    <h2>Describe a topic</h2>
    <p>
      Once new topics are created, we can easily check its configuration details using <code>describe_configs()</code>
      method
    </p>
    <pre><code class="hljs language-python">configs = admin_client.describe_configs(
    config_resources=[ConfigResource(ConfigResourceType.TOPIC, <span class="hljs-string">"bankbranch"</span>)])
</code></pre>
    <p>Above <code>describe</code> topic operation is equivalent to using <code>kafka-topics.sh --describe</code> in Kafka CLI client:</p>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">describe</span> --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span>
</code></pre>
    <h1>KafkaProducer</h1>
    <p>Now we have a new <code>bankbranch</code> topic created, we can start produce messages to the topic.</p>
    <p>
      For <code>kafka-python</code>, we will use <code>KafkaProducer</code> class to produce messages.
      Since many real-world message values are in the format of JSON, we will show you how to publish JSON messages as an example.
    </p>
    <p>First, let's define and create a <code>KafkaProducer</code></p>
    <pre><code class="hljs language-reasonml">producer = <span class="hljs-constructor">KafkaProducer(<span class="hljs-params">value_serializer</span>=<span class="hljs-params">lambda</span> <span class="hljs-params">v</span>: <span class="hljs-params">json</span>.<span class="hljs-params">dumps</span>(<span class="hljs-params">v</span>)</span>.encode('utf-<span class="hljs-number">8</span>'))
</code></pre>
    <p>
      Since Kafka produces and consumes messages in raw bytes, we need to encode our JSON messages and serialize them
      into bytes.
    </p>
    <p>
      For the <code>value_serializer</code> argument, we define a lambda function to take a Python dict/list object and
      serialize it into bytes.
    </p>
    <p>Then, with the <code>KafkaProducer</code> created, we can use it to produce two ATM transaction messages in JSON format as follows:</p>
    <pre><code class="hljs language-python">producer.send(<span class="hljs-string">"bankbranch"</span>, {<span class="hljs-string">'atmid'</span>:<span class="hljs-number">1</span>, <span class="hljs-string">'transid'</span>:<span class="hljs-number">100</span>})
</code></pre>
    <pre><code class="hljs language-python">producer.send(<span class="hljs-string">"bankbranch"</span>, {<span class="hljs-string">'atmid'</span>:<span class="hljs-number">2</span>, <span class="hljs-string">'transid'</span>:<span class="hljs-number">101</span>})
</code></pre>
    <p>
      The first argument specifies the topic <code>bankbranch</code> to be sent, and the second argument represents
      the message value in a Python dict format and will be serialized into bytes.
    </p>
    <p>The above producing message operation is equivalent to using <code>kafka-console-producer.sh --topic</code> in Kafka CLI client:</p>
    <pre><code class="hljs language-mipsasm">kafka-console-producer.<span class="hljs-keyword">sh </span>--<span class="hljs-keyword">bootstrap-server </span>localhost:<span class="hljs-number">9092</span> --topic <span class="hljs-keyword">bankbranch
</span></code></pre>
    <h1>KafkaConsumer</h1>
    <p>
      In the previous step, we published two JSON messages. Now we can use the <code>KafkaConsumer</code> class to
      consume them.
    </p>
    <p>We just need to define and create a <code>KafkaConsumer</code> subscribing to the topic <code>bankbranch</code>:</p>
    <pre><code class="hljs language-python">consumer = KafkaConsumer(<span class="hljs-string">'bankbranch'</span>)
</code></pre>
    <p>
      Once the consumer is created, it will receive all available messages from the topic <code>bankbranch</code>. Then we
      can iterate and print them with the following code snippet:
    </p>
    <pre><code class="hljs language-python"><span class="hljs-keyword">for</span> msg <span class="hljs-keyword">in</span> consumer:
    <span class="hljs-built_in">print</span>(msg.value.decode(<span class="hljs-string">"utf-8"</span>))
</code></pre>
    <p>The above consuming message operation is equivalent to using <code>kafka-console-consumer.sh --topic</code> in Kafka CLI client:</p>
    <pre><code class="hljs language-mipsasm">kafka-console-consumer.<span class="hljs-keyword">sh </span>--<span class="hljs-keyword">bootstrap-server </span>localhost:<span class="hljs-number">9092</span> --topic <span class="hljs-keyword">bankbranch
</span></code></pre>
    <h1>Summary</h1>
    <p>
      In this reading, you have learned how to use <code>kafka-python</code> package to perform some main operations such as
      create or describe topic, produce and consume messages with a Kafka server.
    </p>
    <h2>Authors</h2>
    <p><a href="https://www.linkedin.com/in/yan-luo-96288783/?utm_medium=Exinfluencer&#x26;utm_source=Exinfluencer&#x26;utm_content=000026UJ&#x26;utm_term=10006555&#x26;utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDB0250ENSkillsNetwork26764073-2022-01-01" target="_blank" rel="external">Yan Luo</a></p>
    <h3>Other Contributors</h3>
    <h2>Change Log</h2>
    <table>
      <thead>
        <tr>
          <th>Date (YYYY-MM-DD)</th>
          <th>Version</th>
          <th>Changed By</th>
          <th>Change Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>2021-10-31</td>
          <td>1.0</td>
          <td>Yan Luo</td>
          <td>Created initial version of the lab</td>
        </tr>
      </tbody>
    </table>
    <p>Copyright (c) 2021 IBM Corporation. All rights reserved.</p>
   
  </body>
</html>
