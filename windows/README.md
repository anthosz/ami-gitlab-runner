The [Packer template](./packer.json) and accompanying scripts,
[`bootstrap.txt`](./bootstrap.txt) and [`configure.ps1`](./configure.ps1), are
copied with minor changes from [James
Nugent](https://github.com/jen20/packer-aws-windows-ssh) who explained his
code on his
[blog](https://operator-error.com/2018/04/16/windows-amis-with-even/). It
installs and configures a [Windows port of
OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) on the free Amazon-owned
AMI for Windows Server 2019, which does not come with an SSH server.

Sadly, there is a long delay between when an AWS instance launched with this
AMI enters the state "running" and when the SSH server on it is running, for
the same reason that [password data is not immediately
available](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-password-data.html),
up to 15 minutes. This is just an unfortunate consequence of [deploying
a service (SSH) on
Windows](https://stackoverflow.com/questions/5080445/why-does-azure-deployment-take-so-long)
it seems.

Docker Machine is smart enough to wait for "SSH to be available", but it runs
runs into an error when Docker Machine tries to
[provision](https://docs.docker.com/machine/reference/provision/) it. There is
an [open issue](https://github.com/docker/machine/issues/2907) in the Docker
Machine project for provisioning Windows machines. They had not started
working on it because there was no easy way out-of-the-box for connecting to
a Windows machine. Now that we have SSH, I'm hoping they'll reconsider it.

```
Error creating machine: Error detecting OS: Error getting SSH command: ssh command error:
command : cat /etc/os-release
err     : exit status 1
output  : cat : Cannot find path 'C:\etc\os-release' because it does not exist.
At line:1 char:1
+ cat /etc/os-release
+ ~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (C:\etc\os-release:String) [Get-Content], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetContentCommand
```
