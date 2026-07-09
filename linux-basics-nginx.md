### EC2 Linux Basics and Nginx Setup

#### SSH Key Setup

Restrict the private key to owner-read-only before first use.
```
chmod 400 key.pem
```

#### Nginx Service Management

Check whether Nginx is installed and running.
```
sudo systemctl status nginx.service
```

Restart Nginx after a config or content change.
```
sudo systemctl restart nginx.service
```

#### Network Checks

List all listening TCP/UDP ports and the owning process.
```
ss -tulpn
```

Confirm the instance's public IP as seen from outside AWS.
```
curl ifconfig.me
```

#### System Resource Checks

Check disk usage per mounted filesystem.
```
df -h
```

Check disk usage excluding pseudo/tmpfs filesystems for a cleaner view.
```
df -xh
```

Check memory and swap usage.
```
free -h
```

#### Nginx Configuration Layout

Inspect the active site config.
```
cat /etc/nginx/sites-available/default
```

Default document root and index file precedence, from `sites-available/default`:
```
root /var/www/html;
index index.html index.htm index.nginx-debian.html;
```
`index.html` takes precedence over `index.nginx-debian.html` — dropping a new `index.html` into `/var/www/html/` overrides the default page without deleting it.

#### Deploying the Custom Website Page

Create the replacement page (preferred over editing the Debian default file directly).
```
sudo vim /var/www/html/index.html
```

Reload Nginx so the change is picked up.
```
sudo systemctl restart nginx.service
```

Verify locally from the instance before checking in a browser.
```
curl http://localhost
```

#### Troubleshooting

##### SSH refuses the key: `UNPROTECTED PRIVATE KEY FILE`

Cause: the `.pem` key file has group/world-readable permissions. AWS/OpenSSH reject keys that aren't owner-read-only.
```
chmod 400 key.pem
```

##### SSH session drops mid-edit: `client_loop: send disconnect: Broken pipe`

Cause: local network interruption or an idle connection timeout — not an Nginx/EC2 fault. If the drop happens before `:wq` in vim, no changes were written to disk.
```
# On the EC2 instance, work inside tmux so an SSH drop doesn't kill the session
tmux new -s setup
# reattach after reconnecting
tmux attach -t setup
```

Reduce future drops by enabling client-side keepalives in `~/.ssh/config` on the local machine.
```
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

##### Website not reachable on port 80 from a browser

Check the Security Group allows inbound TCP 80 from `0.0.0.0/0`, then confirm Nginx is actually listening.
```
ss -tulpn | grep :80
sudo systemctl status nginx.service
```

##### Wrong page still showing after edit

Cause: `index.nginx-debian.html` was edited instead of `index.html`, or the browser served a cached copy.
```
ls -l /var/www/html/
curl -I http://localhost
```
