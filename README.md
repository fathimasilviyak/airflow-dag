An Apache Airflow DAG is a python program. It consists of 5 logical blocks.

<ul>
  <li>Imports</li>
  <li>DAG Arguments</li>
  <li>DAG Definition</li>
  <li>Task Definitions</li>
  <li>Task Pipeline</li>
</ul>

#### A typical imports block looks like

#import the libraries

from datetime import timedelta

#The DAG object; we'll need this to instantiate a DAG

from airflow.models import DAG

#Operators; you need this to write tasks!

from airflow.operators.bash_operator import BashOperator

#This makes scheduling easy

from airflow.utils.dates import days_ago

#### A typical DAG Arguments block looks like

#defining DAG arguments

#You can override them on a per-task basis during operator initialization

default_args = {
    'owner': 'Lavanya',
    'start_date': days_ago(0),
    'email': ['lavanya@somemail.com'],
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

DAG arguments are like settings for the DAG.

The above settings mention
<ul>
  <li>the owner name</li>
  <li>when this DAG should run from: days_age(0) means today</li>
  <li>the email address where the alerts are sent to</li>
  <li>the number of retries in case of failure</li>
  <li>the time delay between retries</li>
</ul>

#### A typical DAG definition block looks like

#define the DAG
  dag = DAG(
    dag_id='sample-etl-dag',
    default_args=default_args,
    description='Sample ETL DAG using Bash',
    schedule_interval=timedelta(days=1),
  )
<ul>
  <li>Here we are creating a variable named dag by instantiating the DAG class with the following parameters</li>
  <li>sample-etl-dag is the ID of the DAG. This is what we see on the web console</li>
  <li>we are passing the dictionary default_args, in which all the defaults are defined</li>
  <li>description helps us in understanding what this DAG does</li>
  <li>schedule_interval tells us how frequently this DAG runs. In this case every day. (days=1)</li>
</ul>


#### A typical task definitions block looks like

#define the tasks

#define the first task named extract

extract = BashOperator(
    task_id='extract',
    bash_command='echo "extract"',
    dag=dag,
)

#define the second task named transform

transform = BashOperator(
    task_id='transform',
    bash_command='echo "transform"',
    dag=dag,
)

#define the third task named load

load = BashOperator(
    task_id='load',
    bash_command='echo "load"',
    dag=dag,
)


A task is defined using:
<ul>
  <li>A task_id which is a string and helps in identifying the task</li>
  <li>What bash command it represents</li>
  <li>Which dag this task belongs to</li>
</ul>

#### A typical task pipeline block looks like

#task pipeline
extract >> transform >> load

Task pipeline helps us to organize the order of tasks.

Here the task extract must run first, followed by transform, followed by the task load.


## Submit a DAG

Submitting a DAG is as simple as copying the DAG python file into dags folder in the AIRFLOW_HOME directory.

Airflow searches for Python source files within the specified DAGS_FOLDER. The location of DAGS_FOLDER can be located in the airflow.cfg file, where it has been configured as /home/project/airflow/dags.

Airflow will load the Python source files from this designated location. It will process each file, execute its contents, and subsequently load any DAG objects present in the file.

Therefore, when submitting a DAG, it is essential to position it within this directory structure.


### command to submit a dag

 cp dag_name.py /home/project/airflow/dags

 ### Run the command below to list out all the existing DAGs.

 airflow dags list

 ### Run the command below to list out all the tasks in dag_name.py
 airflow tasks list dag-name

 dag-name must be there within the day_name.py in dag definition section

# user_info.py

This is a dag that runs daily, and extracts user information from /etc/passwd file, transforms it, and loads it into a file.

This DAG has two tasks extract that extracts fields from /etc/passwd file and transform_and_load that transforms and loads data into a file.

once python file is created, submit the dag using command
 
 cp user_info.py /home/project/airflow/dags

 Run the command below to list out all the tasks in my-first-dag
   
   airflow tasks list my-first-dag



# ETL_Server_Access_Log_Processing.py

Dag that extract the data from web, and transform the data then load into a zip file, that runs daily

