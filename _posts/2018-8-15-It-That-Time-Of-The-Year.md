---
layout: post
title: It's that Time of the year.
comments: true
---
Murrica Week!


I Alway's find it interesting every time I get to play Army how much that goes into getting our environment prepared for the field. Dispite how much we do in preparation moore's law seems to always strike. What can happen will happen.


### Stay Posted: I will try to update this every few days.

##What I do in the reserves:
I am an automations NCO so I am in charge of all the server deployment, making all of the magic happens. I manage our Domain Controllers, Exchange, File Shares, SCCM Servers, Nessus, Group Policy, Network Equipment, and prefunking the magic for many other peices of Equipment/Software for Operational Success.


_Prefunking the Magic Week_:

##Day 1: Sunday
First few minutes of walking into the office, I get bombarded with stuff is broken by our Field Service Engineers, and Find out our Domain Controllers were built with only 80 gigabytes of storage, which was far too small to handle updates let alone them trying to take a snapshot of Active Directory. So our logs filled up the drives and we have to expand them.

Meanwhile I get pulled into a meeting and have to juggle pre-planning meeting so we can set the agenda for the week.
TLDR; Get everything working.

We managed to get carved out a work area space so we arent all 5 of us are crowded in a small server closet. Which will ultimately help us prefunk the magic without stepping on eachothers toes.

Worked with our Junior Network technician get network access into our Medium size office, that we can work out of. As well as take an inventory our Computer Taskers. So we can communicate with our Certifying Unit.

###Our Tasks to Complete the First Week:
* Prepare 5 Switches for Rapid Deployment when we get on site.
* Coordinate with the Site NOC for Network Connectivity.
* Fix all of our "Servers"
* Prepare 22 Systems to go on our domain.
* Repair Active Directory
* Establish DNS Connectivity to our Parent unit
* Repair Exchange (No Access to Portal)
* Repair File Server (Yes its broken)
* Repair SCCM Server for Patching
* Repair Establish TTP Book (Totally not happening)
* Repair Sharepoint
        
##Day 2: Monday  
Its all broken and Sunday wasnt that productive.  
  
I spent most of the day helping out our Junior Network Technician working on figuring out when the network wont pass data. We run a hybrid Dell/Cisco and for some odd reason setting VLans on Dell Layer 3 Doesnt like to talk cisco switches passing Vlan Data. So instead we moved our Vlan Definitions to our Cisco Router, breaking our recommended configuration. But it works.  
  
Finished Expanding our our Domain Controllers, which our Field Support Engineers noted that their OU was missing. I spent most of the day trying to recover the data. So my options were to Restore from a Snapshot (The same snapshot that filled up the DC's and helped to corrupt the Domain Controllers in the first place), or to try to recover what was already in place.
In place repairs kept saying that Active Directory was not corrupt and visibly we could see corruption in the OU. I ended up pulling out my powershell Arsenol and was able move all of the AD-Objects out of the corrupted OU.  
  

`  
$Objects = Get-ADOBject -Filter * | ? DistinguishedName -match "OU=CorruptedOU,"  
$Objects | % { Move-ADObject $_.ObjectGuid -TargetPath "YourPathHere" }  
`
  
##Day 3: Tuesday  
Started creating ADUsers and Imaging Computers. It was pretty straight forward using powershell. Created a Script to build out User's from a CSV that helped us automate future users because I am certain we will have 20+ users to add in addition to what we already have going.  
  
###Troubleshooting DNS  
Something is totally wrong with our DNS we cant add roothints or Conditional forwarders to our domain. I spent the day off and on troubleshooting this issue. Powershell for some odd reason isnt helping. Stubzones work right now as a work around for nothing working, however repeated Zone Transfer requests from an Unknown DNS Server will probably get us locked down so its not a good long term option.  
  
##Day 4: Wednesday  
###Troubleshooting Network:  
Yeah so our JR Network guy was having issues so I spent most of the day off and on trying to figure out one PEBKAK issue after another. I do have to hand it to him given that he knew nothing a few days ago He has gotten a lot more capable in a very short amount of time. Still want to strangle him though.  
  
###Troubleshooting DNS:  
Yup, still at it today. Struggling trying to figure out what happend to our DNS. Stub Zones but is still the wrong answer. Finally a breakthrough halfway through the day. I figured it out. Our DNS Server was initially configured as a ROOT DNS Server. So after Deleting the ROOT Zone we were finally back in Business. Unfortunatly for us we needed to grab the RootHints and forwarders from our Higher. Long story. It works now.  
  
###Troubleshooting FileServer:  
For some odd reason we cant hit our File Server from the Client Machines. It took a little bit of time, but NTP on the Domain Controllers and the End Clients were completely foobar. So I am going to spend the next Two Days fixing it. VMWare VSphere is terribly slow.  
  
###Troubleshooting Email Server:  
I was talking with a Warrant Officer to confirm we were live and able to pass email traffic. I double checked our configs and hit send. It went to my drafts folder. What happened?  
  
The email server Transport Service wasnt running, and refused to turn on. Dependencies I missed after fixing most of the service after somebody decided that it was wise to rename the server to standard after it was already stood up. Finally got transport back up and email sent. I will check email trace logs in the morning to fix it  
  
##Day 5: Thursday  
To Be Continued!!