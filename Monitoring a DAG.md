

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
    <h1>Hands-on Lab: Monitoring a DAG</h1>
    <p>Estimated time needed: <strong>20</strong> minutes</p>
    <h2>Objectives</h2>
    <p>After completing this lab you will be able to:</p>
    <ul>
      <li>Search for a DAG.</li>
      <li>Pause/Unpause a DAG.</li>
      <li>Get the Details of a DAG.</li>
      <li>Explore tree view of a DAG.</li>
      <li>Explore graph view of a DAG.</li>
      <li>Explore Calendar view of a DAG.</li>
      <li>Explore Task Duration view of a DAG.</li>
      <li>Explore Details view of a DAG.</li>
      <li>View the source code of a DAG.</li>
      <li>Delete a DAG.</li>
    </ul>
    <h1>About Skills Network Cloud IDE</h1>
    <p>Skills Network Cloud IDE (based on Theia and Docker) provides an environment for hands on labs for course and project related labs. Theia is an open source IDE (Integrated Development Environment), that can be run on desktop or on the cloud. to complete this lab, we will be using the Cloud IDE based on Theia running in a Docker container.</p>
    <h2>Important Notice about this lab environment</h2>
    <p>Please be aware that sessions for this lab environment are not persistent. A new environment is created for you every time you connect to this lab. Any data you may have saved in an earlier session will get lost. To avoid losing your data, please plan to complete these labs in a single session.</p>
    <h1>Exercise 1 - Getting the environment ready</h1>
    <p>Step 1.1. Open a new terminal by clicking on the menu bar and selecting <strong>Terminal</strong>-><strong>New Terminal</strong>, as shown in the image below.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/new-terminal.png" alt="Screenshot highlighting New Terminal in menu bar">
    </p>
    <p>This will open a new terminal at the bottom of the screen.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/terminal_bottom_screen.png" alt="Screenshot highlighting new terminal at bottom of screen">
    </p>
    <p>Run the commands below on the newly opened terminal. (You can copy the code by clicking on the little copy button on the bottom right of the codeblock below and then paste it, wherever you wish.)</p>
    <p>Start Apache Airflow in the lab environment.</p>
    <pre><code class="hljs language-ebnf"><span class="hljs-attribute">start_airflow</span>
</code></pre>
    <p></p>
    <p>Please be patient, it will take a few minutes for airflow to get started.</p>
    <p>When airflow starts successfully, you should see an output similar to the one below:</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/start_airflow.png" alt="Screenshot highlighting UI URL, Username, and Password">
    </p>
    <p>Step 1.2. Open the Airflow Web UI</p>
    <p>Copy the Web-UI URL and paste it on a new browser tab. Or your can click on the URL by holding the control key (Command key in case of a Mac).</p>
    <p>You should land at a page that looks like this:</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/airflow_webui.png" alt="Screenshot of Skills Network Airflow">
    </p>
    <h1>Exercise 2 - Submit a dummy DAG</h1>
    <p>For the purpose of monitoring, let's create a dummy DAG with three tasks.</p>
    <p>Task1 does nothing but sleep for 1 second.</p>
    <p>Task2 sleeps for 2 seconds.</p>
    <p>Task3 sleeps for 3 seconds.</p>
    <p>This DAG is scheduled to run every 1 minute.</p>
    <p>Step 2.1. Using Menu-><code>File</code>-><code>New File</code> create a new file named <code>dummy_dag.py</code>.</p>
    <p>Step 2.2. Copy and paste the code below into it and save the file.</p>
    <pre><code class="hljs language-routeros"><span class="hljs-comment"># import the libraries</span>

<span class="hljs-keyword">from</span> datetime import timedelta
<span class="hljs-comment"># The DAG object; we'll need this to instantiate a DAG</span>
<span class="hljs-keyword">from</span> airflow import DAG
<span class="hljs-comment"># Operators; we need this to write tasks!</span>
<span class="hljs-keyword">from</span> airflow.operators.bash_operator import BashOperator
<span class="hljs-comment"># This makes scheduling easy</span>
<span class="hljs-keyword">from</span> airflow.utils.dates import days_ago

<span class="hljs-comment">#defining DAG arguments</span>

<span class="hljs-comment"># You can override them on a per-task basis during operator initialization</span>
default_args = {
    <span class="hljs-string">'owner'</span>: <span class="hljs-string">'Ramesh Sannareddy'</span>,
    <span class="hljs-string">'start_date'</span>: days_ago(0),
    <span class="hljs-string">'email'</span>: [<span class="hljs-string">'ramesh@somemail.com'</span>],
    <span class="hljs-string">'email_on_failure'</span>: <span class="hljs-literal">False</span>,
    <span class="hljs-string">'email_on_retry'</span>: <span class="hljs-literal">False</span>,
    <span class="hljs-string">'retries'</span>: 1,
    <span class="hljs-string">'retry_delay'</span>: timedelta(<span class="hljs-attribute">minutes</span>=5),
}

<span class="hljs-comment"># defining the DAG</span>
dag = DAG(
    <span class="hljs-string">'dummy_dag'</span>,
    <span class="hljs-attribute">default_args</span>=default_args,
    <span class="hljs-attribute">description</span>=<span class="hljs-string">'My first DAG'</span>,
    <span class="hljs-attribute">schedule_interval</span>=timedelta(minutes=1),
)

<span class="hljs-comment"># define the tasks</span>

<span class="hljs-comment"># define the first task</span>

task1 = BashOperator(
    <span class="hljs-attribute">task_id</span>=<span class="hljs-string">'task1'</span>,
    <span class="hljs-attribute">bash_command</span>=<span class="hljs-string">'sleep 1'</span>,
    <span class="hljs-attribute">dag</span>=dag,
)


<span class="hljs-comment"># define the second task</span>
task2 = BashOperator(
    <span class="hljs-attribute">task_id</span>=<span class="hljs-string">'task2'</span>,
    <span class="hljs-attribute">bash_command</span>=<span class="hljs-string">'sleep 2'</span>,
    <span class="hljs-attribute">dag</span>=dag,
)

<span class="hljs-comment"># define the third task</span>
task3 = BashOperator(
    <span class="hljs-attribute">task_id</span>=<span class="hljs-string">'task3'</span>,
    <span class="hljs-attribute">bash_command</span>=<span class="hljs-string">'sleep 3'</span>,
    <span class="hljs-attribute">dag</span>=dag,
)

<span class="hljs-comment"># task pipeline</span>
task1 >> task2 >> task3

</code></pre>
    <p></p>
    <p>Submitting a DAG is as simple as copying the DAG python file into <code>dags</code> folder in the <code>AIRFLOW_HOME</code> directory.</p>
    <p>Step 2.3. Open a terminal and run the command below to submit the DAG that was created in the previous exercise.</p>
    <pre><code class="hljs language-powershell"><span class="hljs-built_in">cp</span> dummy_dag.py <span class="hljs-variable">$AIRFLOW_HOME</span>/dags
</code></pre>
    <p></p>
    <p>Step 2.4. Verify that our DAG actually got submitted.</p>
    <p>Run the command below to list out all the existing DAGs.</p>
    <pre><code class="hljs language-ebnf"><span class="hljs-attribute">airflow dags list</span>
</code></pre>
    <p></p>
    <p>Verify that <code>dummy_dag</code> is a part of the output.</p>
    <p>Step 2.5. Run the command below to list out all the tasks in <code>dummy_dag</code>.</p>
    <pre><code class="hljs language-ebnf"><span class="hljs-attribute">airflow tasks list dummy_dag</span>
</code></pre>
    <p></p>
    <p>You should see 3 tasks in the output.</p>
    <h1>Exercise 3 - Search for a DAG</h1>
    <p>In the Web-UI, identify the <code>Search DAGs</code> text box as shown in the image below.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/search_dags.png" alt="Screenshot highlighting Search DAGs textbox, dummy_dag typed in text box, and dummy-dag toggle button">
    </p>
    <p>Type <code>dummy_dag</code> in the text box and press enter.</p>
    <p>Note: It may take a couple of minutes for the dag to appear here. If you do not see your DAG, please give it a minute and try again.</p>
    <p>You should see the <code>dummy_dag</code> listed as seen in the image below:</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/search_dags2.png" alt="Screenshot highlighting the dummy_dag toggle button">
    </p>
    <h1>Exercise 4 - Pause/Unpause a DAG</h1>
    <p>Unpause the DAG using the Pause/Unpause button.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/pauseunpause1.png" alt="Screenshot highlighting the Pause/Unpause DAG button">
    </p>
    <p>You should see the status as shown in the image below after you unpause the DAG.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_listing.png" alt="Screenshot showing the status of the dummy_dag">
    </p>
    <p>You can see the following details in this view.</p>
    <ul>
      <li>Owner of the DAG</li>
      <li>How many times this DAG has run.</li>
      <li>Schedule of the DAG</li>
      <li>Last run time of the DAG</li>
      <li>Recent task status.</li>
    </ul>
    <h1>Exercise 5 - DAG - Detailed view</h1>
    <p>Click on the DAG name as shown in the image below to see the detailed view of the DAG.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_click.png" alt="Screenshot highlighting the dummy_dag tag name">
    </p>
    <p>You will land a page that looks like this.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_detailed_view.png" alt="Screenshot of the page for the dummy_dag">
    </p>
    <h1>Exercise 6 - Explore tree view of DAG</h1>
    <p>Click on the <code>Tree View</code> button to open the Tree view.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_tree_view.png" alt="Screenshot highlighting the Tree View button">
    </p>
    <p>Click on the <code>Auto Refresh</code> button to switch on the auto refresh feature.</p>
    <p>The tree view shows your DAG tasks in the form of a tree as seen in the image above.</p>
    <p>It also shows the DAG run and task run status as seen below.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_tree_view2.png" alt="Screenshot highlighting task run status">
    </p>
    <p>The circles in the image below represent a single DAG run and the color indicates the status of the DAG run. Place your mouse on any circle to see the details.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_status.png" alt="Screenshot showing details of DAG run">
    </p>
    <p>The squares in the image below represent a single task within a DAG run and the color indicates its status. Place your mouse on any square to see the task details.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/task_status.png" alt="Screenshot showing details of task">
    </p>
    <h1>Exercise 7 - Explore graph view of DAG</h1>
    <p>Click on the <code>Graph View</code> button to open the graph view.</p>
    <p>Click on the <code>Auto Refresh</code> button to switch on the auto refresh feature.</p>
    <p>The graph view shows the tasks in a form of a graph. With the auto refresh on, each task status is also indicated with the color code.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_graph_view.png" alt="Screenshot of tasks in graph form">
    </p>
    <h1>Exercise 8 - Calender view</h1>
    <p>The calender view gives you an overview of all the dates when this DAG was run along with its status as a color code.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_calendar_view.png" alt="Screenshot highlighting Calendar veiw button and the detailed information for a specific date">
    </p>
    <h1>Exercise 9 - Task Duration view</h1>
    <p>The Task Duration view gives you an overview of how much time each task took to execute, over a period of time.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_task_times.png" alt="Screenshot of overview for duration of each task">
    </p>
    <h1>Exercise 10 - Details view</h1>
    <p>The Details view give you all the details of the DAG as specified in the code of the DAG.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_details_view.png" alt="Screenshot of DAG details view">
    </p>
    <h1>Exercise 11 - Code view</h1>
    <p>The Code view lets you view the code of the DAG.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_code.png" alt="Screenshot of Code view">
    </p>
    <h1>Exercise 12 - Delete a DAG</h1>
    <p>To delete a DAG click on the delete button.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/delete_a_dag.png" alt="Screenshot highlighting delete button">
    </p>
    <p>You will get a confirmation pop up as shown in the image below. Click <code>OK</code> to delete the DAG.</p>
    <p>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Monitoring%20a%20DAG/images/dag_delete_confirm.png" alt="Screenshot of confirmation pop up">
    </p>
    <h1>Practice exercises</h1>
    <ol>
      <li>Problem:</li>
    </ol>
    <blockquote>
      <p><em>Unpause any existing DAG and monitor it.</em></p>
    </blockquote>
    <h2>Authors</h2>
    <p>Ramesh Sannareddy</p>
    <h3>Other Contributors</h3>
    <p>Rav Ahuja</p>
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
          <td>2021-07-05</td>
          <td>0.1</td>
          <td>Ramesh Sannareddy</td>
          <td>Created initial version of the lab</td>
        </tr>
      </tbody>
    </table>
    <p>Copyright (c) 2021 IBM Corporation. All rights reserved.</p>
    
  </body>
</html>
