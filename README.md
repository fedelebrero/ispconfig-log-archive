# ISPConfig log archive

Bash utility for Debian servers running ISPConfig. It archives rotated, compressed web logs from each site's `log` directory into monthly directories beneath that site:

```text
/var/www/clients/clientX/webY/log/archive/AAAAMM/
```

## Archived files

- Access logs named `YYYYMMDD-access.log.gz` are archived using the date in the filename.
- Error logs named `error.log.N.gz` are archived using their modification time and are renamed to `YYYYMMDD-error.log.gz`.
- If more than one error log has the same date, the later files receive `-01`, `-02`, and so on. No suffix is used unless required.
- Logs dated today or in the future are never moved.
- The retention window is five calendar years: the current year and the preceding four. Older monthly archive directories are removed.

The script is intended for GNU/Linux Debian systems and uses GNU `date`, `stat`, `mv`, and `flock`.

## Installation

Install the script and cron file as root:

```sh
install -m 0755 archive-site-logs /usr/local/sbin/archive-site-logs
install -D -m 0644 cron/archive-site-logs /etc/cron.d/archive-site-logs
```

Cron runs the archive every day at 03:30 as root. The script serializes concurrent invocations with `/run/lock/archive-site-logs.lock` and appends output to `/var/log/ispconfig-log-archive.log`.

## Validation

Run a non-destructive preview before enabling it:

```sh
/usr/local/sbin/archive-site-logs --dry-run
```

Review `/var/log/ispconfig-log-archive.log`. Then run the command without `--dry-run`, or allow the scheduled job to run.
