# Filtering RDS Snapshot Exports By Status via CLI

I wanted to write a small script to see all currently running RDS Snapshot Exports. These would make up two statuses: `starting` and `in_progress`.

According to [the](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ExportSnapshot.html#USER_ExportSnapshot.Monitoring) [docs](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-export-tasks.html) the API provides a `--filters` option which can filter by `export-task-identifier`, `s3-bucket`, `source-arn`, or `status` - the last one appeared to be what I needed.

If you run the command without any filters, the status fields are returned in UPPER_CASE (output redacted):

```json
{
  "ExportTasks": [
    {
      "ExportTaskIdentifier": "...",
      "SourceArn": "...",
      "SnapshotTime": "2020-09-08T08:06:19.819000+00:00",
      "TaskStartTime": "2020-09-08T16:33:22.287000+00:00",
      "S3Bucket": "...",
      "S3Prefix": "...",
      "IamRoleArn": "...",
      "KmsKeyId": "...",
      "Status": "IN_PROGRESS",
      "PercentProgress": 49,
      "TotalExtractedDataInGB": 122
    },
    {
      "ExportTaskIdentifier": "...",
      "SourceArn": "...",
      "SnapshotTime": "2020-09-08T08:11:32.744000+00:00",
      "S3Bucket": "...",
      "S3Prefix": "...",
      "IamRoleArn": "...",
      "KmsKeyId": "...",
      "Status": "STARTING",
      "PercentProgress": 0,
      "TotalExtractedDataInGB": 0
    },
    {
      "ExportTaskIdentifier": "...",
      "SourceArn": "...",
      "SnapshotTime": "2020-08-04T08:11:08.983000+00:00",
      "TaskStartTime": "2020-08-04T15:32:39.972000+00:00",
      "TaskEndTime": "2020-08-04T15:43:15.737000+00:00",
      "S3Bucket": "...",
      "S3Prefix": "...",
      "IamRoleArn": "...",
      "KmsKeyId": "...",
      "Status": "COMPLETE",
      "PercentProgress": 100,
      "TotalExtractedDataInGB": 121
    }
```

You would imagine that these would be the value you would provide to the status filter option... but you would be wrong!

```
❯ aws rds describe-export-tasks --filters Name=status,Values=IN_PROGRESS --profile wave-prod

An error occurred (InvalidParameterValue) when calling the DescribeExportTasks operation: The IN_PROGRESS value isn’t a valid export task status.
```

After a quick chat with support, we realized that the filter inputs are expecting the same strings, but in lower-case!

```json
❯ aws rds describe-export-tasks --filters Name=status,Values=in_progress --profile wave-prod
{
  "ExportTasks": [
    {
      "ExportTaskIdentifier": "...",
      "SourceArn": "...",
      "SnapshotTime": "2020-09-08T08:06:19.819000+00:00",
      "TaskStartTime": "2020-09-08T16:33:22.287000+00:00",
      "S3Bucket": "...",
      "S3Prefix": "...",
      "IamRoleArn": "...",
      "KmsKeyId": "...",
      "Status": "IN_PROGRESS",
      "PercentProgress": 13,
      "TotalExtractedDataInGB": 32
    }
  ]
}
```

AWS support is passing this feedback to the product team - perhaps they can fix the docs or make the API filter options more flexible so that you can use the task output as input to the `--filter` option.
