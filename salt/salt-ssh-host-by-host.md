# Running commands host-by-host (serially) with salt-ssh

I've been doing some work to automate configuration updates on our Clickhouse clusters, which may need to include a restart of the `clickhouse-server` on one server at a time - don't want to reboot all of our replicas at the same time! There didn't seem to be a straightforward way to do this, but after a bit of exploration I found the `--max-procs` option which sounded like it could do what I needed:

```
--max-procs=SSH_MAX_PROCS
    Set the number of concurrent minions to communicate
    with. This value defines how many processes are opened
    up at a time to manage connections, the more running
    processes the faster communication should be. Default:
    25.
```

Initially when setting this I was a bit confused, as it didn't seem to have any impact - I expected this command to take 15+ seconds if it was working, not 7:

```
$ time salt-ssh --max-procs 1 "snuba-metrics-0-*" -r 'sleep 5'
snuba-metrics-0-1:
    ----------
    retcode:
        0
    stderr:
        Warning: Permanently added '192.168.208.150' (ECDSA) to the list of known hosts.
    stdout:
snuba-metrics-0-0:
    ----------
    retcode:
        0
    stderr:
        Warning: Permanently added '192.168.208.199' (ECDSA) to the list of known hosts.
    stdout:
snuba-metrics-0-2:
    ----------
    retcode:
        0
    stderr:
        Warning: Permanently added '192.168.208.202' (ECDSA) to the list of known hosts.
    stdout:

real	0m7.144s
```

A teammate provided some info that got it working - we have a `~/.salt/Saltfile` which defines some defaults, and strangely will also overrule any command-line args that you provide:

```
$ cat ~/.salt/Saltfile
salt-ssh:
  config_dir: ./salt-ssh-config
  rand_thin_dir: True
  ssh_log_file: ./salt-ssh.log
  ssh_max_procs: 30
  ssh_wipe: True
  ssh_sudo: True
  ssh_options:
    - 'StrictHostKeyChecking=no'
    - 'UserKnownHostsFile=/dev/null'
  ssh_user: mwarkentin
```

Removing `ssh_max_procs: 30` and running `salt-ssh --max-procs 1` worked as expected, looping through the servers one by one.

After a bit of research I came across [this doc issue](https://github.com/saltstack/salt/issues/60210) which had an interesting line:

> The naming of the file tends to suggest it is some sort of config file, when in fact it is mere a "macro" to supply CLI options

So it seems like this `Saltfile` actually just gets rendered out into invisible CLI args, which explains why it was overriding my `--max-procs 1` instead of the other way around.
