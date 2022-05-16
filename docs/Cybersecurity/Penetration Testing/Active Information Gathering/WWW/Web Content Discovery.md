## Web Content Discovery

Use SecLists and get creative with WFUZZ!

- https://sirensecurity.io/blog/seclists/

### Installing SecLists

```bash
apt install seclists
# or
cd /usr/share/
git clone https://github.com/danielmiessler/SecLists.git
mv SecLists seclists
```

### Nikto

```bash
nikto --host $URL -C all
```

### gobuster

We will begin with Gobuster.

```bash
export URL="https://example.com/"
```

Bust directories:

```bash
gobuster dir -u $URL -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -k -t 30
```

Bust files:

```bash
gobuster dir -u $URL -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt -k -t 30
```

Bust sub-domains:

```bash
gobuster dns -d someDomain.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -t 30
```

**Make sure any DNS name you find resolves to an in-scope address before you test it.**

---

### WFUZZ

Fuzz directories:

```bash
export URL="https://example.com/FUZZ/"
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt "$URL" |grep -ivE '404|403'
```

Fuzz files:

```bash
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt "$URL" |grep -ivE '404|403'
```

Authenticated fuzzing:

```bash
wfuzz -c -b "<SESSIONVARIABLE>=<SESSIONVALUE>" -z file,/usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt "$URL" |grep -ivE '404|403'
```

Fuzz data and check for parameters:

```bash
export URL="https://example.com/?parameter=FUZZ
# --> and/or some combination of...
# export URL="https://example.com/?FUZZ=data
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt "$URL"
```

Fuzzing post data:

```bash
wfuzz -c -z file,/usr/share/wordlists/Fuzzing/command-injection.txt -d "postParameter=FUZZ" "$URL"
```