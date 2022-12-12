### [fixed] No wifi after sleep-suspend Ubuntu


#### About

* use computer: [SER5 Pro 5600H](https://www.bee-link.com/catalog/product/index?id=362)

* the problem: [No wifi after sleep-suspend Ubuntu](https://forum.bee-link.com/forum.php?mod=viewthread&tid=74847)

* kernel info:
    ```shell
    $ uname -a
    Linux char6ming-SER 5.15.0-56-generic #62-Ubuntu SMP Tue Nov 22 19:54:14 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
    ```
    
* os release info:
    ```shell
    $ cat /etc/os-release
    PRETTY_NAME="Ubuntu 22.04.1 LTS"
    NAME="Ubuntu"
    VERSION_ID="22.04"
    VERSION="22.04.1 LTS (Jammy Jellyfish)"
    VERSION_CODENAME=jammy
    ID=ubuntu
    ID_LIKE=debian
    HOME_URL="https://www.ubuntu.com/"
    SUPPORT_URL="https://help.ubuntu.com/"
    BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
    PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
    UBUNTU_CODENAME=jammy
    ```
    
#### How to fix

> use [Magister](https://forum.bee-link.com/home.php?mod=space&uid=202650)'s solution.

* **step 1**, create a shell script in the fullpath ```/usr/lib/systemd/system-sleep/remove-mt7921e.sh```, fill in with the following contents :
    ```shell
    #!/bin/bash
    [ "$1" = "post" ] && exec /usr/sbin/modprobe mt7921e
    [ "$1" = "pre" ] && exec /usr/sbin/modprobe -r mt7921e
    exit 0
    ```


* **step 2**, add executable permission :
    ```shell
    sudo chmod  a+x   /usr/lib/systemd/system-sleep/remove-mt7921e.sh
    ```


* **step 3**, reboot system :
    ```shell
    sudo init 6
    ```
