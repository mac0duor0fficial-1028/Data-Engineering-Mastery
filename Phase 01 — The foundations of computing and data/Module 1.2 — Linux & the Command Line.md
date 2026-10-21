# Module 1.2 — Linux &amp; the Command Line 🐧

> Goal: work confidently in a Ubuntu Linux terminal — files, permissions, processes, scripting, SSH, package management — and inspect real data (CSV) from the command line.

---

## 1. Getting Ubuntu Linux


| Method                     | How                                                                                | Best for                            |
| -------------------------- | ---------------------------------------------------------------------------------- | ----------------------------------- |
| **WSL2 (Windows)**         | Admin PowerShell: `wsl --install -d Ubuntu`, then reboot &amp; create a Linux user | Daily practice on a Windows PC      |
| **Virtual Machine**        | VirtualBox/VMware → Ubuntu Server or Desktop ISO                                   | Full Linux experience, safe sandbox |
| **Dual boot / bare metal** | Install directly                                                                   | Long-term dedication                |


**WSL2 tips:**

- `wsl --set-default-version 2`, `wsl -l -v` to verify.
- Access Windows files from Linux: `/mnt/c/Users/YourName/...`
- Access Linux files from Windows: `\\wsl$\Ubuntu\home\you\`
- Update after install: `sudo apt update && sudo apt upgrade -y`

**The shell** = your command interpreter (Bash by default). Terminal = the window; shell = the program reading your commands.

---

## 2. Files, Directories, Paths

### The filesystem tree

- Everything starts at **root `/`** (no drive letters like C:).
- Key directories: `/home` (users), `/etc` (config), `/var` (logs, variable data), `/tmp` (temporary), `/usr` (programs), `/bin`, `/sbin` (executables).

### Paths

- **Absolute**: starts with `/` → `/home/macphalen/data/sales.csv`
- **Relative**: relative to current directory → `data/sales.csv`, `..` (parent), `.` (current), `~` (your home), `-` (previous directory)

### Everything is a file

Devices (`/dev/sda`), terminals, and processes (`/proc`) are exposed as files — a core Linux philosophy.

---

## 3. Core Commands (the daily toolkit)


| Command | Purpose                               | Useful examples                                             |
| ------- | ------------------------------------- | ----------------------------------------------------------- |
| `pwd`   | Print working directory               | `pwd`                                                       |
| `ls`    | List files                            | `ls -lah` (long, all, human-readable sizes)                 |
| `cd`    | Change directory                      | `cd ~`, `cd -`, `cd ..`                                     |
| `mkdir` | Make directory                        | `mkdir -p reports/2026/q1` (nested)                         |
| `cp`    | Copy                                  | `cp -r dir1 dir2` (recursive)                               |
| `mv`    | Move / rename                         | `mv old.txt new.txt`                                        |
| `rm`    | Remove (⚠️ permanent, no recycle bin) | `rm -r dir`, `rm -i file` (confirm)                         |
| `cat`   | Print whole file                      | `cat report.txt`                                            |
| `less`  | Page through a file                   | `less big.log` — `/search`, `q` quit, `G` end               |
| `grep`  | Search text / filter                  | `grep -i error app.log`, `grep -n -r "TODO" .`              |
| `find`  | Find files                            | `find . -name "*.csv"`, `find /var -size +100M`             |
| `head`  | First lines                           | `head -n 5 sales.csv`                                       |
| `tail`  | Last lines / follow                   | `tail -f app.log` (live-stream updates)                     |
| `sort`  | Sort lines                            | `sort -t, -k3 -n data.csv` (by 3rd CSV column, numerically) |
| `cut`   | Extract columns                       | `cut -d, -f1,3 sales.csv` (fields 1 &amp; 3)                |
| `wc`    | Count lines/words/chars               | `wc -l sales.csv`                                           |


**Other essentials:** `man cmd` (manual, your best friend), `touch file`, `nano file` (simple editor), `history`, `!!` (repeat last), `Tab` completion, `↑/↓` history.

---

## 4. Permissions &amp; Ownership

`ls -l` output decoded:

```text
-rwxr-xr--  1  macphalen  staff  4096  Oct 21 09:14  report.sh
└┬┘└┬┘└┬┘    └── owner ──┘└ group ┘
 │  │  └ others: read only
 │  └── group: read + execute
 └── owner: read + write + execute
File type: - file, d directory, l symlink
```

- **r** = 4, **w** = 2, **x** = 1 → `rwxr-xr--` = `754`

### Changing permissions/ownership

```bash
chmod 755 script.sh        # octal
chmod u+x script.sh        # symbolic: add execute for user
chown alice file.txt       # change owner (needs sudo)
chown alice:staff file.txt # owner:group
sudo chown -R macphalen:macphalen /data   # recursive
```

- Directories need **x** to be entered, **r** to list contents.
- `sudo` = run as superuser ("do as root") — use sparingly.

---

## 5. Pipes &amp; Redirection

### Redirection

```bash
ls -l > list.txt        # stdout → file (overwrite)
ls -l >> list.txt       # stdout → file (append)
cmd 2> errors.log       # stderr → file
cmd > out.log 2>&1      # both stdout and stderr → same file
cmd < input.txt         # file → stdin
```

### Pipes — chain commands

`cmd1 | cmd2` sends stdout of cmd1 into stdin of cmd2:

```bash
cat sales.csv | grep -i "nairobi" | wc -l
ps aux | grep nginx
history | grep ssh
sort data.csv | uniq -c | sort -rn     # frequency count
```

Classic philosophy: **small tools, composed together.** Also: `tee` (split stream to file + stdout), `xargs` (build command args from input).

---

## 6. Environment Variables &amp; Shell Config

```bash
echo $USER $HOME $PATH
export PROJECT_DIR=/home/macphalen/projects   # session-only
echo $PROJECT_DIR
env                       # list all variables
```

- **`$PATH`** = where the shell looks for executables. Add: `export PATH="$PATH:/opt/tools/bin"`
- **Persist** by adding to `~/.bashrc` (per-shell) or `~/.profile` (login). Then `source ~/.bashrc`.

---

## 7. Processes, Ports, Services, Logs, Exit Codes

### Processes

```bash
ps aux                 # all processes
top / htop             # live monitor
kill <pid>             # terminate
kill -9 <pid>          # force kill
sleep 300 &            # run in background; fg brings it back
```

### Ports

```bash
ss -tulpn              # listening ports + owning process
sudo lsof -i :8080     # what's using port 8080
curl -I http://localhost:3000
```

### Services (systemd)

```bash
sudo systemctl start|stop|restart|status nginx
sudo systemctl enable nginx   # start at boot
journalctl -u nginx -f        # service logs, live
```

### Logs

- `/var/log/syslog` — system events
- `/var/log/auth.log` — logins/auth
- `/var/log/nginx/`, app-specific dirs
- View live: `tail -f /var/log/syslog | grep --line-buffered error`

### Exit codes

- `$?` holds the last command's exit status: **0 = success**, non-zero = failure.

```bash
ls /nope 2>/dev/null; echo $?      # 2
ping -c1 host >/dev/null && echo "up" || echo "down"
```

---

## 8. Bash Scripting &amp; Scheduling

### Script basics (`inspect.sh`)

```bash
#!/usr/bin/env bash
set -euo pipefail        # exit on error, undefined var, failed pipe

FILE="${1:?Usage: $0 <csv-file>}"

echo "== Report for $FILE =="
echo "Total lines: $(wc -l < "$FILE")"
echo "Header: $(head -n 1 "$FILE")"
```

```bash
chmod +x inspect.sh
./inspect.sh sales.csv
```

Key pieces: shebang `#!`, variables `$var`, args `$1 $2 $@`, `if/then/fi`, `for i in ...; do ... done`, functions, `[[ ]]` tests, arithmetic `$((2+2))`.

### Scheduling

```bash
crontab -e
# m  h  dom  mon  dow  command
0 6 * * *   /home/macphalen/scripts/backup.sh     # daily 06:00
*/5 * * * *  /home/macphalen/scripts/check.sh      # every 5 minutes
0 0 1 * *    /home/macphalen/scripts/report.sh    # monthly
```

Alternative: `at` (one-off jobs). Logs of cron runs go to syslog.

---

## 9. SSH, Keys &amp; Remote Servers

```bash
ssh user@server-ip            # login to remote
ssh -i ~/.ssh/mykey user@host # with a specific key
scp file.txt user@host:/path/ # copy to remote
rsync -avz dir/ user@host:dir/  # sync (efficient)
```

### Key-based authentication (no passwords)

```bash
ssh-keygen -t ed25519 -C "macphalen@laptop"   # creates ~/.ssh/id_ed25519 + .pub
ssh-copy-id user@server                       # install public key on server
```

- **Private key** (`id_ed25519`) stays secret on your machine; **public key** (`.pub`) goes to the server's `~/.ssh/authorized_keys`.
- `~/.ssh/config` shortcuts:

```text
Host retail-server
    HostName 203.0.113.10
    User macphalen
    IdentityFile ~/.ssh/id_ed25519
```

→ now just: `ssh retail-server`. Secure permissions matter: `chmod 700 ~/.ssh; chmod 600 ~/.ssh/*`

---

## 10. Package Management &amp; Troubleshooting (Ubuntu/Debian)

```bash
sudo apt update            # refresh package index
sudo apt upgrade           # install updates
sudo apt install python3   # install
sudo apt remove python3    # uninstall
apt search <term>          # find packages
apt show <package>         # details
dpkg -l | grep nginx       # installed packages
```

Other ecosystems: `pip` (Python), `npm` (Node), `snap`, `curl | bash` installers (prefer apt/snap when possible).

### Troubleshooting checklist

1. Read the error message carefully — it usually tells you the problem.
2. Command not found → check `$PATH`, install the package, check spelling.
3. Permission denied → do you need `sudo`? Wrong owner? `ls -l`.
4. Service won't start → `systemctl status`, `journalctl -u <svc>`.
5. Disk full? `df -h` (filesystem usage), `du -sh dir/*`.
6. Port busy? `ss -tulpn`.
7. Weird behaviour → `man cmd`, then search the exact error online.
8. Network down → `ping 8.8.8.8`, `ip a`.

---

## 11. 🧪 Exercise: Retail-Sales CSV Inspection from the Terminal

**Task:** download a retail-sales CSV, inspect it, find missing values / malformed rows, count records, and write a report file.

### Step 1 — Download

```bash
mkdir -p ~/data && cd ~/data
# use any retail sales CSV, e.g. a Kaggle/"Superstore" sample:
wget -O sales.csv "https://example.com/retail-sales.csv"   # or curl -o sales.csv <url>
ls -lh sales.csv
```

### Step 2 — First inspection

```bash
head -n 5 sales.csv                    # peek at header + rows
wc -l sales.csv                        # total lines (≈ records + 1 header)
tail -n 3 sales.csv                    # check the end of file
file sales.csv                         # confirm it's text
less sales.csv                         # browse; / to search
```

### Step 3 — Find missing values

```bash
# rows where any field is empty: ,, 
grep -n ",," sales.csv | head
# rows with an empty last field: trailing comma
grep -n ",$" sales.csv
awk -F',' 'NF != expected_cols {print NR": "$0}' sales.csv   # wrong column count
```

### Step 4 — Find malformed rows

```bash
# row counts per line — all should equal the header's count:
awk -F',' '{print NF}' sales.csv | sort | uniq -c
# negative or non-numeric amounts:
awk -F',' '$3 ~ /^-/ {print "negative:", $0}' sales.csv
grep -E ",[A-Za-z]+," sales.csv | head    # text where numbers expected (adjust)
```

### Step 5 — Counts &amp; quick stats

```bash
tail -n +2 sales.csv | wc -l                     # data rows (excluding header)
cut -d',' -f2 sales.csv | tail -n +2 | sort | uniq -c | sort -rn   # top categories
awk -F',' 'NR>1 {sum+=$3} END {print "avg:", sum/(NR-1)}' sales.csv
```

### Step 6 — Redirect everything to a report

```bash
REPORT=~/data/report.txt
{
  echo "=== Retail Sales Data Inspection Report ==="
  echo "Date: $(date)"
  echo "File: $PWD/sales.csv  ($(du -h sales.csv | cut -f1))"
  echo "Total lines: $(wc -l < sales.csv)"
  echo "Data rows (excl. header): $(tail -n +2 sales.csv | wc -l)"
  echo "--- Rows with missing values ---"
  grep -c ",," sales.csv | xargs echo "Count:"
  grep -n ",," sales.csv | head -n 10
  echo "--- Column count distribution ---"
  awk -F',' '{print NF}' sales.csv | sort | uniq -c
} > "$REPORT"
cat "$REPORT"
```

### One-shot script version

Save as `inspect.sh`, `chmod +x inspect.sh`, run `./inspect.sh sales.csv`:

```bash
#!/usr/bin/env bash
set -euo pipefail
FILE="${1:?Usage: $0 <csv>}"
echo "Rows: $(wc -l < "$FILE") | Missing-value rows: $(grep -c ',,' "$FILE" || true)"
awk -F',' '{print NF}' "$FILE" | sort | uniq -c | sed 's/^/Columns /'
```

---

## 12. Quick Reference Card

```bash
# navigation        # text tools          # search/filter
pwd  ls  cd         cat less head tail    grep find sort cut wc
# permissions       # redirection         # system
chmod chown ls -l   >  >>  2>&1  |        ps top kill ss systemctl journalctl
# scripting         # remote              # packages
bash  crontab -e    ssh scp rsync         apt update/install/search
```

**Pro habits:** `man` first, Tab-complete everything, never run `rm -rf` casually, check exit codes `$?`, script repetitive tasks, and read the logs.
