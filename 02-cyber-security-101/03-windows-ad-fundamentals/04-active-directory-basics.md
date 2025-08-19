# Active Directory Basics

## Task 1: Introduction

Microsoft's Active Directory is the backbone of the corporate world. It simplifies the management of devices and users within a corporate environment. In this room, we'll take a deep dive into the essential components of Active Directory.

### Room Objectives

In this room, we will learn about Active Directory and will become familiar with the following topics

- What Active Directory is
- What an Active Directory Domain is
- What components go into an Active Directory Domain
- Forests and Domain Trust
- And much more!

### Room Prerequisites

- General familiarity with Windows. Check the
  [Windows Fundamentals module](https://tryhackme.com/module/windows-fundamentals)
  for more information on this.

***Answer the questions below***

Click and continue learning!

***Correct answer: No answer needed***

## Task 2: Windows Domains

Picture yourself administering a small business network with only five computers and five
employees. In such a tiny network, you will probably be able to configure each computer
separately without a problem. You will manually log into each computer, create users for
whoever will use them, and make specific configurations for each employee's accounts.
If a user's computer stops working,
you will probably go to their place and fix the computer on-site.

While this sounds like a very relaxed lifestyle, let's suppose your business suddenly
grows and now has 157 computers and 320 different users located across four different
offices. Would you still be able to manage each computer as a separate entity, manually
configure policies for each of the users across the network and provide on-site support
for everyone? The answer is most likely no.

To overcome these limitations, we can use a Windows domain. Simply put, a Windows domain
is a group of users and computers under the administration of a given business. The main
idea behind a domain is to centralise the administration of common components of a
Windows computer network in a single repository called Active Directory (AD).
The server that runs the Active Directory services is known as a Domain Controller (DC).

![domain-controller](domain-controller.png)

The main advantages of having a configured Windows domain are:

- Centralised identity management: All users across the network
  can be configured from Active Directory with minimum effort.
- Managing security policies: You can configure security policies directly from
  Active Directory and apply them to users and computers across the network as needed.

### A Real-World Example

If this sounds a bit confusing, chances are that you have already interacted
with a Windows domain at some point in your school, university or work.

In school/university networks, you will often be provided with a username and password
that you can use on any of the computers available on campus. Your credentials are valid
for all machines because whenever you input them on a machine, it will forward the
authentication process back to the Active Directory, where your credentials will be
checked. Thanks to Active Directory, your credentials don't need to
exist in each machine and are available throughout the network.

Active Directory is also the component that allows your school/university to restrict you
from accessing the control panel on your school/university machines. Policies will
usually be deployed throughout the network so that you don't have
administrative privileges over those computers.

### Welcome to THM Inc

During this task, we'll assume the role of the new IT admin at THM Inc. As our first
task, we have been asked to review the current domain "THM.local" and do some additional
configurations. You will have administrative credentials
over a pre-configured Domain Controller (DC) to do the tasks.

Be sure to click the Start Machine button below now, as you'll use the same machine for all tasks. This should open a machine in your browser.

***Start Machine***

Should you prefer to connect to it via RDP, you can use the following credentials:

|Username|Administrator|
|:-:|:-:|
|Password|Password321|
|IP (RDP)|MACHINE_IP|

Note: When connecting via RDP, use THM\Administrator as the username to
specify you want to log in using the user Administrator on the THM domain.

Since we will be connecting to the target machine via RDP, this is also
a good time to start the AttackBox (unless you are using your own machine).

***Answer the questions below***

In a Windows domain, credentials are stored in a centralised repository called...

***Correct answer: Active Directory***

The server in charge of running the Active Directory services is called...

***Correct answer: Domain Controller***

## Task 3

### Subheading

***Answer the questions below***

***Correct answer: No answer needed***

## Task 4

### Subheading

***Answer the questions below***

***Correct answer: No answer needed***

## Task 5

### Subheading

***Answer the questions below***

***Correct answer: No answer needed***

## Task 6

### Subheading

***Answer the questions below***

***Correct answer: No answer needed***

## Task 7

### Subheading

***Answer the questions below***

***Correct answer: No answer needed***
