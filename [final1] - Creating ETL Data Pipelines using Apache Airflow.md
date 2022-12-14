
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <link rel="stylesheet" href="https://unpkg.com/@highlightjs/cdn-assets@10.7.1/styles/default.min.css">
  </head>
  <body>
    <h1>Final Assignment (Part 1) - Creating ETL Data Pipelines using Apache Airflow</h1>
    <center>
      <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Final%20Assignment/IDSNlogo.png" width="300" alt="cognitiveclass.ai logo">
    </center>
    <p>Estimated time needed: <strong>90</strong> minutes.</p>
    <h1>About This SN Labs Cloud IDE</h1>
    <p>This Skills Network Labs Cloud IDE provides a hands-on environment for course and project related labs. It utilizes Theia, an open-source IDE (Integrated Development Environment) platform, that can be run on desktop or on the cloud. To complete this lab, we will be using the Cloud IDE based on Theia and Apache Airflow and MySQL database running in a Docker container. You will also need an instance of DB2 running in IBM Cloud.</p>
    <h2>Important Notice about this lab environment</h2>
    <p>Please be aware that sessions for this lab environment are not persistent. A new environment is created for you every time you connect to this lab. Any data you may have saved in an earlier session will get lost. To avoid losing your data, please plan to complete these labs in a single session.</p>
    <h1>Scenario</h1>
    <p>You are a data engineer at a data analytics consulting company. You have been assigned to a project that aims to de-congest the national highways by analyzing the road traffic data from different toll plazas. Each highway is operated by a different toll operator with a different IT setup that uses different file formats. Your job is to collect data available in different formats and consolidate it into a single file.</p>
    <h2>Objectives</h2>
    <p>In this assignment you will author an Apache Airflow DAG that will:</p>
    <ul>
      <li>Extract data from a csv file</li>
      <li>Extract data from a tsv file</li>
      <li>Extract data from a fixed width file</li>
      <li>Transform the data</li>
      <li>Load the transformed data into the staging area</li>
    </ul>
    <h1>Note - Screenshots</h1>
    <p>Throughout this lab you will be prompted to take screenshots and save them on your own device. These screenshots will need to be uploaded for peer review in the next section of the course. You can use various free screengrabbing tools or your operating system's shortcut keys (Alt + PrintScreen in Windows, for example) to capture the required screenshots.</p>
    <h1>Exercise 1 - Prepare the lab environment</h1>
    <p>Before you start the assignment:</p>
    <ul>
      <li>Start Apache Airflow.</li>
      <li>Download the dataset from the source to the destination mentioned below.</li>
    </ul>
    <blockquote>
      <p>Note: While downloading the file in the terminal use the <strong>sudo</strong> command before the command used to download the file.</p>
    </blockquote>
    <p>Source : <a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Final%20Assignment/tolldata.tgz" target="_blank" rel="external">https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Final%20Assignment/tolldata.tgz</a><br>Destination : /home/project/airflow/dags/finalassignment</p>
    <ul>
      <li>Create a directory structure for staging area as follows</li>
    </ul>
    <p>/home/project/airflow/dags/finalassignment/staging.</p>
    <ul>
      <li>Firstly enter the command <strong>cd airflow/dags</strong> in the terminal to change the directory to the <strong>/home/project/airflow/dags</strong> .</li>
    </ul>
    <pre><code class="hljs language-bash"><span class="hljs-built_in">cd</span> airflow/dags
</code></pre>
    <ul>
      <li>Step 2: Next enter the below given commands to create the directories <strong>finalassignment</strong> and <strong>staging</strong></li>
    </ul>
    <pre><code class="hljs language-dos">sudo <span class="hljs-built_in">mkdir</span> finalassignment

<span class="hljs-built_in">cd</span> finalassignment

sudo <span class="hljs-built_in">mkdir</span> staging

<span class="hljs-built_in">cd</span> staging
</code></pre>
    <h1>Exercise 2 - Create a DAG</h1>
    <h3>Task 1.1 - Define DAG arguments</h3>
    <p>Define the DAG arguments as per the following details:</p>
    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Value</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>owner</td>
          <td>&#x3C; You may use any dummy name></td>
        </tr>
        <tr>
          <td>start_date</td>
          <td>today</td>
        </tr>
        <tr>
          <td>email</td>
          <td>&#x3C; You may use any dummy email></td>
        </tr>
        <tr>
          <td>email_on_failure</td>
          <td>True</td>
        </tr>
        <tr>
          <td>email_on_retry</td>
          <td>True</td>
        </tr>
        <tr>
          <td>retries</td>
          <td>1</td>
        </tr>
        <tr>
          <td>retry_delay</td>
          <td>5 minutes</td>
        </tr>
      </tbody>
    </table>
    <p>Take a screenshot of the task code.</p>
    <p>Name the screenshot <code>dag_args.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.2 - Define the DAG</h3>
    <p>Create a DAG as per the following details.</p>
    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Value</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>DAG id</td>
          <td><code>ETL_toll_data</code></td>
        </tr>
        <tr>
          <td>Schedule</td>
          <td>Daily once</td>
        </tr>
        <tr>
          <td>default_args</td>
          <td>as you have defined in the previous step</td>
        </tr>
        <tr>
          <td>description</td>
          <td>Apache Airflow Final Assignment</td>
        </tr>
      </tbody>
    </table>
    <p>Take a screenshot of the command you used and the output.</p>
    <p>Name the screenshot <code>dag_definition.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.3 - Create a task to unzip data</h3>
    <p>Create a task named <code>unzip_data</code>.</p>
    <p>Use the downloaded data from the url given in the first part of this assignment in exercise 1 and uncompress it into the destination directory.</p>
    <p>Take a screenshot of the task code.</p>
    <p>Name the screenshot <code>unzip_data.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <p>Read through the file <code>fileformats.txt</code> to understand the column details.</p>
    <h3>Task 1.4 - Create a task to extract data from csv file</h3>
    <p>Create a task named <code>extract_data_from_csv</code>.</p>
    <p>This task should extract the fields <code>Rowid</code>, <code>Timestamp</code>, <code>Anonymized Vehicle number</code>, and <code>Vehicle type</code> from the <code>vehicle-data.csv</code> file and save them into a file named <code>csv_data.csv</code>.</p>
    <p>Take a screenshot of the task code.</p>
    <p>Name the screenshot <code>extract_data_from_csv.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.5 - Create a task to extract data from tsv file</h3>
    <p>Create a task named <code>extract_data_from_tsv</code>.</p>
    <p>This task should extract the fields <code>Number of axles</code>, <code>Tollplaza id</code>, and <code>Tollplaza code</code> from the <code>tollplaza-data.tsv</code> file and save it into a file named <code>tsv_data.csv</code>.</p>
    <p>Take a screenshot of the task code.</p>
    <p>Name the screenshot <code>extract_data_from_tsv.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.6 - Create a task to extract data from fixed width file</h3>
    <p>Create a task named <code>extract_data_from_fixed_width</code>.</p>
    <p>This task should extract the fields <code>Type of Payment code</code>, and <code>Vehicle Code</code> from the fixed width file <code>payment-data.txt</code> and save it into a file named <code>fixed_width_data.csv</code>.</p>
    <p>Take a screenshot of the task code.</p>
    <p>Name the screenshot <code>extract_data_from_fixed_width.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.7 - Create a task to consolidate data extracted from previous tasks</h3>
    <p>Create a task named <code>consolidate_data</code>.</p>
    <p>This task should create a single csv file named <code>extracted_data.csv</code> by combining data from</p>
    <ul>
      <li><code>csv_data.csv</code></li>
      <li><code>tsv_data.csv</code></li>
      <li><code>fixed_width_data.csv</code></li>
    </ul>
    <p>The final csv file should use the fields in the order given below:</p>
    <p><code>Rowid</code>, <code>Timestamp</code>, <code>Anonymized Vehicle number</code>, <code>Vehicle type</code>, <code>Number of axles</code>, <code>Tollplaza id</code>, <code>Tollplaza code</code>, <code>Type of Payment code</code>, and <code>Vehicle Code</code></p>
    <p>Hint: Use the bash <code>paste</code> command.</p>
    <p><code>paste</code> command merges lines of files.</p>
    <p>Example : <code>paste file1 file2 > newfile</code></p>
    <p>The above command merges the columns of the files file1 and file2 and sends the output to newfile.</p>
    <p>You can use the command <code>man paste</code> to explore more.</p>
    <p>Take a screenshot of the command you used and the output.</p>
    <p>Name the screenshot <code>consolidate_data.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.8 - Transform and load the data</h3>
    <p>Create a task named <code>transform_data</code>.</p>
    <p>This task should transform the vehicle_type field in <code>extracted_data.csv</code> into capital letters and save it into a file named <code>transformed_data.csv</code> in the staging directory.</p>
    <p>Take a screenshot of the command you used and the output.</p>
    <p>Name the screenshot <code>transform.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.9 - Define the task pipeline</h3>
    <p>Define the task pipeline as per the details given below:</p>
    <table>
      <thead>
        <tr>
          <th>Task</th>
          <th>Functionality</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>First task</td>
          <td><code>unzip_data</code></td>
        </tr>
        <tr>
          <td>Second task</td>
          <td><code>extract_data_from_csv</code></td>
        </tr>
        <tr>
          <td>Third task</td>
          <td><code>extract_data_from_tsv</code></td>
        </tr>
        <tr>
          <td>Fourth task</td>
          <td><code>extract_data_from_fixed_width</code></td>
        </tr>
        <tr>
          <td>Fifth task</td>
          <td><code>consolidate_data</code></td>
        </tr>
        <tr>
          <td>Sixth task</td>
          <td><code>transform_data</code></td>
        </tr>
      </tbody>
    </table>
    <p>Take a screenshot of the task pipeline section of the DAG.</p>
    <p>Name the screenshot <code>task_pipeline.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h1>Exercise 3 - Getting the DAG operational.</h1>
    <p>Save the DAG you defined into a file named <code>ETL_toll_data.py</code>.</p>
    <h3>Task 1.10 - Submit the DAG</h3>
    <p>Take a screenshot of the command you used and the output.</p>
    <p>Name the screenshot <code>submit_dag.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.11 - Unpause the DAG</h3>
    <p>Take a screenshot of the command you used and the output.</p>
    <p>Name the screenshot <code>unpause_dag.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <h3>Task 1.12 - Monitor the DAG</h3>
    <p>Take a screenshot of the DAG runs for the Airflow console.</p>
    <p>Name the screenshot <code>dag_runs.jpg</code>. (Images can be saved with either the .jpg or .png extension.)</p>
    <p>This concludes the assignment.</p>
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
          <td>2021-08-06</td>
          <td>0.1</td>
          <td>Ramesh Sannareddy</td>
          <td>Created initial version</td>
        </tr>
        <tr>
          <td>2022-08-26</td>
          <td>0.2</td>
          <td>Lakshmi Holla</td>
          <td>Updated sudo commands</td>
        </tr>
      </tbody>
    </table>
    <p>Copyright (c) 2021 IBM Corporation. All rights reserved.</p>
   
  </body>
</html>
