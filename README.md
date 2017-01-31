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

h3. Installation

The scripts make be run from any folder, but you may wish
to add them to `$PATH`, possibly by copying them to
`/usr/local/bin`.

h3. Configuration

`openssl.cnf` should be copied to the folder where you want
to store your PKI.
You should update the `HOME` (line 6) setting, and the
`_default` values in the `[ req_distinquished_name ]` section.

Usage
-----

In this document we will assume that `HOME = /var/pki`,
and the defaults are as in the supplied `openssl.cnf`
(ie, "Tea Cats").

All scripts expect the current directory to be the HOME
of OpenSSL.

h3. `generate_root_ca`

The `generate_root_ca` script creates a self-signed (root)
certificate authority, which is configured to sign other
CA certificates.

It accepts the following environment variables to configure
the action performed:

| Variable   | Default | Purpose |
|------------|---------|---------|
| `CA_NAME`  | root    | The directory and configuration name of the CA |
| `KEY_TYPE` | rsa     | The key's cryptography algorithm |
| `KEY_BITS` | 4096    | The number of bits in the key |
| `HASH`     | sha512  | The hashing algorithm used to sign the key |
| `DAYS`     | 7000    | How many days the key should be valid for |

The CA will be created in `${SSL_HOME}/ca/${CA_NAME}`,
along with its own `openssl.cnf`, which you can customise.

A common name for the new CA will be suggested based on the
organisation name in openssl.cnf and the CA's name.

h3. `generate_intermediate_ca`

The `generate_intermediate_ca` script creates a CA which is
signed by another CA, and is intended for signing non-CA
certificates.

It takes one parameter, the CA's name, and the following
environment variables:

`generate_intermediate_ca "CA NAME"`

| Variable   | Default | Purpose |
|------------|---------|---------|
| `CA_ROOT`  | root    | The name of the CA signing this new CA |
| `KEY_TYPE` | rsa     | The key's cryptography algorithm |
| `KEY_BITS` | 4096    | The number of bits in the key |
| `HASH`     | sha512  | The hashing algorithm used to sign the key |
| `DAYS`     | 1800    | How many days the key should be valid for |

A common name for the new CA will be suggested based on the
organisation name in openssl.cnf and the CA's name.

h3. `generate_csrs`

The `generate_cars` scripts automatically generates a number of
certificate signing requests, which can be signed by a local or
external CA at a later point.

Each argument to the script will be considered as a certificate
name which is to be generated.

`generate_csrs foo.co.uk bar.co.uk`

| Variable   | Default | Purpose |
|------------|---------|---------|
| `CA_NAME`  | root    | [Optional] The name of the CA config to use |
| `KEY_TYPE` | rsa     | The key's cryptography algorithm |
| `KEY_BITS` | 4096    | The number of bits in the key |
| `HASH`     | sha512  | The hashing algorithm used to sign the key |
| `DAYS`     | 365     | How many days the key should be valid for |

The default common name will be set to the current argument.
The key and csr will be output in `${SSL_HOME}/certs/${CERT_NAME}`.

If a cert or csr already exists, you will be prompted to replace
it.
If the key already exists, then you will be given the options
to re-use or replace it.

h3. `sign_csrs`

The `sign_csrs` script signs all outstanding Certificate
Signing Requests with the specified Certificate Authority
as first parameter.

It scans `${SSL_HOME}/certs` for CSR files; if the openssl
call exists with status 0, all the CSRs are deleted.

If the created certificate has a common name which is also
a directory in `${SSL_HOME}/certs`, the certificate is
copied to that file for convience. Any old certificate
is backed up.

`sign_csrs "CA NAME"`

| Variable   | Default | Purpose |
|------------|---------|---------|
| `HASH`     | sha512  | The hashing algorithm used to sign the key |
