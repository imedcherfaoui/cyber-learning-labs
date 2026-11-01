# Active Directory & Windows Domain Architecture

In enterprise environments, managing and securing a large number of machines and users requires a centralized system. Windows Domains and Active Directory provide this structure.

## 1. Domain Structure & Objects
A **Windows Domain** is a logical group of network objects (servers, Domain Controllers, PCs, users) sharing the same Active Directory database. 

*   **Active Directory (AD):** The directory service that allows administrators to manage domain objects.
*   **Objects:** Can represent people (users), services, machines, or security groups.
*   **Organizational Units (OUs):** Containers within AD used to organize objects (users, groups, computers) and delegate administrative control.
*   **Delegation:** Granting specific administrative permissions over an OU to a user or group without giving them full domain admin rights.

## 2. Group Policy Objects (GPOs)
GPOs are collections of rules and settings applied automatically to one or multiple objects within the domain. They are used to enforce security policies, restrict access, and configure environments at scale.

## 3. Trees, Forests & Trust Relationships
As organizations grow, the logical structure expands:
*   **Domain Tree:** Multiple domains operating under the same contiguous namespace (e.g., a company opening a branch in another country). Each domain manages its own rules but can interact with others in the tree.
*   **Forest:** A collection of one or more domain trees that do not share a contiguous namespace (e.g., when a company acquires another business). Each tree maintains its own IT management, but they form a single network boundary.
*   **Trust Relationships:** Define how domains interact and share resources.
    *   *One-Way Trust:* Domain A trusts Domain B. Users in Domain B can access resources in Domain A.
    *   *Two-Way Trust:* Both domains trust each other, allowing reciprocal access.

## 4. Windows Authentication Protocols

### Kerberos (Primary Protocol)
A ticket-based authentication system involving the client, the service, and the Domain Controller (Key Distribution Center):
1.  **TGT Request:** Client sends username/timestamp to the Domain Controller.
2.  **TGT Reply:** Domain Controller issues a Ticket Granting Ticket (TGT) and a session key.
   <img width="1047" height="416" alt="image" src="https://github.com/user-attachments/assets/c9daecbd-6047-45c7-89bc-910b288796bd" />

4.  **TGS Request:** Client requests a Ticket Granting Service (TGS) for a specific service using the TGT and the Service Principal Name (SPN).
5.  **TGS Reply:** Domain Controller issues the TGS and a service session key.
   <img width="1049" height="486" alt="image" src="https://github.com/user-attachments/assets/aa93a5c6-6b62-48bc-a4a4-e8a2215140d6" />

7.  **Service Authentication:** Client presents the TGS directly to the target service to authenticate.
   <img width="1029" height="362" alt="image" src="https://github.com/user-attachments/assets/e70ba153-19b3-415f-9bbd-d83eb3bc50af" />


### Net-NTLM (Legacy/Fallback Protocol)
A challenge-response authentication mechanism:
<img width="1051" height="605" alt="image" src="https://github.com/user-attachments/assets/f2dc0661-bd33-4e8e-b4cd-868a4a6f960a" />

1.  Client sends an authentication request to the server.
2.  Server replies with an NTLM Challenge.
3.  Client combines the challenge with their hashed password to create a Response.
4.  Server forwards the Challenge and Response to the Domain Controller.
5.  Domain Controller calculates the expected response using its stored NTLM hash. If it matches the client's response, authentication is approved.
