
# User Authentication

Use Central Authentication

Disallow Direct root Login

## Use PAM (Pluggable Authentication Modules)

Check that the file /etc/pam.conf does not exist.

The file /etc/pam.conf MUST NOT be used and to avoid ambiguities this file MUST NOT exist.

All PAM configurations MUST be written in service-specific configuration files under /etc/pam.d/.

The special service other MUST be present and MUST implement a deny policy for all PAM types.

PAM configuration files and directories MUST be owned by user root and group root.
PAM configuration files and directories MUST be writable only by the owner.

Configuration of individual PAM modules MUST take place in module-specific configuration files under /etc/security/.

## Use the Linux Shadow Suite
The Linux shadow suite contains the functions, tools and configuration files to deal with local account information files such as /etc/passwd and /etc/shadow.

The Linux Shadow suite MUST be installed and used.
The configuration file for the Shadow suite is /etc/login.defs .

The file /etc/login.defs MUST be owned by user root and group root and MUST be writable by the owner only.
How to check:
Check that the Shadow suite is installed. (The check for the shadow files is already done by 1.10.08.)
Check that /etc/login.defs exists, is owned by user root and group root and is writable only by root.

## Use Strong Password Hashing Methods









