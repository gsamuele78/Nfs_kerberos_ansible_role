# Architecture Overview — NFS Home Integration with AD

## 1. Components

- **Ubuntu 24.04 Servers:** AD-joined via `realm`/`sssd`
- **NFS Server:** `/home` exported using NFSv4 with `sec=krb5p`
- **Clients:** Mount `/home` using autofs + Kerberos authentication
- **SSSD:** Provides identity resolution for AD users and integrates with PAM/NSS
- **Kerberos:** Provides secure authentication for NFS home access
- **Ansible Roles:** Automate installation, configuration, SPN creation, and validation

---

## 2. Data Flow

1. User logs into client machine
2. PAM + SSSD resolves username via AD
3. Kerberos ticket requested (kinit or automated via PAM)
4. Autofs mounts `/home/username` from NFS server
5. NFS server verifies Kerberos ticket using `nfs/hostname` keytab
6. File access permitted/denied according to ticket

---

## 3. Why SSSD/NFS Integration Works This Way

- SSSD centralizes AD identity resolution for UID/GID consistency across nodes
- NFSv4 with Kerberos provides secure, encrypted home directories
- Using host keytab ensures machine identity for NFS service principal
- Autofs + Kerberos allows on-demand mounting of homes without persistent mounts

---

## 4. Kerberos SPN & Keytab Management

- SPN: `nfs/<hostname>@REALM`
- Managed automatically via `adcli keytab add` if not present
- Keytab stored at `/etc/krb5.keytab`, used by NFS server to decrypt tickets
- Idempotent — playbook checks if principal already exists in keytab or AD

---

## 5. Extending the System

- Add more clients: simply include in `[nfs_client]` group and run playbook
- Add more NFS exports: create new export tasks, update autofs maps
- Automate user keytab provisioning for full end-to-end testing
- Integrate monitoring: use Prometheus + node_exporter or NFS logs
