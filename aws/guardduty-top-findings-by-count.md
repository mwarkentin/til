# Finding the most common GuardDuty findings

You can get your GuardDuty detector IDs with this command:

```
❯ aws guardduty list-detectors --profile <aws-profile>
{
    "DetectorIds": [
        "8cb54a8639e0fde4942f6354158a40e5"
    ]
}
```

The GuardDuty CLI provides a `get-findings-statistics` command, but it only allows you to group by severity value:

```
❯ aws guardduty get-findings-statistics --detector-id <detector-id> --finding-statistic-types COUNT_BY_SEVERITY --profile <aws-profile>

{
    "FindingStatistics": {
        "CountBySeverity": {
            "8.0": 4,
            "2.0": 14,
            "5.0": 72
        }
    }
}
```

I wanted to list the most common findings by finding type. You can get a list of finding IDs like so:

```
❯ aws guardduty list-findings --detector-id <detector-id> --profile <aws-profile>
{
    "FindingIds": [
        "6aba06c9c5ad11ba81f1cdf939225b1a",
        "e8b9c37f5ffee1cd12a4c9adf3e93213",
        "baba06c16fde2ae4d1ba707f26860bbd",
        ...
        "10b8628a66050f85c71437eb29232a3b",
        "4cb938ac04bcc50008f44546e9a4fbaa",
        "1eb938a7f6145cf6ecb6cf564d53cabb"
    ]
}
```

`get-findings` returns details on a space-separated list of finding IDs:

```
aws guardduty get-findings --detector-id 8cb54a8639e0fde4942f6354158a40e5 --finding-ids 6aba06c9c5ad11ba81f1cdf939225b1a e8b9c37f5ffee1cd12a4c9adf3e93213  --profile wave-security
```

Combining these two, you can get full finding details for up to 50 findings and dump them to a file with this command: `aws guardduty get-findings --detector-id <detector-id> --finding-ids $(aws guardduty list-findings --detector-id <detector-id> --max-items 50 --profile <aws-profile> | jq -r '.FindingIds | map(.) | join(" ")') --profile <aws-profile> > findings.json`

If you have more than 50 findings you'll need to figure out how to merge them all into a single JSON file. Then you can combine and count the finding types:

```
❯ jq 'flatten | .[].Type' findings.json | sort | uniq -c | sort -r
  17 "Behavior:EC2/NetworkPortUnusual"
   7 "Recon:IAMUser/UserPermissions"
   6 "Recon:EC2/PortProbeUnprotectedPort"
   4 "Recon:IAMUser/ResourcePermissions"
   4 "Recon:IAMUser/NetworkPermissions"
   3 "UnauthorizedAccess:IAMUser/ConsoleLogin"
   1 "UnauthorizedAccess:EC2/TorRelay"
   1 "UnauthorizedAccess:EC2/TorIPCaller"
   1 "UnauthorizedAccess:EC2/TorClient"
   1 "Trojan:EC2/DriveBySourceTraffic!DNS"
   1 "Stealth:S3/ServerAccessLoggingDisabled"
   1 "Policy:IAMUser/RootCredentialUsage"
   1 "Persistence:IAMUser/UserPermissions"
   1 "Discovery:S3/TorIPCaller"
   1 "Discovery:S3/BucketEnumeration.Unusual"
```
