# Write up for CVE id CVE-2020-15778.

# Discoverd by : Chinmay Pandya
>## Email address : cpandya2909@gmail.com
>## Linkedin : www.linkedin.com/in/chinmay-pandya


# Vulnerability title: scp in OpenSSH 8.3p1 allows eval injection.
>##                               Product: Openssh
>##                    Affected Component: SCP
>##                    Vulnerable version: <=openssh-8.3p1
>##                         Fixed version: -
>##                            CVE number: CVE-2020-15778
>##                       Vulnerable line: https://github.com/openssh/openssh-portable/blob/a2855c048b3f4b17d8787bd3f24232ec0cd79abe/scp.c#L989
>##                    Vulnerability type: Comand Injection / Eval injection (Authenticated)
>##                           Attack type: Remote attack
>## Discloser timelie:
>- Issue discovered - 9 Jun 2020
>- CVE obtained - 16 Jul 2020
>- Vendor notified - 9 Jun 2020
>- Vendor acknowledgement received - 9 Jun 2020
>- Public disclosure 18 Jul 2020

# About SCP
scp is a program for copying files between computers. It uses the SSH protocol. It is included by default in most Linux and Unix distributions
 
# Issue
While coping files to remote server, file path is appended at end of local scp command.
For example, if you execute following command
```sh
scp SourceFile user@host:directory/TargetFile
```

it will execute a local command 
```sh
scp -t directory/TargetFile
```

At time of creating local scp command, it does not sanitise file name.
An attacker can pass a backtick enabled payload as file name and when local scp command is executed, local shell will also execute backtick enabled payload.

## Example

Execute following command with scp
```sh
scp  /sourcefile remoteserver:'`touch /tmp/exploit.sh`/targetfile'
```

After executing this command, go to remote server and you will see in /tmp/ directory that file exploit.sh is present. Putting single quotes in file name is important to prevent payload execution on local shell or using escape character like single quotes in file name can prevent payload execution on local shell

***
**THIS IS AN AUTHENTICATED EXPLOIT AND REQUIRES A VALID SSH / SCP CREDENTIALS TO EXECUTE COMMAND ON REMOTE SERVER**
***

# Openssh reply 
>The scp command is a historical protocol (called rcp) which relies upon
that style of argument passing and encounters expansion problems.
>It has proven very difficult to add "security" to the scp model.  All
attempts to "detect" and "prevent" anomalous argument transfers stand a
great chance of breaking existing workflows.
>Yes, we recognize it the situation sucks.  But we don't want to break the
easy patterns people use scp for, until there is a commonplace replacement.
>People should use rsync or something else instead if they are concerned.

#  [Reference]
> Code for SCP - https://github.com/openssh/openssh-portable/blob/master/scp.c
