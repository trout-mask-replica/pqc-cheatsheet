# Configure PQC key exchange for Apache on Ubuntu

## Option 1 - using an Ubuntu version with OpenSSL 3.5 as default
### Prerequisites
Make sure that the default provider of OpenSSL in your distro is of at least version 3.5:

    openssl version

Ubuntu 24.04 LTS for instance runs OpenSSL 3.0, so this guide won't work if that's what you're using. Ubuntu 25.10 is an example of a version that has OpenSSL 3.5 as the default provider.

Check available PQC key exchange groups with:

    openssl list -tls-groups -tls1_3

Look for the ones that IETF mentions in it's [draft on PQC].(https://datatracker.ietf.org/doc/draft-ietf-uta-pqc-app/) 

For hybrid it's:
X25519MLKEM768
SecP256r1MLKEM768
SecP384r1MLKEM1024

For pure PQC it's:
ML-KEM-512
ML-KEM-768
ML-KEM-1024

### Configure
Navigate to the location of your vhost-files, typically:

    cd /etc/apache2/sites-enabled

Open the file that holds the ssl-config for your site with a text editor and add the following lines:

    SSLProtocol TLSv1.3
    SSLOpenSSLConfCmd Curves MLKEM512:MLKEM768:MLKEM1024:SecP256r1MLKEM768:X25519MLKEM768:SecP384r1MLKEM102

This will force TLSv1.3, because earlier versions don't have PQC. And the curves will force the use of PQC key exchange groups in TLSv1.3. Remove or add PQC curves per your requirements.

Restart Apache to apply new settings:

    systemctl restart apache2

### Validate
Different ways of validating are possible, for CLI you can run:

    openssl s_client -connect localhost:443

Look for "Negotiated TLS1.3 group" and validate that the negotiated group is one of your PQC curves.

## Option 2 - using an Ubuntu version without OpenSSL 3.5 as default
Haven't tried this yet. Worth noting is that it can require building Apache from source and linking it to a non default OpenSSL provider.
