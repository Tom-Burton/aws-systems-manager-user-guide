# Update Session Manager preferences \(command line\)<a name="getting-started-configure-preferences-cli"></a>

The following procedure describes how to use your preferred command line tool to make changes to the AWS Systems Manager Session Manager preferences for your AWS account in the selected AWS Region\. Use Session Manager preferences to specify options for logging session data in an Amazon Simple Storage Service \(Amazon S3\) bucket or Amazon CloudWatch Logs log group\. You can also use Session Manager preferences to encrypt your session data\.

**To update Session Manager preferences \(command line\)**

1. Create a JSON file on your local machine with a name such as `SessionManagerRunShell.json`, and then paste the following content into it\.

   ```
   {
       "schemaVersion": "1.0",
       "description": "Document to hold regional settings for Session Manager",
       "sessionType": "Standard_Stream",
       "inputs": {
           "s3BucketName": "",
           "s3KeyPrefix": "",
           "s3EncryptionEnabled": true,
           "cloudWatchLogGroupName": "",
           "cloudWatchEncryptionEnabled": true,
           "cloudWatchStreamingEnabled": false,
           "kmsKeyId": "",
           "runAsEnabled": true,
           "runAsDefaultUser": "",
           "idleSessionTimeout": "",
           "maxSessionDuration": "",
           "shellProfile": {
               "windows": "date",
               "linux": "pwd;ls"
           }
       }
   }
   ```

1. Specify where you want to send session data\. You can specify an S3 bucket name \(with an optional prefix\) or a CloudWatch Logs log group name\. If you want to further encrypt data between local client and managed nodes, provide the AWS KMS key to use for encryption\. The following is an example\.

   ```
   {
     "schemaVersion": "1.0",
     "description": "Document to hold regional settings for Session Manager",
     "sessionType": "Standard_Stream",
     "inputs": {
       "s3BucketName": "DOC-EXAMPLE-BUCKET",
       "s3KeyPrefix": "MyBucketPrefix",
       "s3EncryptionEnabled": true,
       "cloudWatchLogGroupName": "MyLogGroupName",
       "cloudWatchEncryptionEnabled": true,
       "cloudWatchStreamingEnabled": false,
       "kmsKeyId": "MyKMSKeyID",
       "runAsEnabled": true,
       "runAsDefaultUser": "MyDefaultRunAsUser",
       "idleSessionTimeout": "20",
       "maxSessionDuration": "60",
       "shellProfile": {
           "windows": "MyCommands",
           "linux": "MyCommands"
       }
     }
   }
   ```
**Note**  
If you don't want to encrypt the session log data, change `true` to `false` for `s3EncryptionEnabled`\.  
If you aren't sending logs to either an Amazon S3 bucket or a CloudWatch Logs log group, don't want to encrypt active session data, or don't want to turn on Run As support for the sessions in your account, you can delete the lines for those options\. Make sure the last line in the `inputs` section doesn't end with a comma\.  
If you add a KMS key ID to encrypt your session data, both the users who start sessions and the managed nodes that they connect to must have permission to use the key\. You provide permission to use the KMS key with Session Manager through AWS Identity and Access Management \(IAM\) policies\. For information, see the following topics:  
Add AWS KMS permissions for users in your account: [Quickstart default IAM policies for Session Manager](getting-started-restrict-access-quickstart.md)\.
Add AWS KMS permissions for managed nodes in your account: [Step 2: Verify or create an IAM role with Session Managerpermissions](session-manager-getting-started-instance-profile.md)\.

1. Save the file\.

1. In the directory where you created the JSON file, run the following command\.

------
#### [ Linux & macOS ]

   ```
   aws ssm update-document \
       --name "SSM-SessionManagerRunShell" \
       --content "file://SessionManagerRunShell.json" \
       --document-version "\$LATEST"
   ```

------
#### [ Windows ]

   ```
   aws ssm update-document ^
       --name "SSM-SessionManagerRunShell" ^
       --content "file://SessionManagerRunShell.json" ^
       --document-version "$LATEST"
   ```

------
#### [ PowerShell ]

   ```
   Update-SSMDocument `
       -Name "SSM-SessionManagerRunShell" `
       -Content (Get-Content -Raw SessionManagerRunShell.json) `
       -DocumentVersion '$LATEST'
   ```

------

   If successful, the command returns output similar to the following\.

   ```
   {
       "DocumentDescription": {
           "Status": "Updating",
           "Hash": "ce4fd0a2ab9b0fae759004ba603174c3ec2231f21a81db8690a33eb66EXAMPLE",
           "Name": "SSM-SessionManagerRunShell",
           "Tags": [],
           "DocumentType": "Session",
           "PlatformTypes": [
               "Windows",
               "Linux"
           ],
           "DocumentVersion": "2",
           "HashType": "Sha256",
           "CreatedDate": 1537206341.565,
           "Owner": "111122223333",
           "SchemaVersion": "1.0",
           "DefaultVersion": "1",
           "DocumentFormat": "JSON",
           "LatestVersion": "2"
       }
   }
   ```