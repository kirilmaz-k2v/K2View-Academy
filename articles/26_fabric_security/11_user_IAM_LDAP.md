# User IAM Using LDAP

LDAP, the Lightweight Directory Access Protocol, is a mature, flexible and well supported standards-based mechanism for interacting with directory servers. It is often used for authentication and storing information about users, groups and applications, but is a fairly general-purpose data store and can be used in a wide variety of applications.

The data is stored in an hierarchical structure, and active directory searches are done according to this hierarchy.

## Terminology

- **Distinguished Name (DN)** - uniquely identifies an entry and its path. It is comprised of a series of  relative distinguished names (RDNs) separated by commas.  
- **Organization Unit** (**OU**) - part of the hierarchy at the DN.
- **Common name (CN)** - the low level of an entry,  which is actually the user id.
- **Entry** - a collection of information about an entity.


## How Fabric works with LDAP 

When integrated with LDAP, the login steps for the end user are similar to the steps when authentication is managed by Fabric:

User attempts to access a Fabric app (1 in the diagram below). One of two results follow: 

   - If the user already has an existing Fabric session (2), he is allowed access and can carry out actions according to permissions granted to him by Fabric and its apps. 

     ELSE
   - If the user does not have an active session (3), Fabric displays the login page, where the user should enter his credentials - user/password. The following steps are then carried out: 

     1. Fabric sends these credentials to the LDAP server (4).
     2. LDAP server verifies that the credentials are correct and provides the groups that the user is associated to (5).
     3. Fabric establishes a Fabric session for the user, enabling him access according to permissions granted to him by Fabric and its apps. Fabric session provides, via a *UsercCode*, the information about the user and the roles he is associated to (6).

Below is the logical flow, illustrating these steps: 


<img src="/articles/26_fabric_security/images/15_Fabric LDAP.jpg">

 

For more information and guidelines about setting up Fabric with LDAP, refer to the examples for [Azure AD LDAP](/articles/26_fabric_security/11.1_user_IAM_AD_LDAP.md) and [Azure AD LDAPS](/articles/26_fabric_security/11.2_user_IAM_AD_LDAPS.md). For more information about SAML configuration in Fabric, please see [here](/articles/26_fabric_security/13_user_IAM_configiration.md#ldap--ldaps-configuration).



[![Previous](/articles/images/Previous.png)](/articles/26_fabric_security/10_user_IAM_SAML_Fabric_flow.md)[<img align="right" width="60" height="54" src="/articles/images/Next.png">](/articles/26_fabric_security/16_user_IAM_auditing.md)

