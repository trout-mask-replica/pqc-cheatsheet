# Configure PQC key exchange for OpenSSH on Ubuntu
## TL;DR
Add the following to your sshd_config file:

    KexAlgorithms mlkem768x25519-sha256,sntrup761x25519-sha512,sntrup761x25519-sha512@openssh.com  
   
... and make sure sshd isn't launched with other command-line options regarding key exchange algorithms [since this overrides the config file](https://man.openbsd.org/sshd).

## Prerequisites
Make sure that the OpenSSH-version on your server is of at least version 9.0:

    ssh -V

Check available PQC key exchange alorithms with:

    ssh -Q kex

Look for mlkem768x25519-sha256 and sntrup761x25519-sha512. If you're on a version that precedes 9.9 you will only have the sntrup one, which is totally fine.

## Configure
Navigate to the location of your sshd-config-file, typically:

    cd /etc/ssh/

Open the file that holds your sshd configuration, typically sshd_config, and add the following:

    KexAlgorithms mlkem768x25519-sha256,sntrup761x25519-sha512,sntrup761x25519-sha512@openssh.com

Make sure that sshd isn't launched with other command-line options regarding key exchange algorithms [since this overrides the config file](https://man.openbsd.org/sshd).  
Restart ssh to apply new settings:
    
    systemctl restart ssh

## Validate
Validate which key exchange algorithms were offered and used through: 

    ssh -vvv <user>@<host>

This will launch ssh in debug-mode and show information about the connection. Look for:
> debug2: local client KEXINIT proposal
>
> debug2: KEX algorithms: sntrup761x25519-sha512@openssh.com [...]

...to see which key exchange algorithms the client offered, and verify that one of the PQC options were offered.  

Then look for:

> debug2: peer server KEXINIT proposal
>
> debug2: KEX algorithms: mlkem768x25519-sha256,sntrup761x25519-sha512 [...]

... to see which key exchange algorithms the server offered, and verify that only PQC options were offered.  

Finally look for:
> debug3: kex_choose_conf: will use strict KEX ordering
>
> debug1: kex: algorithm: sntrup761x25519-sha512@openssh.com

... to validate that one of the PQC key exchange algorithms were chosen.

## Caveats and troubleshooting
If the client runs a version of OpenSSH that's pre 9.0 then the client wont be able to make a connection to a server that exclusively offers the PQC key exchange algorithms. Check version of client through:

    ssh -V

And validate that it has PQC key exchange algorithms mlkem768x25519-sha256 or sntrup761x25519-sha512 through:

    ssh -Q kex
