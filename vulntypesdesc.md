Web application security or simply said, website security is widely known topic in the current world of cybersecurity. We often hear about hacks that stole millions of SSNs and created massive damage for the company. The question that emerges after each of those events is how big of a threat is cyber attack in our world? Medias start to jump in conclusions and bring out headlines about how cyber attack could be deadly for us all. While all these happens, not many cover on how this can be prevented. This is when the role of security teams, pentesters and security analyst comes up. Everyday cyber attacks happen and are prevented with work of these individuals as they test out applications. Testing application of a website is not like using a wand like a wizard (while that might cool to do). It requires research, work and sometimes overcoming frustration. When I pentest applications, I work on finding low severity issues to critical severity issues. In this post, I will discuss about some common mistakes I have seen during source code reviews or just a direct blind test (my way of saying that I test apps without source codes sometimes).

As a security researcher, I work to find vulnerabilities that range from simple cross site scripting to sometimes remote code executions. One common issue I have seen in websites of many startups and sometimes even multi-billionaire companies is how they handle file upload:

# Content mishap when uploading files

When you try to upload an image to a site, a certain POST request is usually made to the application. This way, the content of the file is uploaded to the server and stored there. To prevent XSS from file names, some companies change the name of the files and make it suitable for them. Now that is a different topic of bypass that we can cover later. Right now lets dive into how file uploads can be risky. Lets go with an example:

You own a website called `superresume.com`. In this site, you allow recruiters to hire prospected employees by checking their resumes. An employee who is looking for a job uploads a PDF file and that is stored in your server with a random name. The employee can also view their own resume and see how it looks on your website. That looks good so far right? Well what if the employee can upload another file type to your server. When a file post is made, there is two things that must be checked always: Content-type and file extension. During my pentest I have notices scenarios like this:

In your superresume.com, someone uploads a HTML file, but then they see the alert: "Sorry the file you uploaded is not supported. Please upload a PDF file". That looks good so far until we start digging deeper into this. To do such test, I use BurpSuite ( a proxy application that allows to proxy http/https request to the remote host through it ). This allows you to modify the request and see how it works. To start out, I upload a PDF file; then intercept the request by Burp. Once that is done, I change the file name which might have been [randomname].pdf to [randomname].html. At the same time, I do not change the Content-Type of the "pdf". The server will now check if the mime type of the file is a pdf. Once it sees it confirms the mime type, it uploads it to the server. Once that is done, there is about 99.99% chance that I uploaded a HTML to your site and now I can use it for multiple purposes. This can get even more dangerous, if your server supports a PHP file. If that is the case, a small PHP shell can allow attacker access to your whole server.

Usually to tackle this, you should do multiple things: 1) Check both extensions and file types, 2) If there are modules out there, convert the file to a diff PDF that way if I even put a PHP script I will not be able to do much. Also make sure you append .pdf to the file no matter what.

# Insecure Direct Object Reference vulnerability

In short this vulnerability is called `IDOR`. Through this, a user can get access to unauthorized information about another user or change information about another user without any form of verification. This can be dangerous in many cases. During my pentest, I have exploited these issues to find confidential information which included Medical records, social security numbers etc.

Here is how this scenario might play in real life: (This example is a real scenario that happened)

Lets again go with a recruiting site and call it (again) superresume.com. In this site, you allow user to update their accounts and add informations about where they graduated and what major they studied. You are making it easier for recruiters in a sense that they do not have to always check the resume because the same info is in the profile description. That is pretty neat of you :)

Lets now move to how the request is played:

Suppose the POST request looks like this when you press the "Save" button in your account:

```
POST /useraccount/edit

Host: superresume.com

[Cookies and info and headers]

name=[First & Last Name]&userid=[int value of user id]&[and other parameters like collegename, major etc].
```

The main part to notice here is the `userid` parameter. This might usually be the `userid` of your account in that site. So now lets create another account as well to test this IDOR. Lets say user account 1 has user id: 45890 and account 2 has user id: 45891. Edit the account 45890 and send the request to Burp. In Burp, lets send it to repeater and change the userid parameter to have 45891. Does the account information in user 45891 also change? If that happens you can now successfully changed the profile account of another user.

In some cases of shopping sites, we need to see how our previous orders are displayed. For example, lets say you bought a beautiful sofa for your home from site bestfurniture.com. When you go to the site and click on your order, the link might look like this: bestfurntiture.com/api/order?orderid=4958 and then it shows your order info like who placed it, where your house is (mailing address) and other private information. What happens if you change the orderid to 4957? This could sometimes spit out the information of another order of another user. If you start bruteforcing these id and just run it through Intruder in Burp, you can grab tons of private informations of multiple users.

# CSRF attacks

These kind of vulnerability could differ from high severity issue to low severity. Sometimes a small CSRF can be chained for account takeover. One negative side of CSRF is that the user must visit the link with your CSRF exploit and also that they must be logged into the vulnerable site. Lets checkout the account takeover scenario:

CSRF usually happens when the website does not authenticate if the request is made directly from the form and by the user instead from an external site. To prevent this, websites utilize CSRF tokens. This can be used to detect if the request was from a legitimate form or not.

Usually you should be able to notice if a web app is vulnerable or not by looking at the request that it sends. If there is no token either in the POST request or in the headers then it is extremely likely that it is vulnerable.

For account takeover, one of the most simplest way of attack is changing the user's email. If the /account/edit endpoint does not require a password when saving content, then you can create a form that changes the user's email. Once you have that done, you can use that email to reset the password of the account and soon after that you get full account access. Here is the scenario:

User A has account in superresume.com and the profile section there is vulnerable to CSRF

User B sends a perfectly legit link with hidden form that auto-submits on page load. In that form, it changes the user A's email from usera@emailprovider.com to shadyemail@emailprovider.com. Once User A opens the website their email changes. User B can then request a password reset which likely goes to shadyemail@emailprovider.com and he gets full access to the account.

In part 2, I will discuss about XSS, RCE, SQLi and other bugs as well. Part 3 will include examples of business logic flaws.
