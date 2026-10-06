# PortSwigger: Insecure Direct Object References

A writeup for the PortSwigger Web Security Academy Apprentice lab:

**Lab: Insecure direct object references**

## Overview

This lab demonstrates an insecure direct object reference (IDOR) vulnerability involving chat transcript files.

The application stores user chat logs directly on the server's file system and retrieves them using static URLs. The transcript filenames use an incrementing number, making it possible to request another user's transcript by changing the filename. [web:42]

The objective was to find Carlos's password in a chat transcript and use it to log in to his account.

## Vulnerability

- Vulnerability type: Insecure Direct Object Reference (IDOR)
- Impact: Sensitive information disclosure
- Exposed data: User password
- Tool used: Burp Suite

## TL;DR

1. Selected the **Live chat** tab.
2. Sent a message.
3. Selected **View transcript**.
4. Captured the transcript request and sent it to Burp Repeater.
5. Identified the `GET` request used to retrieve the transcript.
6. Changed the transcript filename to `1.txt`.
7. Sent the modified request.
8. Reviewed the returned chat history.
9. Found Carlos's password in the transcript.
10. Used the stolen credentials to log in to Carlos's account and solve the lab.

## Example Request

```http
GET /download-transcript/1.txt HTTP/2
Host: <lab-id>.web-security-academy.net
```

The exact path may differ depending on the lab instance.

## Key Takeaway

Predictable filenames can expose sensitive files when the application does not perform server-side authorization checks. A user should only be able to access transcripts that belong to them or that they are explicitly authorized to view.

## Remediation

- Enforce server-side authorization before returning a transcript.
- Avoid using predictable, sequential filenames for sensitive files.
- Use secure identifiers mapped to users on the server.
- Store private transcripts outside publicly accessible directories.
- Prevent passwords and other secrets from being written to chat logs.
- Monitor requests for sequential transcript filenames.

## Disclaimer

This writeup is for educational purposes and applies only to the authorized PortSwigger Web Security Academy lab environment.
