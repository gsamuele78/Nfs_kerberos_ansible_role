# Troubleshooting Guide — NFS/Kerberos + AD

## 1. SPN / Keytab Issues

**Symptoms:**

- Mount fails with `Permission denied` or `No such file or directory`
- `klist -k /etc/krb5.keytab` does not list `nfs/hostname@REALM`

**Checks:**

```bash
klist -k /etc/krb5.keytab
ldapsearch -Y GSSAPI -H ldap://dc.example.com -b "dc=example,dc=com" "(servicePrincipalName=nfs/nfs-server.example.com)" 
```

**Fixes:**

- Re-run `adcli keytab add --principal=nfs/<fqdn>@REALM`

- Verify host is AD-joined and clocks are synchronized

## 2. DNS / Time Skew

- Check forward/reverse DNS:

```bash
getent hosts nfs-server.example.com
host $(hostname -f)
```

- Check NTP:

`timedatectl status`

Kerberos fails if clocks skew > 5 minutes.

## 3. SSSD / NSS Resolution

Ensure SSSD running: systemctl status sssd

Test user resolution: getent passwd testuser

Check /etc/sssd/sssd.conf includes [nfs] service if required

## 4. Autofs / Mount Failures

Run autofs in foreground for debugging:

`automount -f -v`

Verify autofs map `/etc/auto.home` points to correct server and uses `sec=krb5p`

## 5. Logs

SSSD: `/var/log/sssd/sssd_*.log`

NFS server: `journalctl -u nfs-server`

Kerberos: `/var/log/auth.log or journalctl`
