---
title: "SharePoint solutions as a spyware"
date: 2024-08-05T17:40:00-04:00
author: "Kinga Kazala"
githubname: kkazala
# don't change
categories: ["Community post"]
# link to the thumbnail image for the post
images:
    - images/spfxspyware.png
# don't change
tags: ["Entra ID", "Microsoft Entra ID", "SharePoint Framework (SPFx)", "SPFx"]
# don't change
type: "regular"
---

## SharePoint solutions as a spyware - series

1. **SharePoint solutions as a spyware** - this article (updated 26.08.2024)
1. [Trust but verify](./../spfx-solutions-as-spyware-part2)
1. Microsoft Cybersecurity Reference Architecture (MCRA) - coming soon

> This article originally appeared on https://dev.to/ site, [SharePoint solutions as a spyware](https://dev.to/kkazala/sharepoint-solutions-as-a-spyware-667)

Have you ever considered that the SharePoint solutions you are installing could actually be spyware? It's surprisingly easy for this to happen.

## Safe by design?

SPFx solutions are essentially functioning as full-trust code within the SharePoint environment. The APIs accessible by these solutions include the SharePoint REST API, public APIs, and APIs secured by Azure AD (such as Microsoft Graph and Azure services).

**Public APIs**
These **do not require authorization and cannot be blocked**, allowing SPFx solutions to **freely send data to external APIs**. This can occur without the need for administrator or user consent, thus posing potential security risks if not properly managed.

**SharePoint REST API**
**No additional permissions or approvals** are necessary for SPFx solutions to use this API. SPFx solutions can access all SharePoint data that the user has permissions to, **without restrictions.**

**Microsoft Graph and Azure APIs**
These APIs provide access beyond SharePoint, extending to other Microsoft 365 and Azure services.
They are called using service principal (SharePoint Online Client Extensibility ) acting on behalf of the user (delegated permissions) and **apply to all SPFx solutions in the tenant.**
**Malicious software** can exploit this approach by **accessing endpoints** that may have been **previously requested by other solutions, and are already approved.**

## Vector of attack

Malicious actors, including hackers or state actors, may exploit the access capabilities SPFx solutions have to Microsoft 365 environment.

**One potential attack vector** involves the deployment of **seemingly legitimate solutions**, such as web parts or application customizers, across a site or throughout an entire tenant. These solutions might offer genuine functionality that appears to provide value to users, thus gaining trust and widespread usage.

However, these seemingly benign components can be designed with malicious intent. **In the background**, such SPFx solutions could **exfiltrate data and transmit it to external API** controlled by the malicious actor.

**Another concerning attack vector** involves the **theft of user's access and refresh tokens**. This method is both simple to execute and highly dangerous, as it can grant attackers ongoing access to the victim's data without further interaction from the user.
In SharePoint Framework, **acquiring access/refresh tokens** can be as simple as **executing a few lines of code**.
The token represents the user's credentials and permissions within the system, allowing to craft API requests using only permissions that have been granted to the service principal.

Once the token is obtained, the **solution can quietly transmit it to an external endpoint** controlled by the attacker. This can be done without the user's knowledge or consent, as the operation can occur in the background during the brief period the user interacts with the solution.

The attacker, now in possession of the authentication token, can use it to authenticate from an external machine or a serverless function. this approach does not require the user to remain engaged with the malicious solution. The token can be used repeatedly, allowing the attacker to access data continuously without further user interaction. This can lead to **prolonged and undetected data exfiltration**.

## Data Exfiltration impact

**Stolen files** represent a significant threat as they can be utilized for financial gain, competitive advantage, extortion, social engineering, public exposure, etc.

-   Proprietary business information, trade secrets, and intellectual property can be sold to competitors or used to gain a competitive edge in the market.
-   Sensitive operational data can be altered or deleted to disrupt business processes, causing financial and reputational damage.
-   Information gleaned from stolen files can be used to craft convincing phishing emails, tricking recipients into revealing further sensitive information or downloading malware.
-   Stolen files can be released publicly, causing reputational damage and legal issues, especially if they contain sensitive personal data or confidential business information.

**Stolen emails and calendar events** can be valuable tools for hackers to exploit in various malicious way.

-   Hackers can use stolen email addresses to send deceptive emails that appear legitimate, tricking recipients into clicking malicious links or providing sensitive information. By having access to past emails, hackers can craft highly personalized and convincing phishing emails to target specific individuals, increasing the likelihood of success.
-   Emails can contain sensitive information such as personal identification numbers, financial details, and confidential business information, which can be exploited for identity theft, financial fraud, or corporate espionage.
-   Detailed calendar events can provide insights into an individual's schedule, making it easier for hackers to impersonate colleagues or associates and execute social engineering attacks.
-   Knowing the locations and times of meetings can facilitate physical security breaches, such as unauthorized access to secure areas or stalking.
-   Calendar events can reveal business strategies, upcoming projects, or mergers and acquisitions, which can be valuable for corporate espionage.

**Personal data** like email addresses and phone numbers are commonly traded commodities.

-   As of 2024, the average price for stolen email addresses ranges from $10 to $80, depending on the associated information.
    The value of such data on the dark web reflects its utility for cybercriminals, who can use it for various malicious activities such as phishing, identity theft, and unauthorized access to accounts.
-   Even if an organization publishes a list of all employees, not all the data available in Azure AD may be made public. Additionally, certain employees’ data might not be disclosed due to court orders aimed at ensuring their security. The exposure of their contact information could pose a serious risk.

## Roles and Responsibilities

### SharePoint Administrators

In a SharePoint environment, **solutions are installed** to a **tenant-level app catalog** and require **approval from a SharePoint Administrator or** someone with a **higher-level role**. While **users may request apps**, they **do not** have the ability to add custom apps directly or **approve them**.

This **approval process is far more than just a formality** and must be taken extremely seriously. The decision to approve a solution can have significant implications for the security of the entire tenant.

Administrators should carefully evaluate every solution before granting approval to ensure it doesn't introduce potential security risks.

### Site Owners

**Site owners may request** creation of a **site-level app catalog**, which must be enabled by a SharePoint Administrator. It provides Site Owners with ability to **add and deploy SharePoint solutions to their site without requiring further approvals**.

While the **attack surface is smaller**, since the solution is confined to a specific site, all users of that site may still be affected.

This **shift in responsibility is significant**. By enabling a site-level app catalog, the SharePoint Administrator **effectively transfers the responsibility for solution review and approval**, along with the **security of users and their data, to the site owner**.

This is a pivotal moment, and site owners should be instructed about the potential risks associated with installing SharePoint solutions.
They need to be **supported in understanding and applying best practices to maintain security**, ensuring that the solutions they choose do not inadvertently compromise their site or the broader organization.

Consider obtaining **permission** from the site owner to **conduct regular audits** of the installed solutions and the permissions they request on these sites.

## Mitigation strategies

To effectively mitigate the risks associated with installing potentially malicious SPFx solutions, organizations should consider a multi-layered approach that combines best practices for sourcing applications, monitoring, and logging.

### Source Solutions from Trusted Sources

#### Official AppSource Marketplace

The safest approach is to install solutions exclusively from the official Microsoft AppSource marketplace. These solutions undergo a vetting process, reducing the risk of malicious behavior. However, it is crucial to acknowledge that even vetted solutions should be subject to internal review and monitoring.

#### Internal Development Teams

If your organization has in-house developers, creating custom SPFx solutions internally ensures that you have full control over the code and security measures. Internal teams can align development with your organization's specific security policies, conduct thorough code reviews, and perform regular audits.

#### Certified Microsoft Partners

Certified Microsoft Partners often provide custom SPFx solutions and consulting services. These partners are vetted by Microsoft and adhere to strict guidelines. Certified partners are recognized for their expertise and compliance with Microsoft's standards, which adds a layer of trust.

#### Trusted Third-Party Vendors

There are several reputable vendors that specialize in developing SPFx solutions. These vendors often have strong reputations and established security practices.

Look for vendors with a proven track record, positive reviews, and transparency in their development process. Ensure they provide support and regular updates.

**Trusted is the key** word here. A professional-looking website doesn't always guarantee trustworthiness, as we've seen [recently](https://www.linkedin.com/news/story/thousands-duped-by-fake-websites-6024828/).

### Enhanced Monitoring and Logging

#### Dependency Logging to Application Insights

Administrators should enable detailed logging to track dependencies and interactions with external APIs. This can be achieved by deploying a SharePoint solution, such as an application customizer, across the entire tenant. By applying this customizer tenant-wide, all traffic across SharePoint pages will be consistently logged, ensuring comprehensive coverage and visibility into external communications.

To manage the volume of logged data, known and trusted URLs may be whitelisted. This helps focus attention on unfamiliar or suspicious network calls.

Logs capture specific SharePoint pages triggering external calls. This granularity aids in identifying and evaluating potentially malicious solutions.

### Educate and Train

#### Security Awareness

If site level catalogs are enabled in the tenant, educate the site owners about the risks associated with installing and using third-party solutions. Encourage vigilance and a healthy skepticism toward unfamiliar apps or components.

#### Audit

It is possible to review solutions deployed to sites with site-level app catalog and to identify the API permissions they request.

By analyzing this data, administrators can determine which API permissions are no longer necessary, thereby improving the overall security posture of the organization by reducing unnecessary access rights.

[Update 2024.08.18: "Roles and Responsibilities" section added, "Mitigation strategies" updated with more information. ]
