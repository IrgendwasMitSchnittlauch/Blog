[comment]: <> (Images for this page will be tagged with "AutomationAccounts-01")
# Azure Automation Accounts Best Practise
> _Published: 2024.08.30_<br>
> _Author: Schnittlauch_

with this post I want to share my personal experience and traps that come along with the Microsoft automation accounts.

## Purpose of Azure Automation Accounts?
When asking Copilot, we will receive the following answer:<br>
`"Azure Automation Accounts allow you to automate, configure, and manage tasks across Azure and non-Azure environments, reducing manual effort and errors. They help you isolate automation resources, runbooks, and configurations, ensuring efficient and consistent management."`

## How does cross access from Azure to OnPrem work?
Azure offers a so called "hybrid worker" extension, which allows Azure to execute code within your environment.
The following MS learn docs is a great way to understand this construct more. [Link to MS learn](https://learn.microsoft.com/en-us/azure/automation/automation-hybrid-runbook-worker)
![Diagram from MS learning docs regarding hybrid workers](https://learn.microsoft.com/en-us/azure/automation/media/automation-hybrid-runbook-worker/user-hybrid-runbook-worker.png)

## Why would you need another best practise? Microsoft's docs are very detailed.
So in general, Microsoft provides many information regarding hybrid workers (prerequisites, ports, etc.) <br>
A huge point, which is definitely missing, is what type of server the hybrid worker should or shouldn't have! <br>
Microsoft developer could say `works as designed`.

## Proof of concept: Elevate yourself within your ADDS with highest priviledges.
Within a short time of working in Azure Automation Accounts I've discovered a (from my pov) huge security issue, where I can elevate me or anyone else with all posible rights in my OnPrem ADDS (f.e. domain admin).

### Walkthrough
Step 1: Create a hybrid worker group with computers that are domain controllers
![Domain controllers in Hybrid Worker Group](https://github.com/IrgendwasMitSchnittlauch/Blog/blob/main/Internal/Attachments/AutomationAccounts-01-01.png)

Step 2: Make sure, that the credentials are set to default ("running in system context(!))
![Default Setting is set to "running in system context"](https://github.com/IrgendwasMitSchnittlauch/Blog/blob/main/Internal/Attachments/AutomationAccounts-01-02.png)

Step 3: Deploy and run a runbook with malicious code
![Malicious code, in this screenshot elevating myself as domain admin](https://github.com/IrgendwasMitSchnittlauch/Blog/blob/main/Internal/Attachments/AutomationAccounts-01-03.png)

**Here you go**. You've elevated yourself and now you are able to do whatever you want in the local ADDS. This action is not even monitored, reported or spotted by CrowdStrike, MS Defender or Azure itself.

## Purposed Changes
OnPrem:
- Whenever possible, create a new VM for hybrid workers.
- Also use the principle of least privilege. Once an attacker has access to your Azure Automation Account with such a hybrid workers config, the game is over.
- Create a separate user to run scripts
- Setup a monitoring which checks for users that are getting added to critical AD groups like domain admins etc.

Azure:
- Limit the users who can access Azure Automation Accounts
- Update you hybrid worker config
- Implement MFA especially for Automation Accounts
- Create a special PIM role assignment for Automation Accounts aswell.

## Quick links and sharings:
> [!TIP]
> **Do not** install the hybrid worker extension on any domain controllers or other **highly critical** systems, since Azure offers the option "run in system context" as **default** option.

---
> **About the author**
> 
> Florian Scholz alias Schnittlauch is a german based Microsoft it system engineer, who loves powershell automation, Azure and Microsoft infrastructure services.
> After work Florian spents a lot of his time as a voluntary firefighter aswell as cooking with his wife.
