# Admin Guide — NFS / Kerberos / AD Integration

## Overview

This guide explains how to deploy and operate the NFS/Kerberos system using the provided Ansible roles.

- **Server Role:** `nfs_server` — installs NFS server, ensures Kerberos service principal, manages `/home` export.
- **Client Role:** `nfs_client` — installs autofs, mounts homes via Kerberos.

All nodes are **already AD-joined** using `realm` and `sssd`.

---

## Deployment Steps

1. **Ensure prerequisites:**

   - Ubuntu 24.04 nodes
   - AD join confirmed: `realm list`
   - SSSD running: `systemctl status sssd`
   - Correct system time (NTP synced): `timedatectl`
   - DNS forward/reverse configured correctly

2. **Configure Ansible inventory and variables:**

   - `inventory.ini` defines `[nfs_server]` and `[nfs_client]`
   - `group_vars/all.yml` stores vault-protected test user credentials

3. **Run the playbook:**

```bash
ansible-playbook -i inventory.ini site.yml -K
```

   --K prompts for privilege escalation password

Playbook applies roles idempotently

Server automatically checks/creates nfs/hostname principal in AD keytab

Client sets up autofs maps pre-populated with FQDN and /home/&

1. **Verify deployment:**

Server: ```kist -k /etc/krb5.keytab, exportfs -v, getent passwd testuser```

Client: ```getent passwd testuser, mount -t nfs4 -o sec=krb5p nfs-server:/home/testuser /mnt```
