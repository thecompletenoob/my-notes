# JTAC Procedures

- Follow the recommended procedure to open a JTAC support case
- Access support resources
- Use the customer support website
- Access and use the customer support tools
- Use SFTP to transfer large files to JTAC

## Support Entitlement

JTAC supports are only offered to customers with valid maintenance contracts. A chassis serial number is required when opening a case so support can be verified.

Use the `show chassis hardware` to get your chassis serial number.

## Opening a Case

Cases can be opened in the following ways:

1. On the web using My Juniper customer portal - <https://casemanager.juniper.net/casemanager>
2. Through the phone. Always contact JTAC on phone for Priority 1 cases.

All symptoms should be illustrated with relevant command output. The command `request support information` can also be used. It runs a number of operational commands and provides a lot of information which might be useful to JTAC.

## Case Management

### Priority 1 (Critical)

Network/System outage that causes loss of service

### Priority 2 (High)

Significant impact. Eg. Intermittent impact to customers.

### Priority 3 (Medium)

Network events that result in only limited impact to end customers.

### Priority 4 (Low)

No impact to business operations. Eg. Information requests.

Juniper Networks offers systematic escalation management to customers.

## Juniper Knowledge Base

You can use the KB to research your issue. It contains:

- Product documentation
- KB articles including troubleshooting guides
- Technology notes and whitepapers

The KB can be searched using the KB number, keywords or natural language queries.

## PR Searches (Problem Report)

PR is the description of a software or hardware report. It is recommended to review release notes before deploying a new release since it may contain certain open PRs that might not be favorable in your environment.

## Additional Tools

- Junos VPN Configuration Tool
- SRX HA Configuration Tool
- IOS to Junos Translator

## SFTP for large file transfers

You can attach smaller files directly when creating a case in My Juniper portal. However, use the SFTP when transferring files larger than 10 MB.

The files are uploaded to *sftp.juniper.net* to */pub/incoming* folder with case ID

Optionally rename the file using the format *case_number-core-sequence_number*

```text
sftp anonymous@sftp.juniper.net
sftp> lcd /var/tmp
sftp> cd /pub/incoming
sftp> mkdir xxx-xxx-xxx
sftp> cd xxx-xxx-xxx
sftp> mput large-file.tgz
sftp> bye
```
