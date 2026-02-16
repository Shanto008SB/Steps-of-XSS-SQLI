<img width="3780" height="1890" alt="Shanto Bormon" src="https://github.com/user-attachments/assets/3d453833-350b-4518-b714-93f89f4a7c0f" />

# Steps of XSS-SQLI

## **Introduction**

Welcome to **Steps of XSS-SQLI** — a streamlined, high-speed reconnaissance and vulnerability discovery workflow designed for professional security researchers and bug bounty hunters.

In the world of security testing, **speed and coverage are everything.** This repository provides a battle-tested pipeline that automates the essential phases of a web audit: subdomain enumeration, live host discovery, and deep parameter mining. By filtering out the noise, it allows you to focus your energy on identifying and validating **Cross-Site Scripting (XSS)** and **SQL Injection (SQLi)** vulnerabilities.

### **Why use this?**
* **Efficiency:** Move from a raw domain to a filtered list of vulnerable parameters in minutes.
* **Precision:** Leverages industry-standard tools to minimize false positives.
* **Scalability:** Built to handle large scopes by seamlessly piping data between specialized tools.

---

## **Reconnaissance**
1. subdomain finding
```bash
subfinder -d target.com -o subs.txt
```
2. Live Subdomains 
```bash
cat subs.txt | httpx -silent -o live.txt
```
3. Collect all URLs
```bash
cat live.txt | waybackurls > urls.txt
```
4. Find Parameter URLs
```bash
grep -E '\?|&' urls.txt > param_urls.txt
```
***(Optional)*** Advanced Parameter Discovery
```bash
paramspider -l live.txt
```
5. Extract Parameter Names
```bash
cat param_urls.txt | grep -oP '(?<=[\?&])[a-zA-Z0-9_]+(?==)' | sort -u > params.txt
```
6. Extract JS URLs
```bash
grep -E '\.js($|\?)' urls.txt | sort -u > js_urls.txt
```
------------------------

## SQLi Testing Process
7. Identify SQLi Targets
```bash
grep -E "(id|user|pid|cid|order|product)" param_urls.txt > sqli_targets.txt
```
### 8. ***Automated SQLi***
Test single URL
```bash
sqlmap -u "https://target.com/product?id=1" --batch --risk=3 --level=3
```
Test in parameter file
```bash
sqlmap -m sqli_targets.txt --batch --threads=5
```
------------------------

## XSS Testing Process
9 Identify XSS Targets
```bash
grep -E "(search|q|s|query|redirect|name|title|msg)" param_urls.txt > xss_targets.txt
```
### 10. ***Automated XSS***
Single URL Basic XSS scan
```bash
dalfox url "https://target.com/product?id=test"
```
Test in parameter file
```bash
dalfox file xss_targets.txt -o xss_results.txt
```
