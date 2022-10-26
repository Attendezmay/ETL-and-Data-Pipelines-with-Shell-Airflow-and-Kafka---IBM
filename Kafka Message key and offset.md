
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
    <h1>Kafka Message key and offset</h1>
    <p>Estimated time needed: <strong>40</strong> minutes</p>
    <h2>Objectives</h2>
    <p>After completing this lab, you will be able to:</p>
    <ul>
      <li>
        <p>Use message keys to keep message streams sorted in their original publication state/order</p>
      </li>
      <li>
        <p>Use consumer offset to control and track message sequential positions in topic partitions</p>
      </li>
    </ul>
    <h1>Important notice about this lab environment</h1>
    <p>
      Please be aware that sessions for this lab environment are not persistent.
      A new environment is created for you every time you connect to this lab.
      Any data you may have saved in an earlier session will get lost.
      To avoid losing your data, please plan to complete these labs in a single session.
    </p>
    <h1>Lab environment setup and preparation</h1>
    <h3>Download and Extract Apache Kafka</h3>
    <p>Open a new terminal, by clicking on the menu bar and selecting <strong>Terminal</strong>-><strong>New Terminal</strong>, as shown in the following image.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/new-terminal.png" alt="Screenshot highlighting New Terminal in menu">
    </p>
    <p>This will open a new terminal at the bottom of the screen.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/terminal_bottom_screen.png" alt="Screenshot highlighting new terminal at bottom of screen">
    </p>
    <p>
      Run the following commands on the newly opened terminal. (You can copy the code by clicking on the little copy button
      on the lower-right of the following codeblock and then paste it, wherever you wish.)
    </p>
    <p>Download Kafka, by running the following command:</p>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">wget</span> https://archive.apache.org/dist/kafka/<span class="hljs-number">2</span>.<span class="hljs-number">8</span>.<span class="hljs-number">0</span>/kafka_<span class="hljs-number">2</span>.<span class="hljs-number">12</span>-<span class="hljs-number">2</span>.<span class="hljs-number">8</span>.<span class="hljs-number">0</span>.tgz
</code></pre>
    <p></p>
    <p>Extract kafka from the zip file by running the following command:</p>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">tar</span> -xzf kafka_<span class="hljs-number">2</span>.<span class="hljs-number">12</span>-<span class="hljs-number">2</span>.<span class="hljs-number">8</span>.<span class="hljs-number">0</span>.tgz
</code></pre>
    <p></p>
    <p>This creates a new directory 'kafka_2.12-2.8.0' in the current directory.</p>
    <h1>Start ZooKeeper</h1>
    <p>ZooKeeper is required for Kafka to work. Start the ZooKeeper server.</p>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">cd</span> kafka_<span class="hljs-number">2</span>.<span class="hljs-number">12</span>-<span class="hljs-number">2</span>.<span class="hljs-number">8</span>.<span class="hljs-number">0</span>
<span class="hljs-attribute">bin</span>/zookeeper-server-start.sh config/zookeeper.properties
</code></pre>
    <p></p>
    <p>When ZooKeeper starts you should see an output like this:</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/zookeeper1.png" alt="Screenshot of output">
    </p>
    <p>You can be sure it has started when you see an output like this:</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/zookeeper2.png" alt="Screenshot of output">
    </p>
    <p>
      ZooKeeper, as of this version, is required for Kafka to work.
      ZooKeeper is responsible for the overall management of a Kafka cluster.
      It monitors the Kafka brokers and notifies Kafka if any broker or partition goes down,
      or if a new broker or partition comes up.
    </p>
    <h1>Start Apache Kafka Server</h1>
    <p>Start a new terminal.</p>
    <p>Run the following command to start Kafka server</p>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">cd</span> kafka_<span class="hljs-number">2</span>.<span class="hljs-number">12</span>-<span class="hljs-number">2</span>.<span class="hljs-number">8</span>.<span class="hljs-number">0</span>
<span class="hljs-attribute">bin</span>/kafka-server-start.sh config/server.properties
</code></pre>
    <p></p>
    <p>When Kafka starts, you should see an output like this:</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/kafka1.png" alt="">
    </p>
    <p>You can be sure it has started when you see an output like this:</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/kafka2.png" alt="">
    </p>
    <h1>Create a topic and producer for processing bank ATM transactions</h1>
    <p>
      Next, we will be creating a <code>bankbranch</code> topic to process the messages that come
      from the ATM machines of bank branches.
    </p>
    <p>
      Suppose the messages come from the ATM in the form of a simple JSON object,
      including an ATM id and a transaction id like the following example:
    </p>
    <pre><code class="hljs language-json">{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">1</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">100</span>}
</code></pre>
    <p>To process the ATM messages, let's first create a new topic called <code>bankbranch</code>.</p>
    <ul>
      <li>Start a new terminal and go to the extracted <code>Kafka</code> folder:</li>
    </ul>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">cd</span> kafka_<span class="hljs-number">2</span>.<span class="hljs-number">12</span>-<span class="hljs-number">2</span>.<span class="hljs-number">8</span>.<span class="hljs-number">0</span>
</code></pre>
    <p></p>
    <ul>
      <li>
        Create a new topic using the <code>--topic</code> argument with the name <code>bankbranch</code>. In order to simplify the topic configuration and better
        explain how message key and consumer offset work, here we specify <code>--partitions 2</code> argument to create two partitions for this topic.
        You may try other <code>partitions</code> settings for this topic if you are interested in comparing the difference.
      </li>
    </ul>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">create</span> --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span>  --<span class="hljs-comment">partitions</span> <span class="hljs-comment">2</span>
</code></pre>
    <p></p>
    <p>Now let's list all the topics to see if <code>bankbranch</code> has been created successfully.</p>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">list</span>
</code></pre>
    <p></p>
    <p>We can also use the <code>--describe</code> command to check the details of the topic <code>bankbranch</code></p>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">describe</span> --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span>
</code></pre>
    <p></p>
    <p>and you can see <code>bankbranch</code> has two partitions <code>Partition 0</code> and <code>Partition 1</code>. If no message keys are specified, messages will be published to these two partitions in an alternating sequence, like this:</p>
    <p><code>Partition 0</code> -> <code>Partition 1</code> -> <code>Partition 0</code> -> <code>Partition 1</code> ...</p>
    <p>Next, we can create a producer to publish some ATM transaction messages.</p>
    <ul>
      <li>Stay in the same terminal window with the topic details, then create a producer for topic <code>bankbranch</code></li>
    </ul>
    <pre><code class="hljs language-mipsasm"><span class="hljs-keyword">bin/kafka-console-producer.sh </span>--<span class="hljs-keyword">bootstrap-server </span>localhost:<span class="hljs-number">9092</span> --topic <span class="hljs-keyword">bankbranch </span>

</code></pre>
    <p></p>
    <p>To produce the messages, look for the the <code>></code> icon, and copy and paste the following ATM messages after it:</p>
    <pre><code class="hljs language-json">{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">1</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">100</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json">{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">1</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">101</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json">{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">2</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">200</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json">{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">1</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">102</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json">{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">2</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">201</span>}
</code></pre>
    <p>Then, let's create a consumer in a new terminal window to consume these 5 new messages.</p>
    <ul>
      <li>Start a new terminal and go to the extracted <code>Kafka</code> folder:</li>
    </ul>
    <pre><code class="hljs language-apache"><span class="hljs-attribute">cd</span> kafka_<span class="hljs-number">2</span>.<span class="hljs-number">12</span>-<span class="hljs-number">2</span>.<span class="hljs-number">8</span>.<span class="hljs-number">0</span>
</code></pre>
    <p></p>
    <ul>
      <li>Then start a new consumer to subscribe to the <code>bankbranch</code> topic:</li>
    </ul>
    <pre><code class="hljs language-mipsasm"><span class="hljs-keyword">bin/kafka-console-consumer.sh </span>--<span class="hljs-keyword">bootstrap-server </span>localhost:<span class="hljs-number">9092</span> --topic <span class="hljs-keyword">bankbranch </span>--from-<span class="hljs-keyword">beginning
</span></code></pre>
    <p></p>
    <p>Then, you should see the 5 new messages we just published, but very likely, they are not consumed in the same order as they were published. Normally, you need to keep the consumed messages sorted in their original published order, especially for critical use cases such as financial transactions.</p>
    <h1>Produce and consume with message keys</h1>
    <p>In this step, you will be using message keys to ensure that messages with the same key will be consumed in the same order as they were published. In the backend, messages with the same key will be published into the same partition and will always be consumed by the same consumer. As such, the original publication order is kept in the consumer side.</p>
    <p>ASt this point, you should have the following four terminals open in Cloud IDE:</p>
    <ul>
      <li>Zookeeper terminal</li>
      <li>Kafka Server terminal</li>
      <li>Producer terminal</li>
      <li>Consumer terminal</li>
    </ul>
    <p>In the next steps, you will be frequently switching among these terminals.</p>
    <ul>
      <li>First, go to the consumer terminal and stop the consumer using <code>Ctrl</code> + <code>C</code> (Windows)</li>
    </ul>
    <p>or <code>Command</code> + <code>.</code> (Mac).</p>
    <ul>
      <li>Then, switch to the Producer terminal and stop the previous producer.</li>
    </ul>
    <p>Ok, we can now start a new producer and consumer, this time using message keys. You can start a new producer with the following message key commands:</p>
    <ul>
      <li><code>--property parse.key=true</code> to make the producer parse message keys</li>
      <li><code>--property key.separator=:</code> define the key separator to be the <code>:</code> character,</li>
    </ul>
    <p>
      so our message with key now looks like the following key-value pair example:
      - <code>1:{"atmid": 1, "transid": 102}</code>.
      Here the message key is <code>1</code>, which also corresponds to the ATM id, and the value is the transaction JSON object, <code>{"atmid": 1, "transid": 102}</code>.
    </p>
    <ul>
      <li>Start a new producer with message key enabled:</li>
    </ul>
    <pre><code class="hljs language-qml">bin/kafka-<span class="hljs-built_in">console</span>-producer.sh --bootstrap-server <span class="hljs-attribute">localhost</span>:<span class="hljs-number">9092</span> --topic bankbranch --<span class="hljs-keyword">property</span><span class="hljs-string"> parse.key</span>=<span class="hljs-literal">true</span> --<span class="hljs-keyword">property</span><span class="hljs-string"> key.separator</span>=:
</code></pre>
    <p></p>
    <ul>
      <li>Once you see <code>></code> symbol, you can start to produce the following messages, where you define each key to match the ATM id for each message:</li>
    </ul>
    <pre><code class="hljs language-json"><span class="hljs-number">1</span>:{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">1</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">102</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json"><span class="hljs-number">1</span>:{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">1</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">103</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json"><span class="hljs-number">2</span>:{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">2</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">202</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json"><span class="hljs-number">2</span>:{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">2</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">203</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json"><span class="hljs-number">1</span>:{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">1</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">104</span>}
</code></pre>
    <p></p>
    <ul>
      <li>Next, switch to the consumer terminal again, and start a new consumer with</li>
    </ul>
    <p><code>--property print.key=true --property key.separator=:</code> arguments to print the keys</p>
    <pre><code class="hljs language-jboss-cli">bin/kafka-console-consumer.sh <span class="hljs-params">--bootstrap-server</span> localhost<span class="hljs-function">:9092</span> <span class="hljs-params">--topic</span> bankbranch <span class="hljs-params">--from-beginning</span> <span class="hljs-params">--property</span> print.key=<span class="hljs-literal">true</span> <span class="hljs-params">--property</span> key.separator=:
</code></pre>
    <p></p>
    <p>Now, you should see that messages that have the same key are being consumed in the same order (e.g., <code>trans102 -> trans103 -> trans104</code>) as they were published.</p>
    <p>
      This is because each topic partition maintains its own message queue, and new messages are enqueued (appended to the end of the queue)
      as they get published to the partition. Once consumed, the earliest messages will be dequeued and nno longer be available for consumption.
    </p>
    <p>
      Recall that with two partitions and no message keys specified, the transaction messages were published to the two partitions
      in rotation:
    </p>
    <ul>
      <li>Partition 0: <code>[{"atmid": 1, "transid": 102}, {"atmid": 2, "transid": 202}, {"atmid": 1, "transid": 104}]</code></li>
      <li>Partition 1: <code>[{"atmid": 1, "transid": 103}, {"atmid": 2, "transid": 203}]</code></li>
    </ul>
    <p>
      As you can see, the transaction messages from <code>atm1</code> and <code>atm2</code> got scattered across both partitions. It would be difficult
      to unravel this and consume messages from one ATM with the same order as they were published.
    </p>
    <p>However, with message key specified as the <code>atmid</code> value, the messages from the two ATMs will look like the following:</p>
    <ul>
      <li>Partition 0: <code>[{"atmid": 1, "transid": 102}, {"atmid": 1, "transid": 103}, {"atmid": 1, "transid": 104}]</code></li>
      <li>Partition 1: <code>[{"atmid": 2, "transid": 202}, {"atmid": 2, "transid": 203}]</code></li>
    </ul>
    <p>Messages with the same key will always be published to the same partition, so that their published order will be preserved within the message queue of each partition.</p>
    <p>As such, we can keep the states or orders of the transactions for each ATM.</p>
    <h1>Consumer Offset</h1>
    <p>
      Topic partitions keeps published messages in a sequence, like a list.
      Message offset indicates a message's position in the sequence. For example,
      the offset of an empty Partition 0 of <code>bankbranch</code> is <code>0</code>, and if you publish the first message
      to the partition, its offset will be <code>1</code>.
    </p>
    <p>By using offsets in the consumer, you can specify the starting position for message consumption, such as from the beginning to retrieve all messages, or from some later point to retrieve only the latest messages.</p>
    <h2>Consumer Group</h2>
    <p>
      In addition, we normally group related consumers together as a consumer group.
      For example, we may want to create a consumer for each ATM in the bank and manage all ATM related consumers
      together in a group.
    </p>
    <p>So let's see how to create a consumer group, which is actually very easy with the <code>--group</code> argument.</p>
    <ul>
      <li>
        <p>In the consumer terminal, stop the previous consumer if it is still running.</p>
      </li>
      <li>
        <p>Run the following command to create a new consumer within a consumer group called <code>atm-app</code>:</p>
      </li>
    </ul>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span> --<span class="hljs-comment">group</span> <span class="hljs-comment">atm</span><span class="hljs-literal">-</span><span class="hljs-comment">app</span>
</code></pre>
    <p></p>
    <p>
      After the consumer within the <code>atm-app</code> consumer group is started, you should not expect any messages to be consumed.
      This is because the offsets for both partitions have already reached to the end.
      In other words, all messages have already been consumed, and therefore dequeued, by previous consumers.
    </p>
    <p>You can verify that by checking consumer group details.</p>
    <ul>
      <li>
        <p>Stop the consumer.</p>
      </li>
      <li>
        <p>Show the details of the consumer group <code>atm-app</code>:</p>
      </li>
    </ul>
    <pre><code class="hljs language-stata">bin/kafka-consumer-groups.<span class="hljs-keyword">sh</span> --<span class="hljs-keyword">bootstrap</span>-server localhost:9092 --<span class="hljs-keyword">describe</span> --group atm-<span class="hljs-keyword">app</span>
</code></pre>
    <p></p>
    <p>
      Now you should see the offset information for the topic <code>bankbranch</code>:
      
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/consumer_details_lag0.png" alt="">
    </p>
    <p>
      Recall that we have published <code>10</code> messages in total, and we can see the <code>CURRENT-OFFSET</code> column of partition 1 is <code>6</code>
      and <code>CURRENT-OFFSET</code> of partition 0 is <code>4</code>, and they add up to 10 messages.
    </p>
    <p>
      The <code>LOG-END-OFFSET</code>column indicates the last offset or the end of the sequence, which is 6 for partition 1 and 4 for
      partition 0. Thus, both partitions have reached the end of their queues and no more messages are available for consumption.
    </p>
    <p>
      Meanwhile, you can check the <code>LAG</code> column which represents the count of unconsumed messages for each partition.
      Currently it is <code>0</code> for all partitions, as expected.
    </p>
    <p>Now, let's produce more messages and see how the offsets change.</p>
    <ul>
      <li>Switch to the previous producer terminal, and publish two more messages:</li>
    </ul>
    <pre><code class="hljs language-json"><span class="hljs-number">1</span>:{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">1</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">105</span>}
</code></pre>
    <p></p>
    <pre><code class="hljs language-json"><span class="hljs-number">2</span>:{<span class="hljs-attr">"atmid"</span>: <span class="hljs-number">2</span>, <span class="hljs-attr">"transid"</span>: <span class="hljs-number">204</span>}
</code></pre>
    <p></p>
    <p>and let's switch back to the consumer terminal and check the consumer group details again:</p>
    <pre><code class="hljs language-stata">bin/kafka-consumer-groups.<span class="hljs-keyword">sh</span> --<span class="hljs-keyword">bootstrap</span>-server localhost:9092 --<span class="hljs-keyword">describe</span> --group atm-<span class="hljs-keyword">app</span>
</code></pre>
    <p></p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Streaming/images/consumer_details_lag1.png" alt="">
    </p>
    <p>
      You should see that both offsets have been increased by 1, and the <code>LAG</code> columns for both partitions have become
      <code>1</code>. It means we have 1 new message for each partition to be consumed.
    </p>
    <ul>
      <li>Let's start the consumer again and see whether the two new messages will be consumed.</li>
    </ul>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span> --<span class="hljs-comment">group</span> <span class="hljs-comment">atm</span><span class="hljs-literal">-</span><span class="hljs-comment">app</span>
</code></pre>
    <p></p>
    <p>OK, now both partitions have reached the end once again. But what if you want to consume the messages again from the beginning?</p>
    <p>We can do that via resetting offset in the next step.</p>
    <h2>Reset offset</h2>
    <p>We can reset the index with the <code>--reset-offsets</code> argument.</p>
    <p>First let's try resetting the offset to the earliest position (beginning) using <code>--reset-offsets --to-earliest</code>.</p>
    <ul>
      <li>Stop the previous consumer if it is still running, and run the following command to reset the offset:</li>
    </ul>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-literal">-</span><span class="hljs-comment">groups</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span>  --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span> --<span class="hljs-comment">group</span> <span class="hljs-comment">atm</span><span class="hljs-literal">-</span><span class="hljs-comment">app</span> --<span class="hljs-comment">reset</span><span class="hljs-literal">-</span><span class="hljs-comment">offsets</span> --<span class="hljs-comment">to</span><span class="hljs-literal">-</span><span class="hljs-comment">earliest</span> --<span class="hljs-comment">execute</span>
</code></pre>
    <p></p>
    <p>Now the offsets have been set to 0 (the beginning).</p>
    <ul>
      <li>Start the consumer again:</li>
    </ul>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span> --<span class="hljs-comment">group</span> <span class="hljs-comment">atm</span><span class="hljs-literal">-</span><span class="hljs-comment">app</span>
</code></pre>
    <p></p>
    <p>You should see that all 12 messages are consumed and that all offsets have reached the partition ends again.</p>
    <p>
      In fact, you can reset the offset to any position. For example, let's reset the offset so that
      we only consume the last two messages.
    </p>
    <ul>
      <li>
        <p>Stop the previous consumer</p>
      </li>
      <li>
        <p>Shift the offset to left by 2 using <code>--reset-offsets --shift-by -2</code>:</p>
      </li>
    </ul>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-literal">-</span><span class="hljs-comment">groups</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span>  --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span> --<span class="hljs-comment">group</span> <span class="hljs-comment">atm</span><span class="hljs-literal">-</span><span class="hljs-comment">app</span> --<span class="hljs-comment">reset</span><span class="hljs-literal">-</span><span class="hljs-comment">offsets</span> --<span class="hljs-comment">shift</span><span class="hljs-literal">-</span><span class="hljs-comment">by</span> <span class="hljs-literal">-</span><span class="hljs-comment">2</span> --<span class="hljs-comment">execute</span>
</code></pre>
    <p></p>
    <ul>
      <li>If you run the consumer again, you should see that we consumed 4 messages, 2 for each partition:</li>
    </ul>
    <pre><code class="hljs language-brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> --<span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> --<span class="hljs-comment">topic</span> <span class="hljs-comment">bankbranch</span> --<span class="hljs-comment">group</span> <span class="hljs-comment">atm</span><span class="hljs-literal">-</span><span class="hljs-comment">app</span>
</code></pre>
    <p></p>
    <h1>Summary</h1>
    <p>
      In this lab, you have learned how to include message keys in publication to keep their message states/order.
      You have also learned how to reset the offset to control the message consumption starting point.
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
          <td>2021-10-27</td>
          <td>1.0</td>
          <td>Yan Luo</td>
          <td>Created initial version of the lab</td>
        </tr>
      </tbody>
    </table>
    <p>Copyright (c) 2021 IBM Corporation. All rights reserved.</p>
   
  </body>
</html>
