# SSH Server Config

## Server Config (Key Based Authentication)
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
4. Add following parameters to sshd_config. For KRL verification %f (Key fingerprint) is must.
    ```console
    AuthorizedKeysCommand /bin/sh /etc/ssh/auth.sh %u %f
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
    # 1. username: linux username of the user trying to login. It is a required #
    #              parameter. It is set using %u on AuthorizedKeysCommand       #
    # 2. fingerprint: SSH Key finger print. It is an optional parameter. It is  #
    #                 set using %f on AuthorizedKeysCommand                     #    
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
    CURL_ARGS="${CURL_ARGS} --data-urlencode username=${USER}"
    CURL_ARGS="${CURL_ARGS} --data-urlencode host=${HOST}"

    [ -z ${FP} ] || CURL_ARGS="${CURL_ARGS} --data-urlencode fingerprint=${FP}"

    ## Use this if you want to get public keys using HTTP GET
    curl -G ${CURL_ARGS} http://${KEYPER_HOST}/api/authkeys

    ## Use this if you want get public keys using HTTP POST
    #curl ${CURL_ARGS} http://${KEYPER_HOST}/api/authkeys

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

## Server Config (Certificate Based Authentication)
The following instructions were written for RHEL/CentOS type of Linux systems. If you are using any other flavor, you may need to tweak the instructions a little.

Following steps need to be performed on each linux server you want to be part of keyper and perform certificate based authentication:
1. Login as root on linux server
2. Go to SSH config directory
    ```console
     # cd /etc/ssh
     ```
3. Download authprinc.sh.txt file from keyper
    ```console
    # wget http://keyper.example.org/scripts/authprinc.sh.txt
    # mv authprinc.sh.txt authprinc.sh
    # chmod +x authprinc.sh
    ```
4. Add following parameters to sshd_config. For KRL verification %s must be added)
    ```console
    AuthorizedPrincipalsCommand /bin/sh /etc/ssh/authprinc.sh %u %f %s
    AuthorizedPrincipalsCommandUser root
    ```
5. Make appropriate tweaks to the script authprinc.sh. Especially check for KEYPER_HOST and http/https. Uncomment curl call line per your preference of http GET vs POST.
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
    # This scripts does the work of AuthorizedPrinciaplsFile by calling REST    #
    # API and gets Principal for the user attempting to login using SSH.        #
    #                                                                           #
    # The script accepts two parameters:                                        #
    # 1. username: linux username of the user trying to login. It is a required #
    #              parameter. It is set using %u on AuthorizedKeysCommand       #
    # 2. fingerprint: SSH Key finger print. It is an optional parameter. It is  #
    #                 set using %f on AuthorizedKeysCommand                     #    
    # 3. serial: Certificate Serial No. It is set using %s on                   #   
    #         AuthorizedPrincipalsCommand                                       #                          
    # - Deploy this script under /etc/ssh (or corresponding location in your    #
    #   distro).                                                                #
    # - Rename it as authprinc.sh, and make sure it is owned by root and is     #
    #   executable.                                                             #
    #   # chown root:root authprinc.sh                                          #
    #   # chmod +x authprinc.sh                                                 #
    # - Adjust KEYPER_HOST per the hostname and port.                           #
    # - Adjust HTTP protocol to http or https per your configuration of keyper. #
    # - Add following lines to sshd_config file (%f is optional)                #
    #    AuthorizedPrincipalsCommand /bin/sh /etc/ssh/authprinc.sh %u %f %s     #
    #    AuthorizedPrincipalsCommandUser root                                   #
    # - Make sure that HOST is set to the hostname defined in keyper console.   #
    # - Restart sshd                                                            #
    # - Test script by invoking it on CLI                                       #
    #   # /etc/ssh/authprinc.sh <username> <fingerprint> <serial>               #
    # - Above must return a princiapl name for user                             #
    #                                                                           #
    #############################################################################
    USER="$1"
    FP="$2"
    HOST=`hostname`
    SERIAL="$3"
    KEYPER_HOST={{HOSTNAME}}

    CURL_ARGS="-s -q -f -m 7"
    CURL_ARGS="${CURL_ARGS} --data-urlencode username=${USER}"
    CURL_ARGS="${CURL_ARGS} --data-urlencode host=${HOST}"

    [ -z ${FP} ] || CURL_ARGS="${CURL_ARGS} --data-urlencode fingerprint=${FP}"
    [ -z ${SERIAL} ] || CURL_ARGS="${CURL_ARGS} --data-urlencode serial=${SERIAL}"

    ## Use this if you want to get public keys using HTTP GET
    curl -G ${CURL_ARGS} http://${KEYPER_HOST}/api/authprinc

    ## Use this if you want get public keys using HTTP POST
    #curl ${CURL_ARGS} http://${KEYPER_HOST}/api/authprinc

    exit $?
    ```
6. Test the script
    ```console
    # ./authprinc.sh alice astrix SHA256:Y5WAXkMI9FxrgXaiST50Wf8mwqiWT7hdHDSNBK8b16I
    alice
    ```
    If the above returns appropriate key, you are good to go.
7. Download CA's user public key and save it as /etc/ssh/ca_user_key.pub
    ```console
    # curl http://keyper.example.org/api/userca
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDY2Usk+et4K6UE7ohKJz5ApKUVXDeaSg6cfkL1tFPE/piyuxmu8kZ5L+QWKlGJGwTnARTeFBr4rskHNhCvcCUEw0iakYVFCMfRwrfIwLXfG85ldWV18akPDJV5PDhTaDszzC4PSmVr18hL0tPRxctU9z0gwgBCbGdLxMFaTw1r20XWJNeceGfBx9f/OaXckYBu8wZ1V75xm6iuQap9VFhOSwLPnjlCJc4tu+7YnFubn/mw3WnIBIzCxQbxAdP91wHolei0p330f82j+w58F/lC7ZaP+HBgSmh1AvabI0NT54YlIH7OTZkMo7cCRZScMImhYRzcqV2BN6fM8De5UKiPW2GztNon4jsZOg0JFWMz8bGQ5y4Rr5kivt0PcRjUr1j1/MX3SVfY4u8qjNx/za2+cRNTiy3izr/4nWMxEjB3hbpLo9x0kdt7QsHeFwlc8ADX/4CjilBk9X3wm5FKnJruLuF0jSjw8hGkSWhB2dD96NoIsqou2uAZk9PS6sy9ugE=
    ```
8. Download hosts signed certificate from CA either using web console or CLI and save it under ssh_host_rsa-cert.pub (name the cert appropriately per either RSA/DSA/ed25519 format user)
    ```console
    # curl http://keyper.example.org/api/hostcert?hostname=astrix
    ssh-rsa-cert-v01@openssh.com AAAAHHNzaC1yc2EtY2VydC12MDFAb3BlbnNzaC5jb20AAAAgFfYaK4ZcvY+xUuZHZs3i//jT90X24AEB1G3afV+Nv84AAAADAQABAAABgQCpuKUaryiGo8Lx/Eov51o4ojBOdu4s/s1bdTKStgv6FixqHKPWsPbNF+8J/+ODxEz09KB4cD6/OjNvuOWDkavnqKLMY8lipdVqaYxFuESSy/mf1o4gU+92+YDxsy4GzWchHcbOfxJ1mPogTHltcRt3q1R3pj5WVXotN6fUswKFEJypw9IrIr+LQ6D4qvvcY6MJBfqgSE3/SiDwp2lX+loLLd/BCoPIE/bVzx5TnnkM43upWtsiHNd5KPUrFsL7aJ5CpoQ4ZJRr/KxMov9NZGLwYjslyf4MAjwoDTp8U+4PwjecfEyoTPAlfFLBZNm2EQ4g8q+AlQ/riCRQjt/ftJG1ecSW9ypbgsj9pcksGxtPtg7pMq9Si0XMtOaMlWArJZFKLJEVF505eqe6iotal+/zP/hR7mpCCHFqW45q848aJvYLM0AwlQTEFyMEXzl2z9UTZ0Oi8pEEy9JwuDtsUhrGTbyv0qUpp/jalBorvRgcehxYJNIc3SPGNrKUQ+T5cQv0c+kRYD+DoAAAAAIAAAAIZ2V0YWZpeDIAAAAlAAAACGdldGFmaXgyAAAAFWdldGFmaXgyLmRic2VudHJ5LmNvbQAAAABfly6AAAAAAGF3EPMAAAAAAAAAAAAAAAAAAAGXAAAAB3NzaC1yc2EAAAADAQABAAABgQCXVal4chk2nqQWYqCmFZSLOooKzpeuGUxtgIZlw2XE/Jh37cZrs2DPXGdy4H92qZ8ab/00bnbpguEEHDkYvdZ+cyVk4w6g0iVHIONG0uO/66smioggvMZEZ8IftChwgXBxkAiAwJf+zKEvRE1rbLJON4e5dnmHosWcAhi+KhkKEw5rSXUtRUH/r2IR/9z468Ky/nOq7P0LrF791RDSIjDVTt3V3Q7v3nKoQepkmEhMS+/lEZkMoFvI3bpHx1R3XlGqUnHJyvNUT095p1BsClFCfhGEcJQgDq57AbkxUhaYO7/tubFWUdn+IKFgVjRSutvNNQTswQSPdi0UdEiLxBtR6oWSJiHl8BD5F5b4qeIJNoB2qViwK1G2V3ZYttU7x6rRRJuUdq3oWcazA0wiB0H4BVH4svG7yEzRxc/gvQdVQM6vh7RYM0Iwx5VkzN61A6yf6cnIj//4YNSZ+dtNy7Cf0TplQQpDUFO0HTNRaPD8OUg8zM+P/pXGwIpVhu8wBeEAAAGUAAAADHJzYS1zaGEyLTUxMgAAAYAPxFfHS8fyl6TXgCjuMNku538/tcSNdqZKrUg/Ius/qIxwIyRfCWLYuPkR65Xj3rYS8Dj9q+G+Ha6NkJ5eLWI/lL9NNx5uPkYI/NcbtjWlI00WBNi8qFJsPre/qo3nhTrO/7hHqVHKoXTDPLM0XDfPgXYfqv21/NvZPHNplH80GEQr344Y+Y25OZIsCq28Ykuv8+BfKwGJohogYdrvd2FpoFc/YAlgiCmvvGXbjzgO4+VGWBoeEXpaXB+/0kmqkGYzrkPO4YG0bQovVHPYgNsaoSPjei5YZUj2j/2F08LQ5CVNwG6jN8ECyVdNneOCJlw1ahpCKCEeaRJ2gP0uqGAVuXbR3T1EXj7GrkO4NFcDC17AiyCddscLx1MoippcrLirdvLXR+ejtftxmYyrxDztxQstSbM3mr76rnwoFT4l76aCdXWI9nOp9t8gbd4XFwXi4xBY9oaODEteVORuQe1AJvLvZm1NsuSr9ocO104KDDU1AJiUU5HEku3RtkytL2I= /etc/sshca/tmp/tmp7hqrd80i.pub
    ```
9. Add following lines to sshd_config file
    ```console
    TrustedUserCAKeys /etc/ssh/ca_user_key.pub
    HostCertificate /etc/ssh/ssh_host_rsa_key-cert.pub
    ```
    TrustedUserCAKeys instructs SSH to trust user certificates signed by ca_user_key.
    HostCertificate instructs SSH to send signed host certificate to the client when requested.
7. Restart SSH daemon for SSH to start calling auth.sh during authentication
    ```console
    # systemctl restart sshd
    ```

## Client Config (Certificate Based Authentication)
Following steps need to be performed on the client you plan to use for Certificate based authentication. These steps assume that you have already uploaded your user key to Keyper for signing:
1. Download CA's host public key.
    ```console
    # curl http://keyper.example.org/api/hostca
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCXVal4chk2nqQWYqCmFZSLOooKzpeuGUxtgIZlw2XE/Jh37cZrs2DPXGdy4H92qZ8ab/00bnbpguEEHDkYvdZ+cyVk4w6g0iVHIONG0uO/66smioggvMZEZ8IftChwgXBxkAiAwJf+zKEvRE1rbLJON4e5dnmHosWcAhi+KhkKEw5rSXUtRUH/r2IR/9z468Ky/nOq7P0LrF791RDSIjDVTt3V3Q7v3nKoQepkmEhMS+/lEZkMoFvI3bpHx1R3XlGqUnHJyvNUT095p1BsClFCfhGEcJQgDq57AbkxUhaYO7/tubFWUdn+IKFgVjRSutvNNQTswQSPdi0UdEiLxBtR6oWSJiHl8BD5F5b4qeIJNoB2qViwK1G2V3ZYttU7x6rRRJuUdq3oWcazA0wiB0H4BVH4svG7yEzRxc/gvQdVQM6vh7RYM0Iwx5VkzN61A6yf6cnIj//4YNSZ+dtNy7Cf0TplQQpDUFO0HTNRaPD8OUg8zM+P/pXGwIpVhu8wBeE=
    ```
2. Add above key to the known_hosts file for the user in following format:
    ```
    cert-authority *.dbsentry.com <host CA key>
    ```
    Replace hosts in your domain above instead of using *.dbsentry.com. Above instructs SSH to trust all host certificates signed by CA's host key.
    Delete all older entries in known_hosts file that you plan to use for certificate based authentication.
3. Test SSH and make sure you do not get TOFU warning
4. Download users signed certificate from CA either using web console or CLI and save it under .ssh/id_ed25519-cert.pub (name the cert appropriately per either RSA/DSA/ed25519 format for the key)
    ```console
    # curl http://keyper.example.org/api/usercert?username=alice&keyid=100
    ssh-ed25519-cert-v01@openssh.com AAAAIHNzaC1lZDI1NTE5LWNlcnQtdjAxQG9wZW5zc2guY29tAAAAIH+qOtaZQV6+TdvyQYznEOy/qsu6WvKj8yPKqjr3MN74AAAAIOneaLgpzgUK4p80a7niJMmV8PdcTxrk9VeTXA7BO60r+F9Vpf0S5doAAAABAAAABm1hbmlzaAAAAB4AAAAGbWFuaXNoAAAABm1ndXB0YQAAAAZhcGFjaGUAAAAAX5cw2AAAAABgDdhNAAAAAAAAAIIAAAAVcGVybWl0LVgxMS1mb3J3YXJkaW5nAAAAAAAAABdwZXJtaXQtYWdlbnQtZm9yd2FyZGluZwAAAAAAAAAWcGVybWl0LXBvcnQtZm9yd2FyZGluZwAAAAAAAAAKcGVybWl0LXB0eQAAAAAAAAAOcGVybWl0LXVzZXItcmMAAAAAAAAAAAAAAZcAAAAHc3NoLXJzYQAAAAMBAAEAAAGBANjZSyT563grpQTuiEonPkCkpRVcN5pKDpx+QvW0U8T+mLK7Ga7yRnkv5BYqUYkbBOcBFN4UGviuyQc2EK9wJQTDSJqRhUUIx9HCt8jAtd8bzmV1ZXXxqQ8MlXk8OFNoOzPMLg9KZWvXyEvS09HFy1T3PSDCAEJsZ0vEwVpPDWvbRdYk15x4Z8HH1/85pdyRgG7zBnVXvnGbqK5Bqn1UWE5LAs+eOUIlzi277ticW5uf+bDdacgEjMLFBvEB0/3XAeiV6LSnffR/zaP7DnwX+ULtlo/4cGBKaHUC9psjQ1PnhiUgfs5NmQyjtwJFlJwwiaFhHNypXYE3p8zwN7lQqI9bYbO02ifiOxk6DQkVYzPxsZDnLhGvmSK+3Q9xGNSvWPX8xfdJV9ji7yqM3H/Nrb5xE1OLLeLOv/idYzESMHeFukuj3HSR23tCwd4XCVzwANf/gKOKUGT1ffCbkUqcmu4u4XSNKPDyEaRJaEHZ0P3o2giyqi7a4BmT09LqzL26AQAAAZQAAAAMcnNhLXNoYTItNTEyAAABgDpZ9LCWVcPsNjk4ImcZJkvlZRstwAnV2Zmsct6isCyiOiRbSAtCXDrWyN4ehTUp7zn/JWxxrAYvKy1wXTdhn4m3H3e875UkITAYU8jAiNEv4fsck835JsukKXQnU0iNIdAy5RqnCyuBRCs7FIi9DRorbrtEGBgyYq3Tx0KyzTogKvQgCpyRHbopZLS1rPGBteEIpG7IZ1vr8oKchgTJ2BfYZb/Kek+dzye4xAQuQnacpoZKazq0D/79dGKkOC+/cjO8v9UAjlzxeW5/ZyRHvovipX2D5EfaLiyCpLpq1wpj+bTVbXrlbLeLhPbDedaVEHxJu1eWZlNYZQb7KtOP8Wk/tgoZQbTPf7t2pCqm33YXTPIVBlROKL3I2aAdOCbaD0+D9P5JZSq8wkPDFsXNmDsbc2NjzbPAk4BdryICp2+vMi977CgXX+7klbZZTjGbUzLsDoZCZBjY26dPocydli80iLj3LqTGbjaUyJIVqeB7R6gxV9IhmElBNUYuxCjozg== manish@picanmix4
    ```
5. Test SSH Authentication. You should not get TOFU warning and be able to login without being asked for the password.
