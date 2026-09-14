# SC-900 Study Notes

**English** · [ไทย](README.th.md)

Notes for **Microsoft Certified: Security, Compliance, and Identity Fundamentals (SC-900)**, written after passing in September 2026.

By [Jarudech Thongkum](https://www.linkedin.com/in/jarudech-thongkum/)

These are my own notes, written from scratch. They are not a question bank and contain no exam content. See [What is not here](#what-is-not-here).

The goal was to be able to *explain* the material out loud, not recognise it in a multiple-choice list.

---

## The four services, in one picture

Most of SC-900 comes down to knowing which product owns which problem. The analogy that made it stick for me is a single office building.

| Service | Think of it as | Owns |
|---|---|---|
| **Entra** | The door and the keycard system | Who gets in, and what they can reach |
| **Purview** | Labels on the documents, and rules about where they may travel | The data itself |
| **Defender** | Guards and sensors in each room | Detecting and stopping attacks where they happen |
| **Sentinel** | The control room with every camera feed on one wall | Correlating signals from everywhere, including non-Microsoft sources |

---

## Microsoft Entra

Identity and access management, formerly Azure Active Directory. Entra answers *who are you, and what are you allowed to reach.*

- **Entra ID**: the directory of users, groups, devices and app registrations
- **Authentication**: MFA, passwordless, FIDO2 keys, Windows Hello
- **Conditional Access**: the piece that does the real work. Policies of the form *if these conditions, then require this, or block.* Sign-in from an unfamiliar country forces MFA. An unmanaged device is blocked from downloading.
- **Privileged Identity Management (PIM)**: admin rights granted just-in-time and time-boxed, instead of standing permanently on an account
- **Identity governance**: access reviews, joiner/mover/leaver lifecycle, entitlement management
- **Entra ID Protection**: risk scoring on sign-ins and users, such as impossible travel or leaked credentials

Why this domain carries so much weight on the exam: in cloud environments there is no network edge left to defend, so identity becomes the control plane.

---

## Microsoft Purview

Data governance and compliance. Entra protects the person; Purview protects the data wherever it travels.

- **Sensitivity labels**: classify and tag content as Public, Internal or Confidential, with encryption and watermarking attached to the label
- **Data Loss Prevention (DLP)**: enforce what labelled data may do, such as blocking a Confidential file from leaving via email or USB
- **Insider Risk Management**: behavioural signals for risk originating inside the organisation
- **eDiscovery and Audit**: locate and preserve content for legal or investigative purposes
- **Data Lifecycle Management**: retention and deletion schedules
- **Compliance Manager**: score and track posture against regulatory frameworks

The idea worth remembering: protection travels with the file, not with the folder it happens to sit in.

### Four things I kept confusing

This was my weakest area, and it was because I had collapsed four separate things into one.

| Thing | What it actually does |
|---|---|
| Sensitive information type | Detects a pattern, such as a credit card number |
| Sensitivity label | Encrypts and restricts access |
| Retention label | Decides how long data lives |
| Content explorer | Shows *where* labelled files are |
| Activity explorer | Shows *what people did* with those files |

---

## Microsoft Defender

Threat protection. Defender is a family, not a single product, and each member watches a different surface.

| Product | Watches |
|---|---|
| Defender for Endpoint | Laptops and servers (EDR, behavioural detection, response) |
| Defender for Office 365 | Email and collaboration: phishing, malicious links and attachments |
| Defender for Identity | Attacks against on-premises Active Directory |
| Defender for Cloud Apps | SaaS applications in use across the organisation (CASB) |
| Defender for Cloud | Posture and workload protection across Azure, AWS and GCP |
| Defender XDR | The unified layer that correlates alerts from the above |

XDR is the point. A phishing email, a suspicious sign-in, a process spawned on a laptop, a token replayed and a storage bucket read are not five problems. They are one attack. XDR stitches them into a single incident with one timeline instead of five tickets in five consoles.

---

## Microsoft Sentinel

Cloud-native SIEM plus SOAR. Where Defender goes deep on Microsoft surfaces, Sentinel goes wide.

- **Data connectors**: ingest from anywhere, including firewalls, network devices, AWS CloudTrail and third-party tools
- **Analytics rules**: detection logic written in KQL that turns raw log lines into incidents
- **Workbooks**: dashboards and investigation views
- **Playbooks**: automated response built on Logic Apps, such as disabling an account or isolating a host
- **UEBA**: baseline normal behaviour, then flag deviation
- **Threat intelligence**: enrich detections with known-bad indicators

---

## Two distinctions people get wrong

### Defender vs Sentinel

The question I would expect first.

Defender detects and protects within its own surface. Sentinel aggregates and correlates across everything, including what Defender cannot see: the third-party firewall, the AWS account, the custom application writing its own logs. Defender feeds Sentinel. They are not competitors.

Short version: **Defender sees deep in its own house. Sentinel sees wide across the city.**

### Entra vs Defender for Identity

Entra *manages* identity. Defender for Identity *detects attacks against* identity.

Entra is the system of record and the policy engine: create the account, grant the role, require MFA, review the access. Defender for Identity is the sensor watching for someone abusing that layer through lateral movement, Kerberos abuse or reconnaissance against a domain controller.

---

## Zero Trust

Three principles. The third is the one that changed how I think.

1. **Verify explicitly.** Authenticate and authorise from every available signal: identity, device health, location, workload, data sensitivity. Being inside the corporate network is not evidence of anything.
2. **Use least privilege access.** Just-enough access, just-in-time, with risk-based adaptive policy.
3. **Assume breach.** Design as though the attacker is already inside. Segment, encrypt end to end, verify continuously, minimise the blast radius.

### Why "assume breach" is not defeatism

It replaces a question that cannot be answered, *how do we make sure we are never breached*, with one that can be measured: **when an identity is compromised, how much can it reach?**

That question is measurable, so I measured it.

> **Related project:** [iam-blast-radius-lab](https://github.com/olafier/iam-blast-radius-lab). A controlled experiment across 100 actions and 12 AWS services comparing three IAM policy tiers. A narrow policy reached **3.9%** impact-weighted blast radius; a PowerUser-style policy reached **77.6%**. Same identity, same compromise, twenty-fold difference in consequence, purely from how the policy was written.

Principle 2 and principle 3 are the same sentence read from two directions.

### Shared responsibility

Responsibility shifts with the service model.

| | On-premises | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Physical, host, network | Customer | Provider | Provider | Provider |
| OS and patching | Customer | **Customer** | Provider | Provider |
| Application | Customer | Customer | **Shared** | Provider |
| **Identity and access** | Customer | **Customer** | **Customer** | **Customer** |
| **Data** | Customer | **Customer** | **Customer** | **Customer** |

The bottom two rows never move. Identity and data are always the customer's responsibility, on every model. Most cloud incidents live in exactly those two rows.

---

## The exam at a glance

| Domain | Weight |
|---|---|
| Concepts of security, compliance and identity | 10–15% |
| Capabilities of Microsoft Entra | 25–30% |
| Capabilities of Microsoft security solutions | 35–40% |
| Capabilities of Microsoft compliance solutions | 20–25% |

Passing score: **700 / 1000**. Content was refreshed in mid-2026, so check the current [study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/sc-900) before trusting any third-party course.

---

## How I prepared

Two weeks, alongside university coursework.

My first practice attempt came back at 58%, and compliance was the worst domain at 38%. The instinct was to drill more questions. What actually helped was stopping to find out *which* domains I was failing repeatedly, then rereading only those. Once I separated the four Purview concepts above, compliance climbed to 100% on later practice sets.

**The real exam disagreed.** Compliance was still my weakest domain on the score report.

I think the reason is question format. The practice tests I used were almost all long scenario questions, where the context does half the work of eliminating options. The real exam asked definitions directly, with nothing to lean on. So what I had actually fixed was my ability to answer *scenario questions about compliance*, not my knowledge of Purview. The 100% that reassured me had been measuring the wrong thing from the start.

That is the part worth passing on. Choosing the wrong instrument will convince you that you are good at something you are not.

What I would tell someone starting SC-900:

- Do not memorise product names. Learn which problem each product owns, and every question gets easier.
- Take a diagnostic early. A bad score at the start is information; a bad score the night before is panic.
- Say the answers out loud. If you cannot explain Defender versus Sentinel without notes, you do not know it yet.
- Mix question formats. If you only practise on scenarios, you will overestimate yourself.

---

## Resources

- [Official SC-900 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/sc-900)
- [Microsoft Learn: SC-900 learning path](https://learn.microsoft.com/en-us/training/courses/sc-900t00), free and first-party
- [Official practice assessment](https://learn.microsoft.com/en-us/credentials/certifications/security-compliance-and-identity-fundamentals/), free, and the only practice material whose provenance is guaranteed
- [Zero Trust guidance centre](https://learn.microsoft.com/en-us/security/zero-trust/)
- [Shared responsibility in the cloud](https://learn.microsoft.com/en-us/azure/security/fundamentals/shared-responsibility)

---

## What is not here

**No exam questions. No recalled exam content. No redistributed course material.**

Microsoft's [exam security policy](https://learn.microsoft.com/en-us/credentials/support/exam-and-assessment-lab-security-policies) treats exam content as confidential intellectual property. Posting it, including questions written down from memory, can result in revoked credentials and a permanent ban from the certification programme. Paid third-party courses are separately protected by copyright and their platform's terms of use.

Everything here is written in my own words from public documentation and my own study.

---

## Licence

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Product names and trademarks belong to Microsoft.
