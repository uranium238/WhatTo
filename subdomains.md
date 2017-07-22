Many websites do not just work with example.com technique or just www.example.com. For emails, blog, internal domains these companies tend to use subdomains. Subdomains map itself to a specific IP, 3rd party servicec etc to serve the contents. Subdomain takeovers are still popular in bug bounty and can be seen in many programs. I don't think I will be wrong if I said 90% of the companies launching their resposnisble disclosure programs are vulnerable to subdomain takeovers. But how do these work? 

# History of Subdomain Takeover

I personally saw subdomain takeovers in hike during the year 2014. This was when a blog post was made by the team at Detectify and for many in the community, the king of subdomain takeover is no other than Frans Rosén. 
We can see new subdomain takeover writeups just by searching "Subdomain Takeovers HackerOne". This will show you different ways for takeovers to be done. In this blog post, we will discuss about takeovers of Github, S3 Bucket and Tumblr. But first lets learn about subdomain enumerating techniques. 

# Subdomain Enumeration 

I mentioned most of the tools and website in my previous post, Recon.md. In this post, I will try to go deeper on each of those tools and explain their usage. 

1) HostileSubBruteforcer

This tool was written by Ben Sadeghipour aka (@Nahamsec). It is written in Ruby and in my personal opinion is one of the best tools for takeovers. This tool not only lists out subdomains by bruteforcing them, it also maps out where it points to. Along with that, if the domain throws out errors like `This Github pages does not exist`, `NoSuchBucket` etc, it will print it out in red alert and asks you to check them for possible takeovers. I personally have found some takeovers with this tool. 

2) Sublist3r

This tool is a package of multiple websites' results. It contains subdomains from VirusTotal, ThreatCrowd, DNSDumpster, PassiveDNS and many others. One bad side of this tool is that it might give out false positives. Some websites like `DNSDumpster` update their website after 1 month. Due to this, if a service was updated within that time period, DNSDumpster will take time to show it. Nonetheless, this is a great tool to have on your side. 

*There are also websites that we can disucss about like VirusTotal, but because they are integrated in Sublist3r I am not going to discuss much.*

3) Certificates tool

Some companies like Facebook and Google allow you to check certificates of website under ownership of the company. This will allow you to enumerate many more subdomains. 

https://developers.facebook.com/tools/ct is what I have been using quite frequenly now. It also allows you to subscribe to the domain's alert so that you can get an alert when a new certificate is issued (this could mean new subdomains or just a renewal of certificates). 

# Taking over subdomains

Once you enumerate subdomains, you will have to understand where they are pointing to and the third party services that they use to confirm if they are vulnerable. Sometimes, I have noticed reporters submit some false positives as well. 

I have seen researchers ask about Fastly subdomain takeovers but based on my checks and multiple confirmation from other hackers, I can tell that Fastly subdomain takeover is nearly impossible. Some reporters are still submiting these reports and the companies who get these validate them as correct however, Fastly subdomain takeover AGAIN is not vulnerable. 

In this blog, I will show you taking over a subdomain in website `securify.network`. This is my personal website that I made vulnerable to takeovers. First lets enumerate the subdomains: 

![Subdomains Listed](http://i.imgur.com/wMmsGlP.png "Subdomains Listed")

In the above picture, we see three section: `Sublist3r`, `Vulnerable`, `Bruteforced`. Each of these show subdomains that are good for us to know. The `vulnerable` section is an interesting one because it lists out three subdomains. 

Let us start with the highlighted `tutorials3.securify.network`. Its DNS information is below: 

![DNS Analysis](http://i.imgur.com/qj6DTR2.png "DNS Analysis")

When checking the DNS, we find the CNAME linking to bucket that does not exist. Going to the website also shows that the bucket `tutorials3.securify.network` bucket does not exist. We now go to AWS and create that bucket

![AWS Bucket](http://i.imgur.com/r6Y0bLZ.png "AWS Bucket")

After we create the bucket the website now shows `Access Denied` which means that the takeover was successful. 

Moving on...

For next takeover we can try the Github takeover. Going to the page loads `404 Github Pages not found`. Once we check its DNS we see the following information: 

![Github](http://i.imgur.com/zvEF82Y.png "Github")

We now know that the CNAME is pointing to: `randomuranium238980345.github.io`. We then have to create an user account. In Github for takeover, we need to also have our username to be `randomuranium238980345`. Once we signup, we can create a repositor with the name: `randomuranium238980345.github.io` and in Settings tab, put the Custom Domain to `githubtakeover.securify.network`. After about 5 mins the website should point to the github and we have our second takeovers. 

Next is Tumblr. 

Based on the picture that I listed above, `blog.securify.network` is shown to be vulnerable and it points to domains.tumblr.com as shown below: 

![Tumblr](http://i.imgur.com/thXpShq.png "Tumblr")

Lets go to Tumblr and create an account first. After creating account, go to settings at: https://www.tumblr.com/settings/blog/[blog-name]

![Tumblr](http://i.imgur.com/HmIKrHs.png "Tumblr")

Right below username there is `Tumblr URL`. Click on it and select `Use Custom Subdomain`. It should now show `It's Good` when you press `Test Domain`

![Tumblr](http://i.imgur.com/9Xna2VU.png "Tumblr")

Click `Save` and your subdomain takeover should work. 

There are other services like Zendesk, Desk, Unbounce and many other 3rd party services that can be used for takeovers. The key for takeovers exists in understanding how the third party works and how they setup subdomains for customers.

