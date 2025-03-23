## Introduction

If you are upgrading to 10.1 from a previous version of Zimbra, you will notice a new service called LDS or License Daemon Service. LDS was introduced in the middle of 2024 during the release of 10.1. Some customers who attempted to upgrade and failed to configure LDS properly experienced major disruptions to their Zimbra environment and email services.

This article attempts to help troubleshoot some of the more common issues that customers experienced, how they were triaged, and the steps that were taken to help get our customer’s Zimbra services back up and running. This is by no means a complete list as the scale and customization that Zimbra allows our customers makes it very difficult to duplicate every single failure and provide a remedy that is comprehensive.

Even though they are categorized by error message, some of the troubleshooting and remediation steps can be utilized across several failures. The following steps are simple to implement and might help reduce the amount of time spent troubleshooting and resolving the issue.

## Activation Tips

Some things to keep in mind during activation of the Zimbra License key:

1. LDS should be installed on its own node with port 8080 access to `license.zimbra.com`.
2. A license can only be used on one node at a time.

## Initial Troubleshooting Steps to Verify Your License
The following commands can be used to verify the status, validity, and number of accounts that are activated on your Zimbra server. You should know how many accounts and entitlements were purchased for the key you are trying to activate.

```sh
su - zimbra

# Verify that the key applies the correct number of accounts in your invoice.
# Make sure that you’re not trying to activate more accounts or entitlements than what you have in your invoice.

zimbra@mailbox:~$ zmlicense -p
Current activated license : xxxxxxxxxxxxxx
Activated Product Version : 10.1.6_GA_4744
LDS Device Id : E1QT/yKbM+xtfpelv7Ih
Valid from : 20240918050000Z
Valid until : 20251117060000Z

Feature : AccountsLimit
                Status: Authorized for use
                Total Limit: 50
                Used Limit: 2

Feature : ViewInHtmlEnabledAccountsLimit
                Status: Authorized for use
                Total Limit: 50
                Used Limit: 2

zimbra@mailbox:~$ zmlicense -c
checking license status. . .
Current license code : 539444585329439933, Activation status: License is in grace period
zimbra@mailbox:~$ zmlicense -v
Verifying the signature
License details are valid
```

## Common Error Messages and Solutions

### ERROR: Failed to activate the license

#### Possible Issue: Incorrectly selecting Offline mode when it’s an online license

**Try:**

```sh
zimbra@mailbox:~$ zmlicensectl --service setOfflineMode false
```

#### Clear the LDS Cache

```sh
zimbra@mailbox:~$ zmlicense -a xxxxxxxxxxxxxx
$:~/Failed to activate the license: xxxxxxxxxxxxxxx Reason: LDS / License $:~/Server is unreachable.
```

#### Clean the Work Directory

```sh
zimbra@mailbox:~$ zmlicensectl --clearLicenseWorkDir
```

#### Remove All Files from Store Directory

```sh
zimbra@mailbox:~$ license/store$ pwd
/opt/zimbra/license/store

zimbra@mailbox:~$ license/store$ ls -ltr
   -rw-r----- 1 zimbra zimbra  80 Nov 26 14:14 APV_secured.txt
   -rw-r----- 1 zimbra zimbra  84 Nov 26 14:14 ZPL_secured.txt
   -rw-r----- 1 zimbra zimbra 240 Nov 26 15:03 UCL_secured.txt

zimbra@mailbox:~$ license/store$ mv * /tmp
```

#### Restart LDS

```sh
zimbra@mailbox:~$ zmlicensectl --service restart
```

#### Re-activate License

```sh
$:~/zmlicense -a xxxxxxxxxxxxxx
# Failed to activate the license: xxxxxxxxxxxxxxx Reason: operation denied: License is invalid
```
```
