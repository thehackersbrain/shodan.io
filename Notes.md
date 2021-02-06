# Shodan.io
Shodan.io is a search engine for the Internet of Things Devices.
Shodan scans the whole internet and indexes the services run on each IP address.

## Finding Services
Cloudflare acts as a proxy between TryHackMe and their real servers. If we were pentesting a large company, this isn't helpful. We need some way to get their IP addresses.

We can do this using Automomous System Numbers.

### Autonomous System Numbers
An autonomous system number (ASN) is a global identifier of a range of IP addresses. If you are an enormous company like Google you will likely have your own ASN for all the IP addresses you own.

We can use the IP address into an ASN lookup tool such as [ultratools](https://www.ultratools.com/tools/asnInfo), Which tells us they have the `ASN AS14061`

Google Search Report for `AS14061` shows that this is `DigitalOcean ASN` number.
![[Pasted image 20210206170930.png]]

On Shodan.io we can use ASN filter for searching for specific `ASN` devices numbers. The Filter is `ASN: [number]` which in our case it become `ASN:AS14061`

![[Pasted image 20210206171531.png]]

Query URL: [here](https://www.shodan.io/search?query=ASN%3AAS14061)


## Getting Started
Time to dig in! 

### Banners
Banner is one of the most important part of Information Gathering.

An example of banner
```json
{
		"data": "Moxa Nport Device",
		"Status": "Authentication disabled",
		"Name": "NP5232I_4728",
		"MAC": "00:90:e8:47:10:2d",
		"ip_str": "46.252.132.235",
		"port": 4800,
		"org": "Starhub Mobile",
		"location": {
				"country_code": "SG"
		}
 }
```

this is an output of a single port, which includes information about the IP and authentication details.

### Questions

1. What is Google's ASN number?
```
AS15169
```
2. When was it allocated? Give the year only.
```
2000
```
![[Pasted image 20210206172807.png]]

3. Where are the most of the machines on this ASN number, physically in the world?
```
United States
```
4. What is Google's top services across all their devices on this ASN?
```
SSH
```
![[Pasted image 20210206173209.png]]
5. What SSH product does Google use?
```
OpenSSH
```
![[Pasted image 20210206173500.png]]
6. What is Google's most used Google product, according to this search ? Ignore the work "Google" in front of it.
```
cloud
```
![[Pasted image 20210206173628.png]]

## Filters
On the Shodan.io homepage, we can click on "explore" to view the most up voted search queries. The most popular one is webcams. [here](https://www.shodan.io/explore)

Note: this is a grey area. It is legal to view a publicly accessible webcam, it is illegal to try to break into a password protected one. Use your brain and research the laws of you country!

one the most up voted searches is a search for `MYSQL` databases. [here](https://www.shodan.io/search?query=product%3AMySQL)

if we look at the search, we can see it is another filter.
`product:MySQL`

On TryHackMe's ASN, let's try to find some `MYSQL` servers.

We use this search query
```
asn:AS14061 product:MySQL
```

And YAY! We have `MYSQL` servers on the TryHackMe ASN (which is really the DigitalOcean ASN). [here](https://www.shodan.io/search?query=asn%3AAS14061+product%3AMySQL)
![[Pasted image 20210206174830.png]]

We have some amazing filter called `vuln`
Let's search for Eternal Blue Vulnerable Devices.
```
VULN:MS17-010
```

**However, this is only available for academic or business users, to prevent bad actors from abusing this!**

City Country Geo (coordinates) Hostname net (based on IP / CIDR) os (find operating systems) port before/after (timeframes)

### API
Shodan.io has an API! It requires an account, so I won't talk about it here.

Shodan.io API, a blog post about finding Pi-Holes with it here:
[here](https://github.com/beesecurity/How-I-Hacked-Your-Pi-Hole/blog/master/README.md)

**NOTE: We can use `API` to program and automate some manual things.**

### Questions
1. How to we find Eternal Blue exploits on Shodan?
```
VULN:MS17-010
```

## Google & Filtering
Learning to filter with Google. 
**Helpful Hint: pay close attension to what the question is asking you!**

1. What is the top operating system for `MYSQL` servers in Google's  ASN?
```
5.6.40-84.0-log	ASN:AS15169 product:"MySQL"
```
2. What is the 2nd most popular country for MYSQL servers in Google's ASN?
```
Netherlands
```
3. Under Google's ASN, which is more popular for nginx, Hypertext Transfer Protocol or Hypertext Transfer Protocol with SSL?
```
Hypertext Trasfer Protocol	ASN:AS15169 product:"nginx"
```


## Shodan Monitor
Shodan Monitor is an application for monitoring your devices in your own network.
__Keep track of the devices that you have exposed to the Internet. Setup notifications, launch scan and gain complete visibility into what you have connected.__

Previously we had to do this using their API, but now we have this fancy application.
Access the dasahboard via this link:
[here](https://monitor.shodan.io/dashboard)

follow the setup steps and you're done...

## Shodan Dorking
Shodan has some lovely webpages with Dorks that allow us to find things. Their search example webpages feature some.
some fun ones include:
```
has_screenshot:true excrypted attention
```

Which uses optical character recognition and remote desktop to find machines compromised by ransomware on the internet.

```
screenshot.label:ics
```

`vuln:CVE-2014-0160` Internet connected machines vulnerable to heartbleed. Note: CVE search is only allowed to academic or business subscribers.

Solar Winds Supply Chain Attack by using Favicons:
```
http.favicon.hash:-1776962843
```
You can find more Shodan Dorks on GitHub.

1. What dork lets us find PCs infected by Ransomware?
```
has_screenshot:true encrypted attention
```


## Shodan Extension
Shodan also has an extension.

Chrome: [here](https://chrome.google.com/webstore/detail/shodan/jjalcfnidlmpjhdfepjhjbhnhkbgleap)
Firefox: [here](https://chrome.google.com/webstore/detail/shodan/jjalcfnidlmpjhdfepjhjbhnhkbgleap)

When installed, you can click on it and it'll tell you the IP address of the web server running, what ports are open, where it's based and if it has any security issues.

this extension is cool for people interested in bug bounties, being quickly able to tell if a system looks vulnerable or not based on the Shodan output.


## Exploring the API & Conclusion
Shodan.io has an API! It requires an account, so I won't talk about it here.

Shodan API Blog: [here](https://github.com/beesecurity/How-I-Hacked-Your-Pi-Hole/blob/master/README.md)

The API will lets us programm and automate some manual things.

**PS: You can automatically filter on Shodan by clicking the things in the left hand side bar!**

