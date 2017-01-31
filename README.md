javajawa SSL Tools
==================

A collection of shell scripts that can simplify and partially
automate certificate management, including maintain a personal
Certificate Authority.

Installation / Configuration
----------------------------

These tools are designed to be used with openssl on linux-like
systems.

*Dependencies:*

 - A POSIX compatible shell
 - `openssl`
 - `readlink`, supporting -f
 - `sed`, supporting non / delimiter
 - `find`, supporting `-exec {} +` syntax
 - `grep`
 - `od`
 - `diff`

*Installation:*

The scripts make be run from any folder, but you may wish
to add them to `$PATH`, possibly by copying them to
`/usr/local/bin`.

*Configuration:*

`openssl.cnf` should be copied to the folder where you want
to store your PKI.
You should update the `HOME` (line 6) setting, and the
`_default` values in the `[ req_distinquished_name ]` section.

Useage
------

In this document we will assume that `HOME = /var/pki`,
and the defaults are as in the supplied `openssl.cnf`
(ie, "Tea Cats").

All scripts expect the current directory to be the HOME
of OpenSSL.

*`generate_root_ca`*

The `generate_root_ca` script creates a self-signed (root)
certificate authority, which is configured to sign other
CA certificates.

It accepts the following environment variables to configure
the action performed:

| Variable   | Default | Purpose
| `CA_NAME`  | root    | The directory and configuration name of the CA
| `KEY_TYPE` | rsa     | The key's cryptography algorithm
| `KEY_BITS` | 4096    | The number of bits in the key
| `HASH`     | sha512  | The hashing algorithm used to sign the key
| `DAYS`     | 7000    | How many days the key should be valid for


