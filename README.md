# End-to-End-Machine-Learning-with-TensorFlow-on-GCP

## Create Storage Bucket

Choose a Regional bucket and set a unique name (use your project ID because it is unique). Then, click Create.

## Launch Cloud Datalab

gcloud compute zones list

datalab create mydatalabvm --zone ```<ZONE>```

## Clone course repo within your Datalab instance

In Cloud Datalab home page (browser), navigate into notebooks and add a new notebook using the icon notebook.png on the top left.

Rename this notebook as repocheckout.

```
%bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
rm -rf training-data-analyst/.git
```

## Explore dataset

```
# change these to try this notebook out
BUCKET = 'qwiklabs-gcp-XXXXXXXXXXXXXXX'   # CHANGE this to a globally unique value. Your project name is a good option to try.
PROJECT = 'qwiklabs-gcp-XXXXXXXXXXXXXXX'     # CHANGE this to your project name
REGION = 'us-central1'               # CHANGE this to one of the regions supported by Cloud AI Platform https://cloud.google.com/ml-engine/docs/tensorflow/regions

import os
os.environ['BUCKET'] = BUCKET
os.environ['PROJECT'] = PROJECT
os.environ['REGION'] = REGION

%%bash
if ! gsutil ls | grep -q gs://${BUCKET}/; then
  gsutil mb -l ${REGION} gs://${BUCKET}
fi

# Create SQL query using natality data after the year 2000
query = """
SELECT
  weight_pounds,
  is_male,
  mother_age,
  plurality,
  gestation_weeks,
  ABS(FARM_FINGERPRINT(CONCAT(CAST(YEAR AS STRING), CAST(month AS STRING)))) AS hashmonth
FROM
  publicdata.samples.natality
WHERE year > 2000
"""

# Call BigQuery and examine in dataframe
from google.cloud import bigquery
df = bigquery.Client().query(query + " LIMIT 100").to_dataframe()
df.head()

# Create SQL query using natality data after the year 2000
query2 = """
SELECT
  distinct(is_male) as sex,
  count(plurality),
  avg(weight_pounds)
FROM
  publicdata.samples.natality
WHERE year > 2000
GROUP BY sex
"""

# Call BigQuery and examine in dataframe
from google.cloud import bigquery
df = bigquery.Client().query(query2).to_dataframe()
df.head()

# is_male	num_babies	avg_wt
# False	16245054	7.104715
# True	17026860	7.349797

from google.cloud import bigquery
df = bigquery.Client().query(query).to_dataframe()
df.plot(x='is_male', y='num_babies', logy=True, kind='bar');
df.plot(x='is_male', y='avg_wt', kind='bar');


```
