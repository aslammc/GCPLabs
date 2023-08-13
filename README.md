TASK:1
Please follow these instructions carefully and replace the placeholder "ProjectID" with your actual GCP project ID wherever it appears in the document

bq mk lab_980 //change the dataset name as per the quest

gsutil mb gs://ProjectID-marking

gsutil cp gs://cloud-training/gsp323/lab.csv .

gsutil cp gs://cloud-training/gsp323/lab.schema .

cat lab.schema

[ {"type":"STRING","name":"guid"}, {"type":"BOOLEAN","name":"isActive"}, {"type":"STRING","name":"firstname"}, {"type":"STRING","name":"surname"}, {"type":"STRING","name":"company"}, {"type":"STRING","name":"email"}, {"type":"STRING","name":"phone"}, {"type":"STRING","name":"address"}, {"type":"STRING","name":"about"}, {"type":"TIMESTAMP","name":"registered"}, {"type":"FLOAT","name":"latitude"}, {"type":"FLOAT","name":"longitude"} ]

Create a table using the provided schema: Update the dataset name, table name, GS path, and schema according to the 'cat schema' output

bq load --autodetect
--source_format=CSV
lab_980.customers_578
gs://cloud-training/gsp323/lab.csv
guid:STRING,isActive:BOOLEAN,firstname:STRING,surname:STRING,company:STRING,email:STRING,phone:STRING,address:STRING,about:STRING,registered:TIMESTAMP,latitude:FLOAT,longitude:FLOAT

Data flow job : Please change the values as per the quest

gcloud dataflow jobs run transform --gcs-location gs://dataflow-templates-us-west1/latest/GCS_Text_to_BigQuery --region us-west1 --worker-machine-type e2-standard-2 --staging-location gs://ProjectID-marking/temp --parameters javascriptTextTransformGcsPath=gs://cloud-training/gsp323/lab.js,JSONPath=gs://cloud-training/gsp323/lab.schema,javascriptTextTransformFunctionName=transform,outputTable=ProjectID:lab_980.customers_579,inputFilePattern=gs://cloud-training/gsp323/lab.csv,bigQueryLoadingTemporaryDirectory=gs://ProjectID-marking/bigquery_temp

TASK:2
Dataproc cluster creation : Change the values as per the quest

gcloud dataproc clusters create cluster-af51 --region us-west1 --zone us-west1-a --master-machine-type e2-standard-2 --master-boot-disk-size 500 --num-workers 2 --worker-machine-type e2-standard-2 --worker-boot-disk-size 500 --num-secondary-workers 2 --secondary-worker-boot-disk-type --secondary-worker-boot-disk-size 0 --num-secondary-worker-local-ssds null --image-version 2.1-debian11 --secondary-worker-type preemptible --project ProjectID

//Once created the cluster, go to the Dataproc cluster's details, then navigate to 'VM Instances'. SSH into the master server. If SSH is not enabled, wait for some time. Once you're connected to the master server, execute the following command (the same command will be provided in the quest): 'hdfs dfs -cp gs://cloud-training/gsp323/data.txt /data.txt##

Now submit the jobs as per the quest, change the values as per the quest

gcloud dataproc jobs submit spark
--cluster=cluster-af51
--region=us-west1
--project=ProjectID
--class=org.apache.spark.examples.SparkPageRank
--jars=file:///usr/lib/spark/examples/jars/spark-examples.jar
--max-failures-per-hour=1
--
/data.txt

TASK:3
Run the following command in cloud shell to create an API key with a specific display name (replace "test" with your desired display name)

gcloud beta services api-keys create --display-name=test

After running the command, you will receive a response containing the details of the newly created API key, including a field named keyString. Copy the value of the keyString from the response. Export the API key value as an environment variable using the following command (replace YOUR_API_KEY_VALUE with the actual key value):

export API_KEY=YOUR_API_KEY_VALUE

Now create request json file, change the uri as per the quest

echo '{ "config": { "encoding":"FLAC", "languageCode": "en-US" }, "audio": { "uri":"gs://cloud-samples-tests/speech/brooklyn.flac" } }' > request.json

Create result json file using below command

curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" >result.json

Now copy the output to GCS bucket // Please change the GCS bucket name as per the quest

gsutil cp result.json gs://ProjectID-marking/task3-gcs-994.result

TASK:4
Run the below command in cloud shell export GOOGLE_CLOUD_PROJECT=$(gcloud config get-value core/project) gcloud iam service-accounts create my-natlang-sa --display-name "my natural language service account"

gcloud iam service-accounts keys create ~/key.json
--iam-account my-natlang-sa@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com

//change the key.json file path based on the output export GOOGLE_APPLICATION_CREDENTIALS="/home/student_00_d2521eee482/key.json"

//Change the content as per the question gcloud ml language analyze-entities --content="Old Norse texts portray Odin as one-eyed and long-bearded, frequently wielding a spear named Gungnir and wearing a cloak and a broad hat'." > result.json //verify result.json using cat command # cat result.json gsutil cp result.json gs://ProjectID-marking/task4-cnl-941.result
