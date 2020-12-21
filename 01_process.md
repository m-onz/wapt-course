
# Security audit process


## Rules of engagement

Formal upfront agreement outlining responsibilities, liabilities and scope of work.

* the goal and scope of the engagement
* the timeline and milestones
* the liabilities & responsibilities
* the allowed techniques
* the deliverables and expectations
* the statement of work

## Goals and scope

Ask questions...

##### Question the goal

> Why do you want to execute a pen test?

##### Question the scope

> What is the logical and physical scope?

Logical for example: the department or the entire business

Physical scope is defining the IP address, subdomains, individual servers and machines and so on.

> Is it just a single website... or does it include subdomains?

## Timeline

Create a work timeline including:

* start time and date
* end time and date
* criticality of work
* affected targets
* source IP address
* step in the process

consider using a table.

## Liabilities

During an engagement possible liabilities are:

* accessing data out of scope
* accidentally removing data
* causing unavailability of production services
* other catastrophic events that impact an organisation

## Responsibilities

* keeping the client informed & up to date
* keeping the reports collected in a safe place
* following a code of ethics
* nondisclosure of any information

store data in an encrypted place and destroy them after providing them to the client. You can never disclose information you come across during an engagement to anyone.

you may come across personal information that is considered sensitive and falls under strict privacy laws and regulations. The client is responsible for protecting their employees information, make the client aware of this in writing before the engagement to cover yourself legally.

## NDA

A non-disclosure (NDA) is a part of any engagement. The pentester guarentees in writing that any information, vulnerability or general information relating to the engagement will be disclosed to any third party.

This assurance must be made in accordance with ethical conduct, codes of honor and respect for the confidentiality of the client.

## Emergency plan

This is a plan agreed before the engagement which has plans of action in circumstances considered bad or emergencies that may arise. This could be natural disasters, any of the liabilities mentioned earlier.

The emergency plan contains.

* the timetable
* the contact in charge of responding the emergency plan
* the solutions to apply in each scenario

If the criticality at a point of the timeline is high and has a correspondingly critical emergency plan. The client must have a team ready to act on their emergency plan if the need arises.

The emergency plan is rendered useless if...

* the contacts are not available when needed
* there was no planning done (contact is too unprepared to help)
* there was no written plan

## Allowed techniques

You should agree upon allowed techniques for intrusion that avoid destroying the clients systems or infrastructure.

Some intrusive attacks that may be off limits are:

* brute force attacks
* social engineering
* data harvesting of temporary internet files
* phishing attacks

## Methodologies

Some existing pentesting methodologies includde:

* PTES - Penetration testing execution standard
* OWASP Testing guide

## Reporting

Take notes and record what your doing AS YOUR DOING the pentest. DO NOT WAIT until the end before making notes as you will miss out or forget important info.

The main focus of a report is...

* knowing the status of the security of assets in scope
* knowing what is vulnerable
* knowing what needs to be fixed first

The report is the only deliverable and the findings and content within will be what the client judges you on.

The report will need to be...

* exhaustive
* clear
* on-time
* good looking
* adherent to the clients goals

Consider using mind mapping tools to get relationships and structure to your findings.

## Report audiences & report structure

The report has too speak to different audiences and the report speaks to each audience in the following order.

### Executives

* Corporate, management and business executives.

### IT Department

* which departments are affected by which vulnerabilities

### Development

* provide exploits, POCs remediation tips, source code
* this is the technical part of the report

or

* executive summary
* vulnerability report
* remediation report

### Executive Summary

No more than 2-3 pages long. Give a brief and concise overview of the whole engagement. eg.

> The purpose of this assessment and report is identify any web application issues that affect ACME CORPs e-commerce application and the web server hosting it, and to provide solutions to remedy the same issues.

* use metrics, risk mitigation to demonstrate money loss
* show graphs and statistics

Estimate the level of security and demonstrate your findings using graphs, charts, stats and tables. Avoid technical details and focus on risk and objective facts.

Demonstrate concrete and viable proof that by hiring you and following your remediation guidelines they have lowered their risk.

If you have already done pentests there, include a graph showing the risk over time in comparison to previous tests (hopefully after having applied remediation).

Use MITRE (CAPEC) or WASC threat classification to graph found vulnerabilities by a threat score.

### Vulnerabilities by cause

* 0 day vulnerabilites
* un-patched software
* failure to implement security controls
* improper authentication management
* misconfiguration
* other

## Vulnerability report

Graph vulnerabilities per item in scope.. using OWASP top 10, WASC Threat classification or MITRE CWE.

Also use descriptions from NIST or osvdb.org

You may need to elaborate the descriptions to make the information more relevant to the clients context and situation specifics.

## Vulnerabilities by type

For each vulnerability found use a schema:

name of vulnerability:
  * brief description
  * impact - business impact
  * reference to classifications
  * vulnerability ID

exploitation proof of concept:
  * screenshots
  * exploitation code

affected targets:
  * vuln # com.1 domain / page1 / param1
  * vuln # com.2 domain / page2 / param2

You should also assign an impact value using:

* difficulty of exploitation (easy or hard)
* affected systems (asset value)
* exposure (remote access or privileged account)
* availability (obscure or metasploit module)

## Exploitation proof

* keep it short and keep redundant information to a minimum.
* include snapshots and exploit payloads so that the attack can be reproduced.
* do not show your workings.. only show a small example POC to demonstrate the vulnerability is present.
* only include one impactful outcome per exploitation... work laterally.

## Remediation report

This is the last section aimed at developers charged with fixing and acting upon your findings. Focus on short term and long term solutions.

### Example short term remediation

Suggest a way to inform developers immediately should a high risk vulnerability be found during the pentest.

### Example long term remediation

* Implentation of SSDLC (secure software development cycle)
* Employ security checks early in business & development process
* Use different platforms, version of frameworks

## Example reports

Find and read other pentest reports to learn from
Get someone else to proof read yours possible

https://members.elearnsecurity.com/course/resources/name/wapt_v2_section_1_module_1_attachment_Reporting_guide

https://cure53.de/#publications
