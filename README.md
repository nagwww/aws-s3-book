
---

# AWS S3

---

###### Table of Contents

* [Introduction](#introduction)
* [What is S3](#s3)
* [S3 buckets](/chapter1.md)
  * [Create](#create-an-s3-bucket) / [Delete](#bucket) / [List](#find-out-all-the-s3-buckets-in-your-aws-account) / [Tag](#tag-a-bucket)
* [S3 objects](/s3-objects.md)
  * [Create](#create-an-object) / Get / [Delete](#delete-an-object) / [List](#list-an-object) / [Tag](#tag-an-s3-object)
* [ACL and Policy](#acls--policies)
  * [Create](#create-an-acl-for-bucket) / [Get](#get-an-acl-for-bucket) / [Delete](#delete-acl) \[ ACL \]
  * [Create](#create-a-s3-bucket-policy) / [Get](#get-s3-bucket-policy)  / Delete \[ Policy \]
* [Archiving,  backup & versioning](#archiving-backup--versioning)
  * [Lifecycle](#lifecycle) \[ [Create](#create-lifecycle) / [Get](#get-life-cycle) / [Delete](#delete-lifecycle) \]
  * Glacier 
  * GCS - Google cloud storage
  * [Versioning](#versioning)
    * [Enable](#enable-versioning) / [Status](#get-versioning) / [Suspend](#delete-versioning) / [List](#list-all-the-object-versions)
* [S3 Event Notifications](#s3-event-notifications)
  * [Create](#create-event-notification-to-sqs) / [Get](#get-notification) 
* [S3 Replication](#s3-replication)
  * [Create](#create-replication) / [Get](#get-replication-configuration) / [Delete](#delete-bucket-replication)
* [S3 Analytics](#s3-analytics)
  * [Create](#create-analytics) / [List](#list-analytics) / [Get](#get-analytics) / [Delete](#delete-analytics)
* [S3 Website hosting](#website-hosting-on-s3)
  * [Create](#create-website) / [Get](#get-website) / [Delete](#delete-website) 
* [S3 Security & Tools](#s3-security-and-tools)
  * [Security Monkey](#security-monkey)

You can checkout all the examples in this book at [https://github.com/nagwww/101-AWS-S3-Hacks](https://github.com/nagwww/101-AWS-S3-Hacks)

# Introduction..

---

[Boto documentation](http://boto3.readthedocs.io/en/latest/reference/services/s3.html) for S3 is really great. There is never a day, that pass by with out referring. Every time I need help I refer to the documentation, which inspired me to create an example for each and every method and so was this AWS S3 101 hacks born.

Digital information is driving exponential growth in data. Data in the form of genes, biometrics, logs, events, photos, videos, comments, etc play a key role in the current digital generation.

If you are student, teacher, doctor, engineer no matter the profession it is likely that you are playing with data in every walk of life.

Here are some basics of data

* Each of the 0's and 1's is called a bit - Binary digit
* Eight bits form a Byte
* 1000 bytes form a KiloByte \( KB \)
* 1000 KB form a MegaByte  \( MB \)
* 1000 MB form a GigaByte  \( GB \)
* 1000 GB form a TeraByte   \( TB \)
* 1000 TB form a PetaByte   \( PB \)
* 1000 PB form a ExaByte    \( EB \)
* 1000 EB form a ZettaByte \( ZB \)

##### Running code and examples \[ test \]

Option 1 :

Option 2 :

Contributors/Authors

AWS released 100k feature in the last couple years, as i am writing this I am pretty sure this might be outdated in the next min, to keep up with the pace of innovation I take PR, so feel free to send in a PR and more than happy to take PR.

* Nag Medida
* Click here

# What is S3

---

S3 stands for **S**imple **S**torage **S**ervice. An Amazon service which was released on

The one word answer for it's widely used popularity is the ease of use at any scale ranging from 1 byte to peta bytes.

S3 is used to store your data on internet. In layman terms

* Imagine  a Folder in your C drive. Just that the Folder is present on internet.
* Imagine a Directory on your UNIX/UBUNTU server, just that the directory is on internet.

  ?![](/assets/s31.jpg)

* You can store your personal files \( Photos, videos, documents \).

* Database administrators can use it to store the RMAN Backups

* Do you have a website, then you can store your static pages like Images, Java Script, HTML,CSS.

# S3 buckets

---

Amazon Bucket is a container of data. In technical terms “A bucket is a Container with objects Inside”.

A few things to keep in mind when working with AWS S3 buckets,

* Buckets names are unique
* Buckets are regional
* By default you can create 101 buckets in an AWS account, you can always work with AWS support and increase the limit.

#### Create an S3 bucket

Come up with a bucket naming convention, for me I would like append  AWS region for all the buckets.

By default when you create an S3 bucket if you do not specify a region they are created in "us-east-1" region

```py
"""
- Hack    : Create a Bucket in S3
- AWS CLI : aws s3api create-bucket  --bucket us-east-1.nag
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-east-1.nag"
    print client.create_bucket(Bucket=bucketname)
```

To create an S3 bucket in a different AWS region

```py
"""
- Hack    : Create a Bucket in S3 ( Different region )
- AWS CLI : aws s3api create-bucket  --bucket us-west-1.nag --region us-west-1 --create-bucket-configuration LocationConstraint=us-west-1

"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-1.nag"
    print client.create_bucket(Bucket=bucketname, CreateBucketConfiguration={'LocationConstraint': 'us-west-1'})
```

#### Delete an S3 Bucket {#bucket}

To delete a S3 bucket

```py
"""
- Hack   : Delete an Bucket in S3
- AWS CLI: aws s3api delete-bucket --bucket us-east-1.nag
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucket_name = "us-east-1.nag"
    print client.delete_bucket(Bucket=bucket_name)
```

To delete a S3 bucket in a different region

```py
"""
- Hack   : Delete an Bucket in S3
- AWS CLI: aws s3api delete-bucket --bucket us-west-1.nag ( You do not have to specify the region when deleting the bucket )
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3', region_name="us-west-1")
    bucket_name = "us-west-1.nag"
    print client.delete_bucket(Bucket=bucket_name)
```

#### Find out all the S3 buckets in your AWS Account.

By default you can only create 101 S3 buckets in an AWS account. If needed you can work with AWS to increase the limit,

Here is a quick script to list all the S3 buckets in your account

```py
"""
- Author : Nag m
- Hack   : List all the S3 buckets in an account
"""

import boto3

if __name__ == "__main__":
   client = boto3.client('s3')
   all_buckets =  client.list_buckets()["Buckets"]
   for bucket in all_buckets:
       print bucket["Name"], "Created on ", bucket["CreationDate"]
```

#### Tag a Bucket

Tagging helps you to add custom metadata to your S3 bucket. Tags are of  key and value format, here is what a tag looks like,

##### Create a tag

```py
"""
- Hack   : Tag an s3 bucket
- AWS CLI: aws s3api put-bucket-tagging --bucket us-west-2.nag --tagging 'TagSet=[{Key=name,Value=nag}]'
"""

import boto3

if __name__ == "__main__":
   client = boto3.client('s3',region_name="us-west-2")
   bucketname = "us-west-2.nag"
   print client.put_bucket_tagging(Bucket=bucketname,Tagging={
        'TagSet': [
            {
                'Key': 'Name',
                'Value': 'Nag'
            },
        ]
    })
```

##### Retrive a tag

```py
"""
- Hack   : Get all the tags for an S3 bucket
- AWC CLI: aws s3api get-bucket-tagging --bucket us-west-2.nag
"""

import boto3

if __name__ == "__main__":
   client = boto3.client('s3')
   bucketname = "us-west-2.nag"
   print client.get_bucket_tagging(Bucket=bucketname)["TagSet"]
```

##### Deleted a tag

```py
"""
- Hack   : Delete bucket tagging
- AWS CLI: aws s3api delete-bucket-tagging --bucket us-west-2.nag
"""

import boto3

if __name__ == "__main__":
   client = boto3.client('s3')
   bucketname = "us-west-2.nag"
   print client.delete_bucket_tagging(Bucket=bucketname)
```

##### 

# S3 Objects

---

You can think of objects as a file. An object has,

* Data
* MetaData

##### Create an object

```py
"""
- Hack   : Create an Object in S3
- AWS CLI: aws s3api put-object --bucket us-west-2.nag --key hello.txt --body hello.txt
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3', region_name="us-west-2")
    bucketname = "us-west-2.nag"
    print client.put_object(Bucket=bucketname, Key="hello.txt", Body="Hello World")
```

##### Get an Object

##### List an object

```py
"""
- Hack   : Create an Object in S3
- AWS CLI: aws s3 ls s3://us-west-2.nag
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3', region_name="us-west-2")
    bucketname = "us-west-2.nag"
    for obj in client.list_objects(Bucket=bucketname)["Contents"]:
        print obj["Key"]
```

##### List all objects by prefix

```py
#!/usr/bin/python

"""
- Hack   : List all the objects by path
- AWS CLI: aws s3 ls s3://us-west-2.nag/hello/
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3', region_name="us-west-2")
    bucketname = "us-west-2.nag"
    for obj in client.list_objects(Bucket=bucketname, Prefix="hello/")["Contents"]:
        print obj["Key"]
```

##### Delete an Object

```py
"""
- Hack   : Delete an Object in S3
- AWS CLI: aws s3api delete-object --bucket us-west-2.nag --key hello.txt
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.delete_object(Bucket=bucketname, Key="hello.txt")
```

##### Tag an S3 object

```py
"""
- Hack   : Tag an S3 object
- AWS CLI: aws s3api put-object-tagging  --bucket us-west-2.nag --key hello.txt --tagging 'TagSet=[{Key=name,Value=nag}]'
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_object_tagging(Bucket=bucketname, Key="hello.txt",
                                    Tagging={"TagSet": [{"Key": "Name", "Value": "Nag"}, ]})
```

##### Get all the tags for an S3 object

```py
"""
- Hack   : Get all the tags for an S3 object
- AWS CLI: aws s3api get-object-tagging --bucket us-west-2.nag --key hello.txt
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.get_object_tagging(Bucket=bucketname, Key="hello.txt")["TagSet"]
```

##### Delete object tagging

```py
#!/usr/bin/python

"""
- Hack   : Delete object tagging
- AWS CLI: aws s3api delete-object-tagging --bucket us-west-2.nag --key hello.txt
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.delete_object_tagging(Bucket=bucketname, Key="hello.txt")
```

# ACL's & Policies

---

ACL's and policies are the gate keepers for your data in S3.

##### ACL - Access Control List

This is how an ACL looks like if you are curious.

* Every Bucket and object has an ACL
* Every bucket 

![](/assets/s3_acl.jpg)

##### Get an ACL for bucket

```py
"""
- Hack   : Create an Bucket in S3
- AWS CLI: aws s3api get-bucket-acl --bucket us-west-2.nag 
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print json.dumps(client.get_bucket_acl(Bucket=bucketname)["Grants"], indent=1)
```

##### Create an ACL for bucket

###### Granting READ access to "AutneticatedUsers"

```py
"""
- Hack   : Grant read access to Authenticated users ( You sure don't want to do this anytime )
- AWS CLI:aws s3api put-bucket-acl --bucket us-west-2.nag --grant-read uri=http://acs.amazonaws.com/groups/global/AuthenticatedUsers
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_acl(Bucket=bucketname, ACL="authenticated-read")
```

###### Granting READ access to a bucket from a different "AWS Account"

```py
"""
- Hack   : Grant read access to Authenticated users ( You sure don't want to do this anytime )
- AWS CLI: aws s3api put-bucket-acl --bucket us-west-2.nag --grant-read emailaddress=test@gmail.com

"""

import json
import boto3

acp = {
    "Grants": [
        {
            "Grantee": {
                "Type": "AmazonCustomerByEmail",
                "EmailAddress": "test@gmail.com"
            },
            "Permission" : "READ"
        }

    ],
    "Owner" : {
        "DisplayName": "test",
        "ID": "ba54237358a1exxxxxxxxxxxxx401cc"

      }
}

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_acl(Bucket=bucketname, AccessControlPolicy=acp)
```

###### Granting READ access a bucket from an "CanonicalUser"

canonical user ID is a long string, such as`96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2besssssss`.

You can use canonical user IDs in an Amazon S3 bucket policy for cross-account access.

```py
"""
- Hack   : Grant read access to a bucket by CanonicalUser
- AWS CLI: aws s3api put-bucket-acl --bucket us-west-2.nag --grant-read id=xxxxxxxx
"""

import json
import boto3

acp = {
    "Grants": [
        {
            "Grantee": {
                "Type": "CanonicalUser",
                "ID": "xxxx"
            },
            "Permission" : "READ"
        }

    ],
    "Owner" : {
        "DisplayName": "nagm",
        "ID": "xxxx"

      }
}

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_acl(Bucket=bucketname, AccessControlPolicy=acp)
```

# \[ TBD \] : Can add more examples

##### Delete ACL

There is NO delete ACL, however you can update the ACL. One of the recommendation is to get the current ACL and update it accordingly. Using the above methods.

#### Policies

Policies give you fine grained access control, with ACL you can only grant like READ, WRITE,FULL CONTROL, with policies you can go granular like "list" "putACL", "getACL" etc. here are a few examples,

##### Create a S3 bucket policy

```py
#!/usr/bin/python

"""
- Hack   : Create bucket policy
- AWS CLI: aws s3api put-bucket-policy --bucket us-west-2.nag --policy '{"Version":"2012-10-17","Statement":[{"Sid":"AddPerm","Effect":"Allow","Principal":"*","Action":"s3:GetObject","Resource":"arn:aws:s3:::us-west-2.nag/*"}]}'
"""

import json
import boto3
p = {"Version":"2012-10-17","Statement":[{"Sid":"AddPerm","Effect":"Allow","Principal":"*","Action":"s3:GetObject","Resource":"arn:aws:s3:::us-west-2.nag/*"}]}

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_policy(Bucket=bucketname, Policy=json.dumps(p))
```

##### Get S3 bucket policy

```py
#!/usr/bin/python

"""
- Hack   : Get  policy for a bucket
- AWS CLI: aws s3api get-bucket-policy --bucket us-west-2.nag
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.get_bucket_policy(Bucket=bucketname)["Policy"]
```

##### Create a Bucket Policy

# Archiving, Backup & Versioning

---

Archiving

* Archiving is for space management and long term retention.

Backup

* Backup is for recovery from hardware or data corruption. This is the the data that is needed for near term business continuity 

Versioning

* Versioning helps you with accidental deletion of your files/objects. Versioning is an optional feature for keeping multiple variants of an object in the same bucket. 

#### Versioning

If versioning is enabled S3 automatically adds new versions and preserves deleted objects with delete markers,

##### Enable Versioning

```py
"""
- Hack   : Enable versioning for an S3 bucket
- AWS CLI: aws s3api put-bucket-versioning --bucket us-west-2.nag --versioning-configuration '{"Status":"Enabled"}'
"""

import json
import boto3

v={
        'Status': 'Enabled'
    }

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_versioning(Bucket=bucketname, VersioningConfiguration=v)
```

##### Get Versioning

Check the status of versioning for an S3 bucket

```py
"""
- Hack   : Get versioning for an S3 bucket
- AWS CLI: aws s3api get-bucket-versioning --bucket us-west-2.nag 
"""

import json
import boto3

v = {
    'Status': 'Enabled'
}

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.get_bucket_versioning(Bucket=bucketname)
```

##### Delete Versioning

hmmm... you cannot delete versioning, however you can suspend versioning.

```py
"""
- Hack   : Suspend versioning for an S3 bucket
- AWS CLI: aws s3api put-bucket-versioning --bucket us-west-2.nag --versioning-configuration '{"Status":"Suspended"}'
"""

import json
import boto3

v={
        'Status': 'Suspended'
    }

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_versioning(Bucket=bucketname, VersioningConfiguration=v)
```

##### List all the object versions

```py
"""
- Hack   : List all the versioned objects
- AWS CLI: aws s3api list-object-versions --bucket us-west-2.nag
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.list_object_versions(Bucket=bucketname)
```

##### List all the versions of an object

```py
"""
- Hack   : List all the versioned objects
- AWS CLI: aws s3api list-object-versions --bucket us-west-2.nag --prefix hello.txt
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    all_versions = client.list_object_versions(Bucket=bucketname, Prefix="hello.txt")["Versions"]
    for obj in all_versions:
        print "Last modified, isLatest, size => ", obj["LastModified"], obj["IsLatest"], obj["Size"]
```

#### LifeCycle

What

##### Create LifeCycle

```py
#!/usr/bin/python

"""
- Hack   : Create a bucket lifecycle policy
- AWS CLI: aws s3api put-bucket-lifecycle-configuration --bucket us-west-2.nag --lifecycle-configuration  file://./f.json [ Copy the below policy to f.json ]
"""

import json
import boto3

p = {
    "Rules": [
        {
            "Status": "Enabled",
            "Prefix": "",
            "ID": "Move old versions to Glacier",
            "Expiration": {
                "Days": 5
            }
        }
    ]
}

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_lifecycle_configuration(Bucket=bucketname, LifecycleConfiguration=p)
```

##### Get Life Cycle

```py
#!/usr/bin/python

"""
- Hack   : Get the lifecycle policy of an S3 bucket
- AWS CLI: aws s3api get-bucket-lifecycle --bucket us-west-2.nag 
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.get_bucket_lifecycle_configuration(Bucket=bucketname)
```

Modify LifeCycle

There is no option to modify LifeCycle from the API, you will have to create a new one and the existing one will be replaced.

##### Delete LifeCycle

```py
#!/usr/bin/python

"""
- Hack   : Delete a bucket lifecycle policy
- AWS CLI: aws s3api delete-bucket-lifecycle --bucket us-west-2.nag
"""

import json
import boto3


if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.delete_bucket_lifecycle(Bucket=bucketname)
```

#### Glacier

Google Cloud Platform

# S3 Event notifications

---

Event notifications are great if you want to further process your data for objects that land in an S3 bucket.

##### Create event notification to SQS

```py
"""
- Hack   : Create a SQS notification
- AWS CLI: aws s3api put-bucket-notification-configuration --bucket us-west-2.nag --notification-configuration   file://./notification.json
"""

import json
import boto3

p = {
    "QueueConfigurations": {
        "QueueArn": "arn:aws:sqs:us-west-2:220580744359:nag1",
        "Events": [
            "s3:ObjectCreated:Post"
        ],
        "Id": "MjcwMTRjOGUtZDVkYS00YTA3LThkMTgtNTEwNmI3OTExNzBi",
        "Event": "s3:ObjectCreated:Post"
    }
}


if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_notification_configuration(Bucket=bucketname, NotificationConfiguration=p)
```

##### Get Notification

```py
"""
- Hack   : Get the S3 bucket notification
- AWS CLI: aws s3api get-bucket-notification --bucket us-west-2.nag
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.get_bucket_notification_configuration(Bucket=bucketname)
```

Delete Notification

There is no API to Delete a bucket notification.

# S3 Replication

---

S3 replication was a new feature introduced in 2016. One of the prerequisites of replication is versioning.

##### Create replication

```py
"""
- Hack   : Set up bucket replication
- AWS CLI: aws s3api put-bucket-replication --bucket us-west-2.nag --replication-configuration  file://./replication.json [ Copy the below policy to f.json ]
"""

import json
import boto3

p = {

    "Rules": [
        {
            "Status": "Enabled",
            "Prefix": "",
            "Destination": {
                "Bucket": "arn:aws:s3:::us-east-1.nag",
                "StorageClass": "STANDARD"
            },
            "ID": "us-west-2.nag1"
        }
    ],
    "Role": "arn:aws:iam::220580744359:role/service-role/replication_role_for_us-west-2.nag_to_us-east-1.nag"
}

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_replication(Bucket=bucketname, ReplicationConfiguration=p)
```

##### Get Replication configuration

```py
"""
- Hack   : Get the replication configuration of an S3 bucket
- AWS CLI: aws s3api get-bucket-replication --bucket us-west-2.nag
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.get_bucket_replication(Bucket=bucketname)
```

##### Delete bucket replication

```py
"""
- Hack   : Delete bucket replication
- AWS CLI: aws s3api delete-bucket-replication --bucket us-west-2.nag
"""

import json
import boto3


if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.delete_bucket_replication(Bucket=bucketname)
```

# S3 Multipart upload

---

The largest object that can be uploaded in a single PUT is 5 gigabytes. For objects larger than 100 megabytes, [Multipart Upload](http://docs.amazonwebservices.com/AmazonS3/latest/dev/UploadingObjects.html) is an option

# S3 Analytics

---

S3 analytics

##### Create analytics

```py
"""
- Hack   : Create analytics configurations for an S3 bucket.
- AWS CLI: aws s3api put-bucket-analytics-configuration --bucket us-west-2.nag --analytics-configuration '{"Id": "full1","StorageClassAnalysis": {}}' --id "full1"
"""

import boto3

config = {"Id": "full1","StorageClassAnalysis": {}}

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    analytics_config = client.put_bucket_analytics_configuration(Bucket=bucketname, Id="full1", AnalyticsConfiguration=config)
    print analytics_config
```

##### List analytics

```py
"""
- Hack   : Lists the analytics configurations for an S3 bucket.
- AWS CLI: aws s3api list-bucket-analytics-configurations --bucket us-west-2.nag
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    analytics_config = client.list_bucket_analytics_configurations(Bucket=bucketname)
    print analytics_config["AnalyticsConfigurationList"]
```

##### Get analytics

```
"""
- Hack   : Get the analytics configurations for an S3 bucket.
- AWS CLI: aws s3api get-bucket-analytics-configuration --bucket us-west-2.nag --id full
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    analytics_config = client.get_bucket_analytics_configuration(Bucket=bucketname, Id="full")
    print analytics_config["AnalyticsConfiguration"]
```

##### Delete analytics

```py
#!/usr/bin/python

"""
- Hack   : Delete analytics configurations for an S3 bucket.
- AWS CLI: aws s3api delete-bucket-analytics-configuration --bucket us-west-2.nag --id full1
"""

import boto3


if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    analytics_config = client.delete_bucket_analytics_configuration(Bucket=bucketname, Id="full1")
    print analytics_config
```

# S3 Inventory

---

# 

# WebSite Hosting on S3

---

AWS S3 bucket can also be used for hosting static websites.

Set up a website for an S3 bucket

##### Create Website

```py
"""
- Hack   : Set up a website for an S3 bucket
- AWS CLI: aws s3api put-bucket-website --bucket us-west-2.nag --website-configuration  file://./website.json
[ Copy the below policy to f.json ]
"""

import json
import boto3

p = {
    "IndexDocument": {
        "Suffix": "index.html"
    }
}

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.put_bucket_website(Bucket=bucketname, WebsiteConfiguration=p)
```

##### Get Website

```py
"""
- Hack   : Get the website configuration of an S3 bucket
- AWS CLI: aws s3api get-bucket-website --bucket us-west-2.nag
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.get_bucket_website(Bucket=bucketname)
```

##### Delete Website

```
"""
- Hack   : Delete website configuration for an S3 bucket
- AWS CLI: aws s3api delete-bucket-website --bucket us-west-2.nag
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3')
    bucketname = "us-west-2.nag"
    print client.delete_bucket_website(Bucket=bucketname)
```

# Pre-Signed URL's

---

All objects and buckets by default are private. The pre-signed URLs are useful if you want your user/customer to be able upload a specific object to your bucket, but you don't require them to have AWS security credentials or permissions. When you create a pre-signed URL, you must provide your security credentials, specify a bucket name, an object key, an HTTP method \(PUT for uploading objects\), and an expiration date and time. The pre-signed URLs are valid only for the specified duration.

##### Generate a pre-signed URL to post

```py
#!/usr/bin/python

"""
- Hack   : Generate a presigned url for post
- AWS CLI: N/A
"""

import json
import boto3

if __name__ == "__main__":
    client = boto3.client('s3', region_name="us-west-2")
    bucketname = "us-west-2.nag"
    post_url = client.generate_presigned_post(Bucket=bucketname, Key="hello1.txt", ExpiresIn=3600,
                                         Fields={"acl": "public-read", "Content-Type": "html/txt"},
                                         Conditions=[
                                             {"acl": "public-read"},
                                             {"Content-Type": "html/txt"}
                                         ],
                                         )
    print "URL to test : ", 'https://{}.s3.amazonaws.com/{}'.format(bucketname,"hello1.txt"), post_url

```

##### Generate a pre-signed URL to get

Note : Unlike other methods you have to provide the region in the when you create the client, else when you access the following error is thrown "The bucket you are attempting to access must be addressed using the specified endpoint"

```py
#!/usr/bin/python

"""
- Hack   : Generate a presigned url
- AWS CLI: There is no CLI
"""

import boto3

if __name__ == "__main__":
    client = boto3.client('s3', region_name="us-west-2")
    bucketname = "us-west-2.nag"
    post_url = client.generate_presigned_url('get_object', {'Bucket': bucketname , 'Key':'hello1.txt' }, ExpiresIn=3600)
    print "URL to test : ", post_url
```

# S3 VPC Endpoints

---

VPC endpoint enables you to create a private connection between your VPC and S3, meaning all the S3 calls would go from your VPC to S3 privately and not over the internet. Endpoints are

* Virtual devices, 
* horizontally scaled, 
* redundant, and 
* highly available VPC components that allow communication between instances in your VPC and AWS services without imposing availability risks or bandwidth constraints on your network traffic.

VPC Endpoint simply changes the way in which the requests are routed from EC2 to S3.

One of the perks with VPC endpoints is you can restrict access to your bucket based on a VPC, here is a quick example

```py
{
        "Resource": [
          "arn:aws:s3:::us-west-2.nag",
          "arn:aws:s3:::us-west-2.nag/*"
        ],
        "Effect": "Allow",
        "Sid": "SID-Nag",
        "Action": [
          "s3:GetObject"
        ],
        "Condition": {
          "StringEquals": {
            "aws:sourceVpc": [
                  "vpc-123456"
            ]
          }
        },
        "Principal": "*"
}
```

Create VPC endpoint,

# S3 security and tools

---

There are primarily two layers of security you can use to secure your S3 data

* Resource level security
  * S3 ACLS.
  * S3 Policies at bucket level
  * S3 permissions at object level
* User level security
  * AWS IAM Policy

The AWS account which creates the object is the owner of the object.

#### Security Monkey

Security Monkey is an OpenSource application from Netflix \( NetflixOSS\) which monitors/alerts/reports one or multiple AWS accounts for anomalies.

Security Monkey monitors AWS S3 buckets across multiple AWS accounts, here are few things which it can be used for,

* Security Monkey acts as the source control for your S3 buckets policies, ACL, lifecycle rules.
* Generates an audit report of all the current issues \( Ex : AWS S3 buckets which are accessible to everyone shared across unknown AWS accounts and have conditional statements \)
* Creates an e-mail alert when a S3 bucket is added or deleted.
* AWS S3 resource policies are used to grant fine grain access controls for S3 buckets and objects. All the ACL’s and policies are stored in security monkey which triggers alerts when changes are done. Comes handy when you have sensitive S3 buckets and you want to monitor for changes.
* Tracks S3 buckets for bucket-level encryption.
* Tracks versioning of buckets.
* Tracks the lifecycle object of an S3 bucket. Lifecycle rules enable you to automatically archive/delete S3 objects based on predefined rule sets.
* Monitors S3 ACL’s and bucket policies since last check and alerts when buckets are publicly accessible. Here is a good read on 100’s AWS S3 buckets left open exposing private data
  [https://www.helpnetsecurity.com/2013/03/27/thousands-of-amazon-s3-buckets-left-open-exposing-private-data/](https://www.helpnetsecurity.com/2013/03/27/thousands-of-amazon-s3-buckets-left-open-exposing-private-data/)
* Click here for more info on [Security Monkey](https://medium.com/@nagwww/secops-with-security-monkey-2ad26cccd5ec)



