# CVE-2021-41773

**Identification** : This flaw gives an attacker permission to map URLs to files outside protected directories (path traversal attack), and possibly allowing remote code execution. Can be exploited in Apache servers version 2.4.49.


**Cataloguing**:
- Who: Ash Daulton and the cPanel Security Team
- When:
    - Exploit created on 05/10/2021 
    - Vulnerability Reported 29/09/2021
    - Fixed at 01/10/2021
    - Update fixing it released at 04/10/2021
- How: Disclosed to Apache directory
- Severity: There are several vulnerability classifications, given that there are multiple versions of classification 
    - Critical (src: Apache) 
    - 7.5 High  (src: CVSS version 3.x)
    - 4.3 Medium  (src: CVSS version 2.0)


**Exploits**: 
- Path traversal - An attacker would be able to change the URL in an http request to directories outside the root, resulting in a successful request.
- RCE (Remote Code Execution) - If CGI (Command Gateway Interface) is enabled, it is also possible to execute arbitrary code

**Attacks**: This vulnerability is known to have been used in the wild before it was disclosed, but we did not find any specific report.

By exploring the *path traversal* exploit a possible attacker could be able to access confidential files, gaining knowledge about sensitive information. However, these files must explicitly be granted permissions. As this is not the default configuration, this exploit is useless against a large percentage of the Apache hosts.

<ins>Example</ins>: Accessing this path $host/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd would retrieve the contents of the file /etc/passwd

If the attacker was able to execute remote code, then it could impact the confidentiality, integrity and availability:

- Add/Read/Modify/Delete files
- Change access privileges
- Turn on/off configurations and services
- Communicate with other servers

**References:**
- https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-41773
- https://tryhackme.com/room/cve202141773
- https://httpd.apache.org/security/vulnerabilities_24.html
- https://www.exploit-db.com/
- https://www.secpod.com/blog/apache-http-server-zero-day-vulnerability-exploited-in-the-wild/
