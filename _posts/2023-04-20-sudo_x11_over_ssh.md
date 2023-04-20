# sudo X11 application over ssh

Scenario: you need to run an X11 application as root user on a remote machine

Unlike running console-based programs where you typically just:

1. ssh to remote machine
2. sudo *whatever-console-program*

To run X11 application as the ssh user, it is also straight-forward:

1. ssh to remote machine
2. *whatever-x11-application*

It gets a bit complicated when you need to run both. In my case, I wanted to run `gparted` as *root* on a remote linux machine.

1. Enable X11 forwarding on remote machine, */etc/ssh/sshd_config*

    ```text
    # Ensure that this line is present
    # If you had to modify the file, then you will have to restart sshd
    X11Forwarding yes
    ```

1. Start ssh session to remote machine with `-X` option

    ```bash
    ssh -X -C NON_ROOT_USER@remote_machine

    # Ensure X11 forwarding succeeded
    echo $DISPLAY
    ```

1. Swith remote user to *root* in the ssh session


    ```bash
    # Let the new shell inherit some environment variable crucial for X11/Wayland to work
    sudo -EH -s
    ```

1. Check connection access. The error is expected.

    ```bash
    xhost
    X11 connection rejected because of wrong authentication
    ```

1. Add NON_ROOT_USER credentials to root user /.Xauthority

    ```bash
    xauth add $(xauth -f ~NON_ROOT_USER/.Xauthority list | tail -1)
    xhost
    # Output message may vary
    access control enabled, only authorized clients can connect

    # Voila! Now you can run your X11 application
    ```

## Remove access credentials

```bash
xauth remove $DISPLAY
```

[source](https://www.ibm.com/support/pages/x11-forwarding-ssh-connection-rejected-because-wrong-authentication)
