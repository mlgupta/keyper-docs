# SSH Server Config
For a Linux server to be able to work with keyper and access centralized SSH Public Key for a user, it must be running an SSH server that supports the *AuthorizedKeysCommand* option in *sshd_config* (OpenSSH 6.8 or newer). The following instructions were written for RHEL/CentOS type of Linux systems. If you are using any other flavor, you may need to tweak the instructions a little.

Following steps need to be performed on each linux server you want to be part of keyper:
1. Login as root on linux server
2. Go to SSH config directory
    ```console
     # cd /etc/ssh
     ```
3. Download auth.sh.txt file from keyper
    ```console
    # wget http://keyper.example.org/scripts/auth.sh.txt
    # mv auth.sh.txt auth.sh
    # chmod +x auth.sh
    ```
4. Add following parameters to sshd_config
    ```console
    AuthorizedKeysCommand /bin/sh /etc/ssh/auth.sh %u 
    AuthorizedKeysCommandUser root
    ```
5. Make appropriate tweaks to the script auth.sh. Especially check for KEYPER_HOST and http/https. Uncomment curl call line per your preference of http GET vs POST.
    ```
    #!/bin/sh

    USER="$1"
    HOST=`hostname`
    KEYPER_HOST=keyper.example.org

    curl -s -q -f -m 7  "http://${KEYPER_HOST}/api/authkeys?username=${USER}&host=${HOST}"
    #curl -s -q -f -m 7 -Fusername=${USER} -Fhost=${HOST} http://${KEYPER_HOST}/api/authkeys

    exit $?
    ```
6. Test the script
    ```console
    # ./auth.sh alice astrix
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1KtJpPn6W9W5WgPU8+eYuuSKKyHA+Z62mVLYp50Ch/MMTUSxcFF/V1H81CStU4OrPv/pUxpHtqSDeTCMbVtTmP0Bbc5V7rCYQVgfhTB7CzKAwnfJSfJGY/JoJLCrC4kt40PMwyXTHiOKkrs4tOHiv7GIT4aZI/wmVPrg8x6oBFRgfCl1TQVgeSQl2kAnjkUHEsq2CsnZR9mKIJ31CWzeHLotYHNg82jmgylCWUsl6Pd5eigObUtk0j6Vnjn7FUKwSmffhEPInU1K+IzYMdFe1QElTSO7X+IOjedQZ2Y8nt3U9N9WPyd7FK13Sn8Ij22CIMmTuvfNXv/H4ja9vF0Ob
    ```
    If the above returns appropriate key, you are good to go.
7. Restart SSH daemon for SSH to start calling auth.sh during authentication
    ```console
    # systemctl restart sshd
    ```