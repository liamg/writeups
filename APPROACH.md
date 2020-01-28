#### *DISCLAIMER: I'm a software engineer and by no means a trained pen-tester, this is just my approach and collected notes. I do not claim to know what I'm doing. It's probably not the best approach, but it's recorded here as a starting point and mostly for my own reference. Feel free to PR any improvements!* 

# My HackTheBox Machine Challenge Approach

First of all, if you haven't yet signed up or connected to the HTB VPN, check out [this guide first](GETTING_STARTED.md).

## Phase 1: Enumeration

The starting point for a machine challenge is always a single IP address. That being all the information we have, the first stage is - quite sensibly - about gathering information. The pen-testing world calls this stage *enumeration*. 

IMO, the most important thing we can do in this process is take good notes. Make a record of everything we find, whether it be versions of software running, ports open, usernames spotted, interesting files - whatever it is, write it down, we don't know how useful it'll be later in the process.

An undocumented convention of HTB machine challenges seems to be that the hostname of the machine fits the format `NAME.htb` where NAME is the name of the challenge. The first thing I usually do is add this to my `/etc/hosts` against the machine IP address.

Anyway, we can *enumerate* in a number of ways...

### Port Scanning/Service Enumeration

The first thing we want to know is what the host is exposing to us - are there services running that we can exploit? We can try to work this out with an initial port scan. The following scan will attempt to expose open ports along with the services and service versions running on those ports:

```bash
nmap -sC -sV -O [TARGET]
```

Writing the scan results to disk is also a good idea so we can reference them later without having to rescan. Whilst the above is a good starting point, we may also want to run some more advanced scans, such as checking UDP ports. 

This will hopefully give us some information about at least one open port/service. We should record the list of services/versions for later use. Additionally, we can check to see if these versions have known vulnerabilities using [Exploit DB](https://www.exploit-db.com/). If they are, what prerequisites would we need to exploit them? And what would each exploit gain us?

### HTTP Enumeration

If the host is running a web server, there are a few methods for gathering further information. First of all, let's hit the web server in our browser. Is it running a known web application? Is the application open-source? Are there [known vulnerabilities](https://www.exploit-db.com/)? Is it a custom app?

#### VHOST Enumeration

The server may be configured to only serve certain files for one or more given VHOSTs. For example `10.10.10.10` may serve an ecommerce site when we hit it using `shop.somehost.htb`, and a blog site via `blog.somehost.htb`. There are a few ways to spot these.

First of all, if the server is using HTTPS, we could check out the SSL certificate - it may list multiple subdomains the certificate is valid for:

```bash
nmap -p 443 --script ssl-cert [TARGET]
```

Second, we can check the output of error pages. Often we will see a hostname mentioned in the output, even when we're accessing it via IP. 

Third, we can use a VHOST fuzzer. I'll shamelessly plug [scout](https://github.com/liamg/scout) here, but many other (likely better) fuzzers are available:

```bash
scout vhost somehost.htb
```

Finally, we can watch out for mention of VHOSTs in our other enumeration, especially in config files, source code etc.

Once we've found a VHOST, we can add it to our `/etc/hosts` and check it out in our browser.

We can then apply the HTTP fuzzing section to any VHOSTs we discovered.

#### HTTP Fuzzing

In order to discover files and directories that are initially hidden from us, we can use a web fuzzer. [gobuster](https://github.com/OJ/gobuster) and [wfuzz](https://tools.kali.org/web-applications/wfuzz) are pretty cool, and again I'll shamelessly plug my own attempt: [scout](https://github.com/liamg/scout).

What these tools do is fire a huge amount of requests for paths which may exist, and attempt to identify those that do exist by such identifiers as differing status codes, varying content lengths, etc.

Scout also does a couple of extra checks like checking for "backed up" files (like the VIM style `index.php~`.)

```bash
scout url http://target.htb
```

#### Manual HTTP Exploration

There's a whole bunch of further exploration to be done here, such as:
 
 - Checking page source code for mention of further URLs, VHOSTs, systems etc.
 - Any mention of human users - such as an "About Us" page on a company website - perhaps we can use this to guess usernames later - especially if email addresses are included.
 - Looking for product/company names etc. - these can be added to wordlists for fuzzing etc.
 - Look at query parameters being used - what format are they? Could they be vulnerable to SQL/other injection attacks? What if we switch parameters out for different formats/values?
 - Check out any cookies that have been dropped - especially after registration/login flows. Are any cookies base 64 encoded and a multiple of 16 bytes long - checkout [pax](https://github.com/liamg/pax)? Can we tweak cookie values? Is there an injection attack available here?
 - Examine headers - any mention of interesting/vulnerable services?

### Enumeration of other services

There's obviously a whole bunch of other services that could be running here, and it would take forever to mention all of them, but we generally want to connect to each and see what we can find out. For example, connect to an FTP server and see what access we have anonymously, what software and version it is running etc. Is there an old exploitable version of OpenSSH running? Enumerate everything! 

## Phase 2: Take stock

Before we go running down rabbit holes, it's a good idea to take a look at the information we've gathered so far and plan out our next steps. Note all potential areas of attack, and for all potential avenues we should ask what looks exploitable and what advantage would it give us? When we attempt each we'll note success/failure reasons, along with any new avenues that we discovered. If we discover a new resource (e.g. VHOST), we should go back to the enumeration stage and start again with it.

If an exploitation route isn't jumping out at this stage, it's time to go back and enumerate some more, or take some time to read up on the services/technologies you've discovered so far. 99% of this process is information gathering.

## Phase 3: Exploitation

By following this process we should eventually be able to exploit a vulnerability and (usually) gain a shell. 

For common vulnerabilities we can [use metasploit](https://www.ceos3c.com/hacking/metasploit-tutorial-the-complete-beginner-guide/) to exploit them, but I find it's often more educational, satisfying (and sometimes more reliable!) to craft my own exploits for the specific scenario.

One common method of gaining a shell is by exploiting a remote command execution (RCE) vuln and starting a [reverse shell](https://resources.infosecinstitute.com/icmp-reverse-shell/#gref). We essentially start a listener on our machine, then execute a command on the target machine which starts a shell that is bound back to our listener.

The first thing we can do when we gain a shell is check to see if the user we're identified as has the `~/user.txt` that we seek for the first challenge goal. If so, let's paste the contents into the "user" part of the challenge on the HTB site for our first points!

## Phase 4: More Enumeration

Once we have a shell, the next stage is some more heavy enumeration. There are so many potentially interesting things to spot here, so it's best to start with an enumeration script and go from there:

```bash
curl -s https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
``` 

The above script will spit out any potentially interesting anomalies to look into, but is by no means comprehensive. Any finds here should be referred back to phase 1 onward before continuing.

## Phase 5: Privilege Escalation

There are myriad ways to escalate privileges on our way to root. A good starting point is always [GTFOBins](https://gtfobins.github.io/). If we've enumerated heavily enough the escalation will usually make itself known to us by reading through our notes at this point.

Eventually we should be able to escalate to root access and `cat ~/root.txt`. Job done!

Getting stuck at any phase just means we have to go back to phase 1 and work our way through again with any new information we've gathered. Most challenges will require looping through this process many times before the elusive root is gained.

## Resources

Check out this [list of resources](RESOURCES.md) for further information.

## Stuck?

The following are things you can try:

- Enumerate more
- Enumerate harder
- Enumerate again
- Enumerate better
