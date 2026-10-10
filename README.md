# LDAP Authentication and Role-Based Access Control Lab

## Project Overview

This project demonstrates the implementation of centralized identity and access management (IAM) using OpenLDAP and System Security Services Daemon (SSSD) on Debian Linux.

The purpose of this lab was to configure LDAP user authentication, manage group membership, and implement group-based access controls to protect sensitive directories.

## Lab Objectives

- Configure an OpenLDAP directory server.
- Create and manage LDAP user and group entries.
- Integrate LDAP authentication with SSSD and PAM.
- Configure secure LDAP communication using StartTLS.
- Implement Linux group-based directory permissions.
- Verify authorized and unauthorized access.
- Apply the principle of least privilege.

## Technologies Used

- Debian Linux
- OpenLDAP
- SSSD
- PAM
- Linux command-line tools
- LDAP utilities
- TLS/StartTLS
- Linux file permissions

## Implementation

### 1. LDAP User and Group Configuration

Created an LDAP user named `jsmith` and assigned the user to the `ITSecurity` group.

Verified the LDAP entries using `ldapsearch`.

### 2. LDAP Authentication

Configured SSSD to integrate LDAP identities with Linux authentication.

Verified that the system recognized the LDAP user:

```bash
getent passwd jsmith
```

Tested authentication using:

```bash
su - jsmith
```

The user successfully authenticated using LDAP credentials.

### 3. Secure LDAP Communication

Configured StartTLS support for the OpenLDAP server and verified encrypted LDAP communication using:

```bash
ldapsearch -x -ZZ -H ldap://localhost \
-b "dc=lab,dc=test" "(uid=jsmith)" dn
```

### 4. Group-Based Access Control

Created a protected directory for the ITSecurity group:

```bash
sudo mkdir -p /srv/itsecurity
sudo chown root:10001 /srv/itsecurity
sudo chmod 2770 /srv/itsecurity
```

The permissions allow the directory owner and authorized group members to access the directory while restricting other users.

The setgid bit ensures newly created files inherit the directory's group ownership.

### 5. Authorized Access Testing

Authenticated as `jsmith` and created a file inside the protected directory:

```bash
cd /srv/itsecurity
touch testfile.txt
ls -l testfile.txt
```

**Result:** The file was successfully created and inherited the `ITSecurity` group.

### 6. Unauthorized Access Testing

Created a local test account outside the ITSecurity group:

```bash
sudo useradd -m -s /bin/bash testuser
id testuser
```

Attempted to access the protected directory:

```bash
sudo -u testuser ls /srv/itsecurity
```

**Result:**

```text
ls: cannot open directory '/srv/itsecurity': Permission denied
```

This confirmed that unauthorized users were prevented from accessing the protected directory.

## Security Concepts Demonstrated

- **Centralized authentication:** LDAP provides a central directory for user identities.
- **Group-based access control:** Directory access is determined by Linux group membership.
- **Least privilege:** Users without the required permissions cannot access protected resources.
- **Secure authentication:** StartTLS provides encrypted communication between LDAP clients and the server.
- **Permission inheritance:** The setgid permission maintains group ownership for newly created files.

## Results

Successfully configured LDAP authentication and integrated LDAP identities with Debian Linux.

Verified that authorized group members could access and create files within the protected directory, while unauthorized users received a permission-denied error.

## Lessons Learned

This lab strengthened my understanding of how identity management, authentication, and authorization work together in Linux environments.

I also gained experience troubleshooting LDAP credentials, SSSD configuration, TLS connectivity, and Linux directory permissions.

## Future Improvements

- Implement additional LDAP groups with different access levels.
- Configure centralized access policies for multiple Linux systems.
- Test account disabling and access revocation.
- Explore LDAP auditing and authentication logging.
