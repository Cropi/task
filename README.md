# Logging daemon task

## Description

1st part:
- Create a C program, which will create(own) and will read from the /dev/log socket
- It must accept any number of parameters
- Each parameter will be a file path to which will write certain information (see below)
- Each log that is read from the socket will be writen to STDOUT as well
- Due to the previous requirements, it must be executed as root
- The program won't exit unless it receives a SIGINT signal

2nd part:
- Detection of reccurring messages.
- If the daemon is stopped (receives a SIGINT), it will write to stdout:
  - the most frequented message
  - and a number indicating how message was detected

Potential enhancements:
- use of CMake in the project
- "-f" option for fork mode

### Before you start
First, it's good to get familiar with rsyslog, which is the rocket-fast system for log processing. Make sure you have it installed and it's running. On CentOS/Fedora:
```
dnf install rsyslog
systemctl start rsyslog
```

Rsyslog runs in the background as a daemon. You can verify it with the ```systemctl status rsyslog``` command. With the default configuration, it reads the /dev/log socket and writes each message to ```/var/log/messages```. You can test it with the ```logger(1)```,e.g.: ```logger "This is my test messages"``` and check the content of ```/var/log/messages```.

Before you write any code:
- Take a look at the implementation of [createLogSocket](https://github.com/rsyslog/rsyslog/blob/master/plugins/imuxsock/imuxsock.c#L510).
- Don't be afraid if you do not fully understand it.
- First, you need to unlink the existing ```/dev/log``` socket with ```unlink /dev/log```
- Stop ```rsyslog``` and ```systemd-journald``` via ```systemctl stop rsyslog``` and ```systemctl stop systemd-journald```

### Example output
Terminal 1:
```
alakatos@fedora  ~/c-task   master  sudo ./logging-daemon /tmp/output1.log /tmp/output2.log /tmp/output3.log
<13>May  5 15:20:08 alakatos: some message
<13>May  5 15:20:12 alakatos: some message
<13>May  5 15:20:12 alakatos: some message
<13>May  5 15:20:16 alakatos: some message
<13>May  5 15:20:23 alakatos: some other message
<13>May  5 15:20:24 alakatos: some other message
4 --> alakatos: some message
The END!
```

Terminal 2:
```
logger some message
logger some message
logger some message
logger some message
logger some other message
logger some other message
```
