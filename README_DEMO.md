# Intro

Explain we won’t be talking about provisioning today, but how we can do it on VMWare, public cloud, bare metal of course etc.
1. Go to Hosts -> Content Hosts and show all the hosts
2. Go to Content -> Lifecycle -> Lifecycle environments and explain paths, and how hosts are associated with them
3. Explain the Lifecycle Environments.  Explain that we have versions for these: dev/qa/prod, but they could have 
    a. Database servers
    b. Web servers
    c. etc.
4. Go to Content -> Lifecycle -> Content Views.  Show how the software is versioned and promoted through the lifecycle environments

# Applying a patch (errata)

## Demo

1. Go to Content -> Content Types -> Errata
2. Filter on RHEL 7 server RPMs
3. Explain Applicable and Installable
5. Select 100 per page
6. Click on the "Applicable" checkbox
7. Find an installable erratum for all 3 hosts: e.g. RHSA-2023:7743 Low: curl security update
8. Note the date is 2023-12-12, before the first filter end date created
9. Click the Apply Errata button to install it and fix the issues on all impacted hosts
11. Click and show the log for one of the hosts

## Optional task: Install an emergency update by creating incremental content views

1. Go back to the Errata view, click the installable checkbox, and select the erratum RHSA-2024:2004 Important: kernel security and bug fix update
2. Click the "Apply Erratum" button
2. Note that it is currently applicable to all 3 hosts, but only installable on 1 host. You can see this by ticking the box to "Only show content hosts where the errata is currently installable in the host's Lifecycle Environment".
3. Untick that box
4. Click Next, and you will be asked to create an incremental CV for RHEL7_QA and RHEL7_Prod
5. Tick the " Apply Errata to Content Hosts immediately after publishing" and then click "Confirm".
6. Updating the CVs and installing the erratum takes around 10 minutes, so you can move to the next demo and check the task via Monitor -> Jobs later if asked.


## Optional task: Install an erratum that is just applicable to one host

1. Go back to the Errata view, click the installable checkbox, and select  	RHSA-2024:2004 	Important: kernel security and bug fix update 	Security Advisory - Important
2. Click the Apply Erratum button and select the host on which the erratum is installable (ie the one in the RHEL7_Dev LCE)
3. Note that you can click the check box "Only show content hosts where the errata is currently installable in the host's Lifecycle Environment."
4. Go to content views to explain why the content is not installable on the Prod and QA hosts

# Installing/Updating a package

1. Go to the Hosts -> Content Hosts
2. Select all 3 RHEL hosts
3. Click the drop-down next to the "Select Action" button, and select "Manage Packages"
4. Enter “nano” package name (editor) (or screen, vim-enhanced, bash-completion)
5. Click the drop down next to "Install" and select "via remote execution"
6. Point out that you could do an “update all packages” but it would take a long time, so we won’t be doing that!
7. Click Done
8. Watch the install, which completes quite quickly

# Check if a reboot is required

1. Go to Hosts -> All Hosts
2. Add filter `trace_status=reboot_needed` via the search box, and press "Search". If no results come up then no systems need a reboot.

Refer to [https://access.redhat.com/discussions/3175851](https://access.redhat.com/discussions/3175851)

# Run a remote job

1. Go to Hosts -> All Hosts
2. Select all 3 hosts
3. Select action -> schedule remote job
4. Fill in text: `hostname; uptime; whoami`
5. Click "Submit"
6. Wait for success
7. Click on a host and check the output

# Demonstrate system roles

Using instructions from: 
[https://www.redhat.com/en/blog/satellite-host-configuration-rhel-system-roles-powered-ansible](https://www.redhat.com/en/blog/satellite-host-configuration-rhel-system-roles-powered-ansible)
and
[https://www.redhat.com/en/blog/advanced-ansible-variables-satellite](https://www.redhat.com/en/blog/advanced-ansible-variables-satellite)

Preparation steps:
1. Configure -> Ansible Roles
2. Press the button "Import from satellite.example.com"
3. Check the "Select all" box and click "Submit"

Execution steps:
1. Open a terminal CLI session on node1
2. Start with Hosts -> all hosts view
3. Explain that all three hosts have been associated with the same host group
4. Click the "All hosts host group" you can see in the "Host Group" column
5. Click Ansible roles
6. Add rhel-system-roles.timesync if not already added
7. Submit, and explain that now we need to check the configuration
8. Click on Configure -> Ansible -> Roles and find the "rhel-system-roles.timesync" role. 
9. Click the "Variables" button, and then the timesync_ntp_provider parameter. 
10. Click the "Override" check box, and paste the line below exactly as is into the "Default Value"  
`chrony`  
11. Click "Submit".  You will now see that the value has a flag next to it to tell us that it has been overridden.
12. Click on the timesync_ntp_servers parameter. 
13. Click the "Override" check box, and paste the line below exactly as is into the "Default Value"  
`[{"hostname":"0.au.pool.ntp.org","iburst":"yes"},{"hostname":"1.au.pool.ntp.org","iburst":"yes"}]`  
14. Click "Submit".  You will now see that the value has a flag next to it to tell us that it has been overridden.
15. Go back to hosts -> all hosts
16. Alt tab to CLI of node1
17. Run the following commands on the host  
```
more /etc/chrony.conf
chronyc sources
```
18. Alt tab Back to GUI
19. Ensure the hosts are selected
20. Action -> Run all Ansible roles
21. Watch it execute on a specific node
22. Go back and check all completed successfully
23. Check on the CLI again  
```
more /etc/chrony.conf
chronyc sources
```
24. Summarise how you have applied a configuration at scale across a group of hosts

# Show SCAP compliance locally

1. Go to Hosts -> Policies
2. Click on Dashboard
3. Explain that this is better for a truly disconnected environment… but ask how disconnected they need to be.
4. Can switch to console.redhat.com as needed and explain that there you can run remediation here if you have a smart management subscription

Alternatively, follow the detailed instructions located in the exercise here:
https://github.com/ansible/workshops/tree/devel/exercises/rhdp_auto_satellite/1-compliance

# Demonstrate Insights

Demonstrate this via console.redhat.com because our lab hosts are not connected to Insights.

# Other capabilities to discuss

- Tailoring SCAP policies
- Generating reports (Monitor -> Report Templates)
    [https://www.redhat.com/en/blog/getting-started-satellite-65-reporting-engine](https://www.redhat.com/en/blog/getting-started-satellite-65-reporting-engine)
- Remote execution jobs


