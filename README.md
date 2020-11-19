# Phishing Email Case Study

Vulnerabilities are more than open ports or unpatched operating systems. The weakest link in the world of technology is people. The “human OS” doesn’t have industry-grade anti-virus or constant security updates. But that doesn’t mean all hope is lost. Cybersecurity awareness training is a must for any business today and everyone should take it seriously.

The best way to train employees is by using real examples and helping them identify the common signs of social engineering. Sometimes, these phishing/vishing attempts are quite easy to identify, while other times they leave you scratching your head, questioning their legitimacy.
In this project, I will be using a series of phishing emails that will target a known individual to retrieve the password to a password-protected excel spreadsheet.

# Q: Why Can’t We Just Crack the Password?

Well, just because you asked, let us try.

Older versions of MS Office use less secure encryption and the password could’ve been cracked easily, had my target been using Office 2010, for example. However, this spreadsheet was created using Office 2016, which uses AES-256-bit encryption. This would take an unfeasible amount of time to brute force, especially considering I do not know anything about this person to create wordlists for Hashcat to use.

Even if I had every PC on the planet working together to brute force the password hash, it would take over 12,000  trillion trillion trillion trillion years. I don’t have that kind of time.

But let’s say our target uses a commonly used password, perhaps one found in the LinkedIn leak from 2012. There were an estimated 117 million unique passwords leaked. If we think there will be a matching password from the leak, we can use these as a wordlist in a Hashcat dictionary attack.

A smaller wordlist, rockyou.txt, commonly used by Hashcat users and Kali Linux, contains over 14 million unique passwords and is a go-to for most password crackers. It takes my secondary PC, running an Intel i5-9600k and an AMD Vega 56 GPU took 31 minutes and 24 seconds to run through the rockyou wordlist in Hashcat mode 9600 (Office 2013/2016 hashes).

This means attempting a straight dictionary attack with a larger wordlist like CrackStation’s wordlist, which contains 1.49 billion passwords, would take an estimated 53.2 hours and there are no promises it will even work. But why not, screw it. Let us try.

# Straight Dictionary Attack
After starting the Hashcat operation, it’s estimated 43 hours until completion, and it ended up taking __ hours. This did not end up cracking the hash. But it did use up almost $5 worth of electricity.

Now we are pretty much out of options here, and brute force is not an option. Here’s why:

The hashpower of my system on mode 9600 is 7666 H/s, which is not great by any means. Let’s say the target is using an 8 or 10 character password containing lowercase, uppercase, numbers, and symbols (!@#$?). This adds up to 67 possible characters.

-         67^8 = 406 Trillion
-         67^10 = 1.82 Quintillion

Cracking these using the given possible characters would take a while. Let’s see how long.

-         8 Character: 406 Trillion / 7666 / 60 / 60 / 24 / 365 = 1680 Years
-         10 Character: 1.82 Quintillion / 7666 / 60 / 60 / 24 / 365 = 7.5 Million Years

As you can tell, this is not going to work. But maybe we can burn some more money and rent some more GPUs on AWS?

# Well… Maybe We CAN Crack the Password?

Before we fabricate a dream in our head that cloud GPUs will solve all of our problems and crack the password hash like an egg, we need to find out how much power we can get out of the GPUs and do some napkin math to see how much this would take and how much it would cost.

On AWS, two GPU instances caught my eye: p3.16xlarge and p4d.24xlarge. Each uses eight Nvidia GPUs.

-         p3.16xlarge: 8 x Tesla V100, 163.5 kH/s (-m 9600), 10.27 kH/$
-         p4d.24xlarge: 8 x Tesla A100, 228 kH/s (-m 9600), 7.125 kH/$

Although the A100s are a bit faster, the hashrate-to-dollar ratio is better with the V100s.

Running the P3 instance would crack the password 21.3 times faster than my Vega 56 GPU alone. Honestly, I was hoping for better. But, the new calculations:

-         8 Character: 80 Years
-         10 Character: 357,142 Years

Sure, it’s better, but who knows if I’ll be alive in 80 years. Plus, I don’t feel like spending $11.15M on cracking this password.

# Maybe not. What next?

Exploiting the human operating system. Requires no money – just time to perfect the exploit.

Unfortunately, this won’t be as easy as tricking your grandma to click a link or calling and pretending to be Microsoft support. The target has a background in tech, but not security. So maybe, just maybe, we can pull this off. But the emails have to be believable and sequenced correctly at the right time to avoid tipping off the target.

# Designing the Social Engineering Attack
The target is very protective of this spreadsheet, so if they caught wind that the spreadsheet was accessed by another user, they might act impulsively. This is good for us. But I’m not going to cast a phishing line blindly and expect them to bite. I need to create a false narrative to convince the target that what seems to be happening is indeed happening. I’m going to do this by creating separate email notifications from Microsoft to the target, each a few minutes apart.

-         Email #1: Microsoft email notification that the target’s account password has been changed

The target will likely think nothing of this or completely ignore it. But just in case, all the links in the email will go to 404 pages on the Microsoft website.

-         Email #2: Microsoft email notification – someone is trying to access “spreadsheet.xlxs”

This email comes 3 minutes after the previous one. Now, the target might be at least slightly concerned. “My password rest and someone accessing my spreadsheet? WTF.” Either they panic and click the link in the email, or again they ignore it.

-         Email #3: Microsoft email – arobinson@_.com has made changes to spreadsheet.xlxs

This email should be the tipping point. The target looks at this third email panicked, heart beating fast, and thinks about how bad this situation is. Of course, they need to see what changes were made, so they click the link in the email to see the changes.

-         Email #4: Optional, someone made changes to another sheet

Just in case the last one didn’t hit home, this should make the target go “wtf how is this person editing all my sheets”

# The Exploit

The emails are just the payloads, but what’s the exploit? The links in emails 2-4 will direct the target to a website, hosted by me, which looks near identical to a Microsoft site. To “see the changes” of the spreadsheet, they will have to enter their sheet password first. This is where we snag the password. However, we only have one shot at this, so we need to be sure we get the password. To help ensure we do, the first three password inputs will show as “invalid password” and force the target to renter the password. This helps for two reasons: if the user doesn’t believe that the page is legit and they enter a fake password, it will still kickback with “invalid password,” making them think “oh, maybe this is real” and they enter in their actual password. Also, if the user makes an honest typo, they will still have to renter it a couple more times, making it less likely we get the password mistyped. After the fourth password attempt, they will be dumped to a 404 page of some kind, left confused and agitated. Don’t worry, they will be okay, but their spreadsheets won’t be.

(TO BE CONT, still working on screeshots and emails)
