# Help Desk Administrator Homelab
## Ticketing (Peppermint + Docker) + Active Directory + Windows 11

This project documents a simulated **Help Desk environment** built while completing the **TCM Security Practical Help Desk Administrator course**.

It includes:
- A **ticketing system** (Peppermint) deployed with **Docker** to track IAM and AD tickets
- An **Active Directory domain** (Windows Server Domain Controller)
- A **Windows 11** domain-joined workstation

---

## Lab Architecture (Visual)

```mermaid
flowchart LR
  U[User / Request] --> T[Ticket Created in Peppermint]
  T --> H[Help Desk / Technician Workflow]
  H --> AD[Active Directory Domain Controller<br/>DC01 - corp.local]
  AD --> W11[Windows 11 Client<br/>CLIENT01]
  AD --> DNS[DNS on DC01]
  H --> C[Resolution Notes + Close Ticket]
```

---

## Environment Details

**Domain:** `corp.local`

**Domain Controller**
- OS: Windows Server 2019
- Hostname: `DC01`
- IP: `192.168.1.10`
- Roles: AD DS, DNS

**Client**
- OS: Windows 11
- Hostname: `CLIENT01`
- Role: Domain-joined workstation

**Ticketing**
- Peppermint Ticket Management System
- Deployed using Docker

---

# Part 1 — Ticketing System Lab (Peppermint)

### Section Topics Covered (from course)
- Ticketing – Section Intro
- Installing Docker and Peppermint
- Configuring Peppermint
- Creating, Commenting, Assigning and Closing Tickets

---

## Step 1 — Install Docker + Deploy Peppermint (Visual)

```mermaid
sequenceDiagram
  autonumber
  participant Admin as Admin/Student
  participant Docker as Docker
  participant Pep as Peppermint (Container)
  participant Web as Browser

  Admin->>Docker: Install Docker
  Admin->>Docker: Pull/Run Peppermint container
  Docker->>Pep: Start container + services
  Admin->>Web: Open Peppermint URL
  Web->>Pep: Load dashboard/login
```

**Outcome:** Peppermint is running and accessible via browser.

---

## Step 2 — Configure Peppermint (Visual)

```mermaid
flowchart TB
  A[Open Peppermint Admin Panel] --> B[Create Admin Account]
  B --> C[Create Technician Roles]
  C --> D[Create Categories/Queues]
  D --> E[Define Workflow Rules]
  E --> F[Ready for Ticket Simulation]
```

**Example categories/queues**
- IAM Requests
- Active Directory Issues
- Password Resets
- Workstation Support

---

## Step 3 — Ticket Lifecycle Practice (Visual)

```mermaid
flowchart LR
  C1[Create Ticket] --> C2[Assign Ticket]
  C2 --> C3[Add Comments/Notes]
  C3 --> C4[Troubleshoot + Resolve]
  C4 --> C5[Close Ticket]
```

---

## Example Tickets (Used in this Lab)

- Ticket 001 — Create new user account
- Ticket 002 — Password reset request
- Ticket 003 — Account locked out
- Ticket 004 — Workstation cannot join domain
- Ticket 005 — User cannot log in (domain login troubleshooting)

---

# Part 2 — Active Directory Lab

## Step 1 — Create Virtual Machines (Visual)

```mermaid
flowchart LR
  V[Virtualization Platform<br/>VMware/VirtualBox] --> DC[VM: Windows Server<br/>DC01]
  V --> CL[VM: Windows 11<br/>CLIENT01]
```

**Recommended resources**
- DC01: 4GB RAM, 2 CPU, 60GB disk
- CLIENT01: 4GB RAM, 2 CPU, 60GB disk

---

## Step 2 — Configure Static IP on DC01 (Visual)

```mermaid
flowchart TB
  A[DC01 Network Adapter] --> B[Set IPv4 Static IP]
  B --> C[Set DNS to DC01 IP]
  C --> D[Verify Network Connectivity]
```

**Example configuration**
- IP: `192.168.1.10`
- Subnet: `255.255.255.0`
- Gateway: `192.168.1.1`
- DNS: `192.168.1.10`

---

## Step 3 — Install AD DS Role (Visual)

```mermaid
flowchart LR
  SM[Server Manager] --> ARF[Add Roles and Features]
  ARF --> ADDS[Select: Active Directory Domain Services]
  ADDS --> INS[Install]
```

**Outcome:** AD DS role installed on DC01.

---

## Step 4 — Promote to Domain Controller (New Forest) (Visual)

```mermaid
flowchart TB
  A[Promote this server to a domain controller] --> B[Add a new forest]
  B --> C[Domain: corp.local]
  C --> D[Set DSRM Password]
  D --> E[Install + Reboot]
```

**Outcome:** DC01 becomes Domain Controller + DNS for `corp.local`.

---

## Step 5 — Create OU Structure (Visual)

```mermaid
flowchart TB
  Root[corp.local] --> IT[OU: IT]
  Root --> HR[OU: HR]
  Root --> Sales[OU: Sales]
  Root --> WS[OU: Workstations]
```

**Outcome:** Clean structure for managing users and devices.

---

## Step 6 — Create Domain Users (Visual)

```mermaid
flowchart LR
  OU[Select OU] --> NU[New User Wizard]
  NU --> U1[Create Username + Password]
  U1 --> U2[Verify in ADUC]
```

**Example users**
- `jsmith` (John Smith)
- `adoe` (Alice Doe)
- `mjones` (Michael Jones)

---

## Step 7 — Join Windows 11 to the Domain (Visual)

```mermaid
sequenceDiagram
  autonumber
  participant W11 as Windows 11 (CLIENT01)
  participant DC as DC01 (AD DS + DNS)
  participant DNS as DNS on DC01

  W11->>DNS: Query corp.local / find DC
  DNS->>W11: Returns DC01 info
  W11->>DC: Domain Join Request (corp.local)
  DC->>W11: Create/Update computer account
  W11->>W11: Reboot
```

**Outcome:** CLIENT01 is domain joined to `corp.local`.

---

## Step 8 — Group Policy Example (Visual)

```mermaid
flowchart TB
  GPMC[Group Policy Management] --> GPO[Create New GPO]
  GPO --> LINK[Link to Domain or OU]
  LINK --> EDIT[Edit Policy Settings]
  EDIT --> APPLY[gpupdate / reboot]
```

**Example policy**
- Disable Control Panel Access  
Path:
- User Configuration → Administrative Templates → Control Panel → Prohibit access to Control Panel → **Enabled**

---

# Ticket-to-Resolution Example (Visual + Narrative)

```mermaid
flowchart LR
  T[Ticket #003: Account Locked Out] --> AD[Unlock Account in AD]
  AD --> PW[Reset Password]
  PW --> DOC[Document Steps in Ticket]
  DOC --> CLOSE[Close Ticket]
```

**Resolution steps**
1. Locate user account in ADUC  
2. Unlock account  
3. Reset password (if required)  
4. Document the fix in Peppermint  
5. Close the ticket

---

# Skills Demonstrated

- Help Desk ticket management (creation, assignment, documentation, closure)
- IAM workflow simulation
- Active Directory administration (AD DS, DNS, users, OUs)
- Windows 11 domain join + troubleshooting
- Group Policy configuration
- Virtual lab deployment (VMware/VirtualBox)

---

# Future Improvements

- File shares + NTFS permissions
- PowerShell automation (bulk users, resets, reporting)
