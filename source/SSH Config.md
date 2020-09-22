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
    #############################################################################
    #                       Confidentiality Information                         #
    #                                                                           #
    # This module is the confidential and proprietary information of            #
    # DBSentry Corp.; it is not to be copied, reproduced, or transmitted in any #
    # form, by any means, in whole or in part, nor is it to be used for any     #
    # purpose other than that for which it is expressly provided without the    #
    # written permission of DBSentry Corp.                                      #
    #                                                                           #
    # Copyright (c) 2020-2021 DBSentry Corp.  All Rights Reserved.              #
    #                                                                           #
    #############################################################################
    # This scripts does the work of authorized_keys file by calling REST API    #
    # and gets Public for the user attempting to login using SSH.               #
    #                                                                           #
    # The script accepts two parameters:                                        #
    # 1. USER: linux username of the user trying to login is a mandetory        #
    #           parameter. It is set using %u on AuthorizedKeysCommand          #
    # 2. Ffingerprint: SSH Key finger print is an optional parameter. It is set #
    #                  usign %f on AuthorizedKeysCommand                        #                          
    # - Deploy this script under /etc/ssh (or corresponding location in your    #
    #   distro).                                                                #
    # - Rename it as auth.sh, and make sure it is owned by root and is          #
    #   executable.                                                             #
    #   # chown root:root auth.sh                                               #
    #   # chmod +x auth.sh                                                      #
    # - Adjust KEYPER_HOST per the hostname and port.                           #
    # - Adjust HTTP protocol to http or https per your configuration of keyper. #
    # - Add following lines to sshd_config file (%f is optional)                #
    #    AuthorizedKeysCommand /bin/sh /etc/ssh/auth.sh %u %f                   #
    #    AuthorizedKeysCommandUser root                                         #
    # - Make sure that HOST is set to the hostname defined in keyper console.   #
    # - Restart sshd                                                            #
    # - Test script by invoking it on CLI                                       #
    #   # /etc/ssh/auth.sh <username>                                           #
    # - Above must return a SSH public key of the user                          #
    #                                                                           #
    #############################################################################
    USER="$1"
    FP="$2"
    HOST=`hostname`
    KEYPER_HOST={{HOSTNAME}}

    CURL_ARGS="-s -q -f -m 7"
    CURL_URL_ARGS_GET="username=${USER}&host=${HOST}"
    CURL_URL_ARGS_POST="-Fusername=${USER} -Fhost=${HOST}"

    ## Use this if you want to get public keys using HTTP GET
    [ -z ${FP} ] || CURL_URL_ARGS_GET="${CURL_URL_ARGS_GET}&fingerprint=${FP}"
    curl ${CURL_ARGS} "http://${KEYPER_HOST}/api/authkeys?${CURL_URL_ARGS_GET}"

    ## Use this if you want get public keys using HTTP POST
    #[ -z ${FP} ] || CURL_URL_ARGS_POST="${CURL_URL_ARGS_POST} -Ffingerprint=${FP}"
    #curl ${CURL_ARGS} ${CURL_URL_ARGS_POST} http://${KEYPER_HOST}/api/authkeys

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