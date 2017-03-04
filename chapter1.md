# S3 buckets

---

Amazon Bucket is a container of data. In technical terms “A bucket is a Container or Folder with objects Inside”.

A few things to keep in mind when working with AWS S3 buckets,

* Buckets names are unique
* Buckets are regional
* By default you can create 101 buckets in an AWS account, you can always work with AWS support and increase the limit.

#### Create an S3 bucket

Come up with a bucket naming convention, for me I would like append the AWS region for all the buckets.

By default when you create an S3 bucket if you do not specify a region they are created in "us-east-1" region

Using AWS CLI :

```py
"""
TO Create an S3 bucket named : us-east-1.nag-bucket
"""
import boto3

if __name__ == "__main__":
   client = boto3.client('s3')
   bucket_name = "us-east-1.nag-bucket"
   print client.create_bucket(Bucket=bucket_name)
```

To create an S3 bucket in a different AWS region

```py
import boto3

if __name__ == "__main__":
   client = boto3.client('s3')
   bucketname = "eu-west-1.nag-bucket"
   print client.create_bucket(Bucket=bucketname, CreateBucketConfiguration={ 'LocationConstraint': 'eu-west-1' })
```

#### Delete an S3 Bucket

To delete a S3 bucket

```py
import boto3

if __name__ == "__main__":
   client = boto3.client('s3')
   bucket_name = "101-s3-aws-1"
   print client.delete_bucket(Bucket=bucket_name)
```

To delete a S3 bucket in a different region

```py
import boto3

if __name__ == "__main__":
   client = boto3.client('s3',region_name="eu-west-1")
   bucket_name = "eu-west-1.nag"
   print client.delete_bucket(Bucket=bucket_name)
```

#### Find out all the S3 buckets in  your AWS Account.

By default you can only create 101 S3 buckets in an AWS account. If needed you can work with AWS to increase the limit,

Here is a quick script to list all the S3 buckets in your account

```py
import boto3

if __name__ == "__main__":
   client = boto3.client('s3')
   all_buckets =  client.list_buckets()["Buckets"]
   for bucket in all_buckets:
       print bucket["Name"], "Created on ", bucket["CreationDate"]
```



