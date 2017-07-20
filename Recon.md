# Recon for target hunts

When starting in bug bounty, it is important to understand the program and its scope. This does not mean just visiting the app and using it. It also means understanding the environment. In this document we will learn some techiniques for recon purposes. 

*Understanding the Environment*
It is important to see how the application is running along with its environment. We will discuss about checking the environment in the following topics. 
1) SSL Certificates

SSL Certificates will help you to see different subdomains that is under the control of the domain. SSL ceritifcates help to validate the onwership of the domain from the company. For example, with similar SSL certificate and company name, we can see that ssl.example.com might is also under control of example.com 
To run SSL tests, multiple websites can be used: https://censys.io, https://crt.sh, https://developers.facebook.com/ct

Below is the example of how Facebook certificate tools loads subdomains of hackerone.com. 

![FB Certificate tool](http://i.imgur.com/PnZVBag.png "Facebook Certificate tool")

Similarly we can also use censys.io: 

![Censys](http://i.imgur.com/91xC8rW.png "Censys")

2) Subdomains Scanning

Subdomain Scanner is another way to perform recon. Through subdomains, sometimes you can just find a takeover which is a vulnerability in itself. In another document, we will go in full depth about how Subdomain takeover works. 

There are multiple ways you can conduct subdomain scanners. Websites like https://virustotal.com, https://dnsdumpster.com, https://threatcrowd.org are great resources for this. 

Lets check out how virustotal.com works with this: 

First go to: https://virustotal.com and click on `Search`. In the text box put: `*.url.com`. I will be using uber.com for this. 

![VirusTotal](http://i.imgur.com/WpAryeB.png "Virustotal")

You can now see the subdomains that are under uber.com that was indexed by VirusTotal

Lets do the same with dnsdumpster.com as well. 

![DNSDumpster](http://i.imgur.com/fK0gl4R.png "DNSDumpster")

There are github projects that you can use fot this as well. Some of them that I highly recommend are: [sublist3r](https://github.com/aboul3la/Sublist3r), [HostileSubBruteforcer](https://github.com/nahamsec/HostileSubBruteforcer) and many others out there. 

3) Port scanners 

Port scanning can sometimes give you multiple ports to test out other than just the regular `:80` and `:443`. I usually use nmap and other tools for port scanning but there are some websites that help with this process as well. 

Following websites should allow you to do such tests: [t1shopper](http://www.t1shopper.com/tools/port-scan/), [MXtoolbox](https://mxtoolbox.com/PortScan.aspx) and [Shodan](https://shodan.io)

Lets run some scans and see how each show suchs results. 

![MXToolBox](http://i.imgur.com/evao6Mv.png "MXToolBox")

This was the result from a scan done by MXToolBox.

4) Running a directory search

Sometimes it is always good to see if you can find different files or directories of a website. This could be done from robots.txt file, Google Dorks, tools and websites. 

I use tools, Google dorks and websites to run bruteforce on directories. Google dorks can be done with simple search like `site:example.com ext:php | ext:log` and many others. Directories can also be found these ways. Sometimes this can give you access token that was being indexed by Google (which is bad). Along with that, websites like https://pentest-tools.com also have helped me on bruteforcing directories.

Here is example of https://pentest-tools.com: 

![Pentest-Tools](http://i.imgur.com/hzWpcbB.png "Pentest-Tools")

Here are some small feature of https://pentest-tools.com

![Pentest-Tools](http://i.imgur.com/BDblrHJ.png "Pentest-Tools")

There should be a new tool published soon by some of my colleagues. It is still being worked on and will be out soon. 
