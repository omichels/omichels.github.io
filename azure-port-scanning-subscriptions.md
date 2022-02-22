## White-Hat-Hacking on your Azure Subscriptions

A white-hat Hacker is a good guy with a ... small script, who is trying to be quicker in
finding the open CVEs in his organisation, than the bad guys.

I just came across a useful tool called [nrich](https://gitlab.com/shodan-public/nrich), which pulls the 
portscanning data from [shodan](shodan.io)
and reveals all IT-Sec failures of your organisations cloud journey 

```
$ echo 167.71.250.15 | nrich -
167.71.250.15
  Ports: 22, 443
  Tags: cloud
  CPEs: cpe:/a:openbsd:openssh:7.9
  Vulnerabilities: CVE-2018-20685, CVE-2019-6109, CVE-2019-6110, CVE-2019-6111
```
*Notice*:
167.71.250.15 belongs to some unfortunate guy, who rented a server on DigitalOcean and forgot to fix it.
Not to my employers Azure subscriptions.

#### Scan "your" Cloud

This best part of nrich is, that it just pulls the available data from shodan and doesn't portscan anything
itself. The bad guys already have that information

Now in the best proactive mind of a IT-sec pro, I decided to automate the tedious search for open CVEs 
with a bit of unix shell scripting. I came to think of iterating all of our Azure subscriptions and all Public IPs
and passing the output to nrich:

```
for i in $(az account list --query "[].id" -o tsv); \
do az account set --subscription $i; \
az network public-ip list --query "[].ipAddress" | jq ".[]" | tr -d '\"' | nrich - ; done
```

example output:
```
20.73.149.63
  Ports: 443
  Tags: cloud

20.76.202.174
  Ports: 80
  Tags: cloud
  CPEs: cpe:/a:igor_sysoev:nginx

20.123.224.98
  Ports: 80
  Tags: cloud
  CPEs: cpe:/a:jquery:jquery, cpe:/a:jquery:jquery_ui

20.123.224.111
  Ports: 80
  CPEs: cpe:/a:igor_sysoev:nginx

20.93.189.68
  Ports: 443
  Tags: cloud

20.23.58.108
  Ports: 80
  Tags: cloud
```