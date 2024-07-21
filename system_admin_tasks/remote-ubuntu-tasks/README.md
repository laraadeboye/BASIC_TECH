# Working with a remote work server

1. SSH into the server
```sh
ssh username@IP
```
Enter the password.

2. Check OS details
```sh
cat /etc/os-release
```
or

```sh
uname -a
```
3. Check which users with access to the server:
```sh
cat /etc/passwd
```

4. Check which users are part of specific groups e.g `sudo`

```sh
getent group sudo
```

5. Check users that are currently logged in:
```sh
users
```
or 
```sh
who
```
or 
```sh
w
```

6. Install postgresql on server:
1. Check version
```sh
psql --version
```

2. Installation
- Add the PostgreSQL APT repository:
```sh
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

```
- Import the repository signing key:
```sh
wget -qO - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

```
- Update the package lists and install the latest stable version of PostgreSQL:
```sh
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
```
- Start the postgresql service
```sh
```
- Check the status
```sh
sudo systemctl status postgresql
```

7. Check the logs of activities on a linux ubuntu server

1. Check System logs
- Syslog: general system logs
```sh
sudo less /var/log/syslog

```
- Kernel log: Kernel logs
```sh
sudo less /var/log/kern.log

```
- Authentication logs
```sh
sudo less /var/log/auth.log

```
- Application logs: `/var/log`
have filenames that correspond to the application name, e.g.
```sh
cat `/var/log/nginx/error.log`
```
- Boot logs: Boot related messages
```sh
sudo less /var/log/boot.log
```

- Journal logs: Journalctl Provides access to systemd logs, which include all system messages.
```sh
sudo journalctl

# View the most recent logs with details
sudo journalctl -xe  

# To view logs for a specific time period, use:
sudo journalctl --since "2024-07-19" --until "2024-07-20"

# To view logs for a specific unit (e.g., PostgreSQL)
sudo journalctl -u postgresql
```

- Installation logs:
```sh
sudo less /var/log/dpkg.log

#with grep
sudo grep "postgresql" /var/log/dpkg.log```


2. Commands to streamline the checks
- `less`
Use less to view large log files interactively:
```sh
sudo less /var/log/syslog
```
Use arrow keys to navigate, `q` to quit.

- `tail` 

```sh
# Use tail to view the last few lines of a log file:
sudo tail /var/log/syslog
```

```sh
#Use `-f` option to follow the log file in real-time:
sudo tail -f /var/log/syslog
```

- `grep`
Use `grep` to search for specific patterns within log files:
```sh
# Example: To search for SSH login attempts:
sudo grep "sshd" /var/log/auth.log
# Example: To search specific error entries attempts:
sudo grep "error" /var/log/syslog
```