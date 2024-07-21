
# Setting up Crontab
By setting up crontab correctly, you can automate tasks like cleaning up cache, backing up data, and performing routine maintenance efficiently.

1. Open the Crontab file
```sh
crontab -e
```

2. Crontab syntax:
```sh
* * * * * /path/to/command
- - - - -
| | | | |
| | | | +----- Day of week (0 - 7) (Sunday=0 or 7)
| | | +------- Month (1 - 12)
| | +--------- Day of month (1 - 31)
| +----------- Hour (0 - 23)
+------------- Minute (0 - 59)

```
3. Add cron job
```sh
#example
0 0 * * * /path/to/your/script.sh

```

save and exit the text editor
Ensure the script being run is executable (use `chmod +x`)
Cron jobs run in a minimal environment. Ensure all required environment variables or paths are set within the script or specify them in the cron job itself.
4. List existing cron jobs
```sh
crontab -l
```

5. Troubleshooting cronjob not running:
- Check logs at  `/var/log/syslog` or  `/var/log/cron`
- Verify permissions
- Ensure script works  



