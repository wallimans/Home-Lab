# Part Four: Managing Group Policy Objects (GPOs)

The Group Policy editor is a crucial tool inside Active Directory Services. Group Policy Objects (GPOs) allow administrators to manage and configure operating system settings, software installation, and user environments across an organization via an easy to use GUI tool. By using GPOs, administrators can enforce security policies, standardize configurations, and ensure compliance with organizational policies across an entire domain rather than configuring each individual user and machine.

Imagine having to crawl through the Windows Registry in `HKEY_CURRENT_USER`, `HKEY_LOCAL_MACHINE`, `HKEY_USERS`, etc. to make policy changes for potentially thousands of computers and users and you will see why the Group Policy Editor is one of our most useful tools in Active Directory. Let's dive into how we can configure some of these rules!

In order to reach our Group Policy editor, start on your Server Manager dashboard, click "Tools", then select "Group Policy Management":

<img width="1440" alt="Screenshot 2024-06-08 at 12 15 41 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/135e2652-0dc8-4ff9-9176-0b2280ac0716">

Here we will see our "Forest"; An Active Directory Forest is the top-level logical container in Active Directory that houses multiple domains, trusts, and organizational units. Click to expand your Forest, then expand Domain, then you can expand your specific server. At the top of this drop down menu you will see "Default Domain Policy". The Default Domain Policy essentially effects every user and every computer on the domain. Below that you can see your various Organizational Units you may have created, in my case `_ADMINS` and `_Users`:

<img width="1440" alt="Screenshot 2024-06-08 at 12 16 36 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/67dbf350-d755-40b7-858e-8370b5b635ee">

Let's go ahead and configure some Group Policy Objects! Right click one of your Organizational Units, I'll choose `_USERS`, and from there we can select "Create a GPO in this domain, and Link it here...". Alternately, if we had already created a GPO elsewhere, we could have selected "Link an Existing GPO..." so you don't have to go through the hassle of recreating identical policies inside each Organizational Unit:

<img width="1440" alt="Screenshot 2024-06-08 at 12 18 18 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/de5ce72f-dc96-4c73-8b92-07f634f9f08d">

From here you will be prompted to give your new policy a name. Go ahead and give it a logical name based upon the type of rule you'd like to create and click "OK". For demonstration purposes I'll just call mine "Example GPO":

<img width="1440" alt="Screenshot 2024-06-08 at 12 21 23 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/10306e83-969d-4334-95c5-0e055e1c9ded">

You will see that our new policy has been created beneath the Organizational Unit we selected, and we are ready to configure the policy. Right click on our new Password Policy and click "Edit":

<img width="1440" alt="Screenshot 2024-06-08 at 12 22 33 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/8aee0a53-7e1d-4a78-be92-6149f15174fc">

This takes us into the "Group Policy Management Editor". Inside this editor on the drop down menu you will see two kinds of configurations, one for Users and one for Computers. Inside those you will notice two different folders "Policies" and "Preferences". The important distinction here is that rules configured in Policies are unchangeable from the users perspective, where as Preferences will set a configuration that is loaded every time a user logs into a workstation, but they can change them for the time being until they log out and back in again:

<img width="1440" alt="Screenshot 2024-06-08 at 12 24 22 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/09455d28-b750-49ce-b43e-289b219999ee">

Inside these Policies and Preferences folders we can click to expand and we will see there are whole host of options we can configure broken into various folders. Clicking to expand "Windows Settings" we can see there are options for Scripts, Security Settings, Folder Redirection, Policy Based QoS, Deployed Printers. Inside of these there are even more options to select until you narrow down to the specific rules you are trying to implement:

<img width="1440" alt="Screenshot 2024-06-08 at 12 25 16 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/31c5f18f-22a0-45f2-bdf1-8c550ef87263">

Let's implement a password policy. Go back to the Group Policy Management dashboard and locate "Default Domain Policy" on the dropdown beneath our domain. Default Domain Policy is especially useful because Policies and Preferences applied to this area are applicable to every user and computer on the domain, not just the specific Organizational Units. Password policies are a crucial step in securing our domain, so we would want to apply these policies to everyone and everything. Right click "Default Domain Policy" and click "Edit":

<img width="1440" alt="Screenshot 2024-06-08 at 12 34 42 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/c81e823f-3065-40c7-9471-37529354696d">

Under Computer Configuration, click to drop down "Windows Settings" > "Security Settings" > "Account Policies" > then click "Password Policy". Here we can see a number of options to enforce password history, age, length, etc. We can see under "Maximum password age" that the default is 42 days. Let's go ahead and double click to select and edit that setting:

<img width="1440" alt="Screenshot 2024-06-08 at 12 40 36 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/e5ee80aa-3996-47e1-8ea3-df6451854efc">

I've went ahead and changed this default to 30 days, which might be more in line with our organizations security mandates. Go ahead and click "Apply" then "OK":

<img width="1440" alt="Screenshot 2024-06-08 at 12 42 51 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/cbc45b2a-a01a-4940-9cb0-3b58b8dd7e90">

And just like that we've configured our password policy! Fairly straightforward stuff, but essential knowledge for working with Active Directory.

Let's show off one more configuration. Back on our Group Policy Management dashboard, let's locate and click our `_ADMINS` OU. On this page we can see a few differrent tabs - "Linked Group Policy Objects", "Group Policy Inheritence", and "Delegation". Let's hone in on the Group Policy Inheritence and click on that. Here we can see that our `_ADMINS` OU has inherited the rules from the Default Domain Policy. Considering this is the OU used for admins, we might not want them to inherit all of the rules that we configure in the Default Domain Policy because the administrtors might need access to things regular users don't have access to. So we can actually right click the `_ADMINS` OU and select "Block Inheritence". This will prevent the administrators from inheriting the rules we've configured across the rest of the domain! 

<img width="1440" alt="Screenshot 2024-06-08 at 12 50 40 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/4784ff9b-a0fb-4aee-9b83-4f5c31bf84a7">

We can now see a little exclamation mark icon next to the `_ADMINS` OU, demonstrating that inheritence is blocked. On the other side of this coin, we can right click to select the Default Domain Policy and select "Enforced", which will require absolutelty everyone in the domain to follow these rules:

<img width="1440" alt="Screenshot 2024-06-08 at 12 53 53 PM" src="https://github.com/wallimans/Home-Lab/assets/170472167/fb3cadbb-a6ed-425a-94b6-90e2266f2f89">

Good stuff! There are so many rules and configurations to explore, so it's worth clicking around and seeing where different things are located for future reference. Some of the most useful configuration options would be password policies, account lockout policies, audit policies, security options, software restriction policies, Windows update polcies, etc. Hope this demonstration has been helpful!
