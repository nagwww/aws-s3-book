
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
* Archiving and backup
  * Lifecycle \[ Create / Get / Delete \]
  * Glacier 
  * GCS - Google cloud storage
* [S3 Security](/s3-and-security.md)

You can checkout all the examples in this book at [https://github.com/nagwww/101-AWS-S3-Hacks](https://github.com/nagwww/101-AWS-S3-Hacks)

# 

# Introduction..

---

Digital information is driving exponential growth in data. Data in the form of genes, biometrics, logs, events, photos, videos, comments, etc play a key role in the current digital generation.

If you are student, teacher, doctor, engineer no matter the profession it is likely that you are playing with data in every walk of life.

Here are some basics of data

* Each of the 0's and 1's is called a bit - Binary digit
* Eight bits form a Byte

##### Running code and examples \[ test \]

Option 1 :

Option 2 :

Contributors

# What is S3

---

S3 stands for **S**imple **S**torage **S**ervice. An Amazon service which was released on

The one word answer for it's widely used popularity is the ease of use at any scale ranging from 1 byte to peta bytes.

S3 is used to store your data on internet. In layman terms

* Imagine like a Folder in your C drive. Just that the Folder is present on internet.
* Imagine like a Directory on your UNIX/UBUNTU server, just that the directory is on internet.

  ?![](/assets/s31.jpg)

* You can store your personal files \( Photos, videos, documents \). Why buy an external hard drive you can as well use S3

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

Come up with a bucket naming convention, for me I would like append the AWS region for all the buckets.

By default when you create an S3 bucket if you do not specify a region they are created in "us-east-1" region

Using AWS CLI :

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

###### Granting READ access a bucket from an "AWS Account"

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

Enable Versioning 

Delete Versioning 

#### LifeCycle

What 

Create LifeCycle 

Modify LifeCycle

Delete LifeCycle

#### Glacier

Google Cloud Platform

# S3 Event notifications

---



# S3 Replication

---

S3 replication was a new feature introduced in 2016. One of the prerequisites of replication is versioning. 

# S3 and Security

---

There are primarily two layers of security you can use to secure you S3 data

* Resource level security
  * S3 ACLS.
  * S3 Policies at bucket level
  * S3 permissions at object level
* User level security
  * AWS IAM Policy

Things to note :

AWS account which create the object is the owner of the object. Here are few example,

Tools I use to monitor my S3.

Security Monkey

