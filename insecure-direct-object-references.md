# Lab Report: Insecure Direct Object References

## Lab Overview

This PortSwigger Web Security Academy Apprentice lab demonstrated an insecure direct object reference (IDOR) vulnerability involving chat transcript files.

The application stored user chat logs on the server and retrieved them through static URLs. The transcript filenames contained incrementing numbers, allowing one transcript to be accessed by changing the filename in the request.

The objective was to find Carlos's password in a chat transcript and use it to log in to his account. [web:42]

## Tools Used

- Burp Suite
- Burp Proxy
- Burp Repeater
- PortSwigger Web Security Academy browser

## Exploitation Steps

1. Selected the **Live chat** tab.

2. Sent a message through the chat interface.

3. Selected **View transcript**.

4. Captured the request used to retrieve the transcript in Burp Proxy.

5. Sent the captured request to Burp Repeater.

6. Reviewed the request URL and identified the transcript filename.

7. Observed that the transcript filename used an incrementing number.

8. Changed the filename to:

   ```text
   1.txt
   ```

9. Sent the modified request from Burp Repeater.

10. Reviewed the response body.

11. Found a chat transcript belonging to another user.

12. Located Carlos's password in the transcript.

13. Returned to the main lab page.

14. Logged in using Carlos's stolen credentials.

15. The lab was successfully solved.

## Modified Request

Example request:

```http
GET /download-transcript/1.txt HTTP/2
Host: <lab-id>.web-security-academy.net
```

The exact endpoint may differ depending on the lab instance. The important part was changing the transcript filename to `1.txt`.

## Result

The application returned an older chat transcript after the filename was modified.

The transcript contained Carlos's password, which was then used to log in to his account and complete the lab. The official lab solution uses this same predictable-filename behavior to retrieve the password. [web:42]

## Vulnerability Identified

The application contained an **Insecure Direct Object Reference (IDOR)** vulnerability.

A transcript file was accessed using a direct filename in the request URL. Because filenames were predictable and the server did not verify ownership, a user could request another user's transcript by changing the file number.

The application treated knowledge of the filename as sufficient authorization.

## Impact

An attacker could access:

- Other users' chat transcripts
- Passwords and authentication details
- Personal information
- Internal support conversations
- Sensitive business data

In a real application, password disclosure could lead to account takeover, unauthorized access, data theft, and further privilege escalation.

## Recommended Remediation

- Enforce server-side authorization for every transcript request.
- Verify that the authenticated user owns or is authorized to access the requested transcript.
- Avoid predictable, sequential filenames for sensitive files.
- Use secure random identifiers mapped to users on the server.
- Store private transcripts outside publicly accessible directories.
- Prevent passwords and other secrets from being included in chat logs.
- Avoid storing sensitive credentials in application logs or support records.
- Monitor repeated requests for sequential filenames.
- Return `403 Forbidden` or `404 Not Found` when access is unauthorized.

## Key Takeaway

Predictable filenames are not a security boundary. If an application exposes direct references to private files without checking authorization, an attacker may access other users' data by modifying the filename.

Every object reference must be protected with server-side access-control checks.

## Disclaimer

This report was created for educational purposes and applies only to the authorized PortSwigger Web Security Academy lab environment.