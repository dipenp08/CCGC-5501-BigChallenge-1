# Challenge 1

## Challenge Statement
Retrieve the flag from the S3 bucket based on the provided IAM policy.

## IAM Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::thebigiamchallenge-storage-9979f4b/*"
        },
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::thebigiamchallenge-storage-9979f4b",
            "Condition": {
                "StringLike": {
                    "s3:prefix": "files/*"
                }
            }
        }
    ]
}

Analysis of the IAM Policy

What do I have access to?

The s3:GetObject action grants the ability to retrieve objects from the bucket thebigiamchallenge-storage-9979f4b.
The s3:ListBucket action allows listing objects in the bucket but only under the files/ prefix.

What don’t I have access to?

Writing or modifying objects in the bucket is not permitted since actions like s3:PutObject are not allowed.
Listing objects outside the files/ prefix is not permitted.

What do I find interesting?

The bucket is open to all principals ("Principal": "*") without restrictions, which poses a potential security risk.
A specific condition restricts listing to the files/ prefix, suggesting the flag might be stored there.

Solution

Detailed Steps to the Flag

1.	List Objects in the Bucket:
Use the AWS CLI to list objects within the files/ prefix:

aws s3 ls s3://thebigiamchallenge-storage-9979f4b/files/

Output:

2025-01-22 12:30:00      123 flag1.txt

2.	Retrieve the Object:
Download the flag1.txt file using the s3:GetObject permission:

aws s3 cp s3://thebigiamchallenge-storage-9979f4b/files/flag1.txt .

3.	Extract the Flag:
Open the file to reveal its contents:

cat flag1.txt

Output:

{wiz:exposed-storage-risky-as-usual}

Reflection

What was your approach?
I analyzed the IAM policy to understand the permissions granted.
I used AWS CLI to list the bucket contents and retrieve the required file.
By following the allowed actions, I successfully extracted the flag.

What was the biggest challenge?
Ensuring the correct interpretation of the IAM policy to avoid unnecessary actions.

How did you overcome the challenges?
I carefully mapped each policy statement to the appropriate AWS CLI commands, focusing only on permitted actions.

What led to the breakthrough?
Identifying the condition that restricted listing to the files/ prefix allowed me to narrow my search for the flag.

On the blue side, how can the learning be used to properly defend the important assets?
Avoid public policies granting unrestricted access to buckets ("Principal": "*") unless absolutely necessary.
Implement stricter conditions or use IAM roles to limit access to authorized users or services.
Regularly audit bucket policies to ensure sensitive data is not unintentionally exposed.
