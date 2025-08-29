# Nfs_kerberos_ansible_role

Install nfs client and server joined with sssd

nfs_kerberos_ansible/
├── README.md
├── ansible.cfg
├── inventory.ini
├── site.yml                  # main playbook
├── group_vars/
│   └── all.yml               # vault-enabled vars (e.g., test credentials)
├── roles/
│   ├── nfs_server/
│   │   ├── tasks/
│   │   │   ├── main.yml
│   │   │   ├── install.yml
│   │   │   ├── spn_keytab.yml
│   │   │   ├── exports.yml
│   │   │   └── check_config.yml
│   │   ├── handlers/main.yml
│   │   └── defaults/main.yml
│   └── nfs_client/
│       ├── tasks/
│       │   ├── main.yml
│       │   ├── install.yml
│       │   ├── autofs.yml
│       │   └── check_config.yml
│       ├── handlers/main.yml
│       └── defaults/main.yml
└── doc/
    ├── ADMIN_GUIDE.md
    ├── TROUBLESHOOTING.md
    └── ARCHITECTURE.md
