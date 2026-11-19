# Demo server for App Review

Apple's reviewers have to be able to log in somewhere, or OmniSSH will likely be rejected under
guideline 2.1 ("we were unable to review your app"). OmniSSH signs in with keys only, and a
reviewer's key is created fresh on their test device. So the demo server accepts **any** key, but
only for one locked-down `reviewer` account on a throwaway machine.

Cost: about $4–6 a month. Keep it running until the app is approved, and keep it for future updates if you like.

## 1. Create a small server

Any provider works. For example:

- **Hetzner Cloud** CX22, about €4/month
- **DigitalOcean** Basic droplet, $4–6/month
- **AWS Lightsail**, $5/month

Choose Ubuntu 24.04 LTS and add your own SSH key while creating it. Write down its public IP
address, or give it a DNS name such as `demo.chengjianglong.com`.

Use a separate machine for this. Never put this setup on a computer you care about.

## 2. Set it up

Log in as root (`ssh root@<server-ip>`) and paste:

```bash
set -e
apt-get update && apt-get -y upgrade
apt-get -y install tmux htop git curl ufw fail2ban

# The account reviewers use: no password, no sudo.
adduser --disabled-password --gecos "App Review" reviewer

# Accept any public key, for the reviewer account only.
cat >/usr/local/bin/omnissh-accept-any-key <<'EOF'
#!/bin/sh
# Called by sshd as: omnissh-accept-any-key <key-type> <base64-key>
echo "restrict,pty,port-forwarding,permitopen=\"localhost:8888\" $1 $2"
EOF
chown root:root /usr/local/bin/omnissh-accept-any-key
chmod 755 /usr/local/bin/omnissh-accept-any-key

cat >/etc/ssh/sshd_config.d/50-omnissh-review.conf <<'EOF'
PasswordAuthentication no
KbdInteractiveAuthentication no

Match User reviewer
    AuthorizedKeysCommand /usr/local/bin/omnissh-accept-any-key %t %k
    AuthorizedKeysCommandUser nobody
    AllowTcpForwarding local
    PermitOpen localhost:8888
    X11Forwarding no
    AllowAgentForwarding no
    PermitTunnel no
EOF
sshd -t && systemctl restart ssh

# Keep the reviewer account small and contained.
cat >/etc/security/limits.d/reviewer.conf <<'EOF'
reviewer hard nproc 200
reviewer hard nofile 1024
reviewer hard fsize 512000
EOF

# A little sample content, plus a web page for trying Web Ports.
sudo -u reviewer bash -c 'cd ~ && git clone --depth 1 https://github.com/apple/swift-nio-ssh.git sample-project && echo "Welcome to the OmniSSH demo server." > README.txt'
cat >/etc/systemd/system/omnissh-demo-web.service <<'EOF'
[Unit]
Description=OmniSSH demo web page for Web Ports
[Service]
User=reviewer
WorkingDirectory=/home/reviewer
ExecStart=/usr/bin/python3 -m http.server 8888 --bind 127.0.0.1
Restart=always
[Install]
WantedBy=multi-user.target
EOF
systemctl enable --now omnissh-demo-web

# Firewall: SSH only.
ufw allow OpenSSH && ufw --force enable
```

Notes on the setup:

- `restrict,pty,port-forwarding,permitopen="localhost:8888"` turns off agent and X11 forwarding for those keys but still allows a terminal.
- Local forwarding is allowed only to the demo web page on port 8888, so reviewers can try Web Ports and nothing else.

## 3. Check it from your Mac

Any new key should work for `reviewer`, and should not work for `root`:

```bash
ssh-keygen -t ed25519 -f /tmp/reviewtest -N "" -q && ssh -i /tmp/reviewtest -o IdentitiesOnly=yes reviewer@<server-ip> 'echo works; tmux -V'
```

Then try OmniSSH on your iPhone: add the machine with user `reviewer`, and confirm that tabs, swipe-to-scroll and Web Ports (port 8888) all work.

## 4. Paste into App Store Connect → App Review Information → Notes

> OmniSSH is an SSH terminal; no account or sign-in is needed.
>
> To try it with our demo server:
> 1. Tap the key icon (top left) and tap Create Key. Our demo server accepts any key for the "reviewer" account, so there is nothing to copy.
> 2. Go back, tap +, and add a machine: Host `<server-ip or demo host name>`, Port 22, User `reviewer`, with the key you just created.
> 3. Tap the machine, then tap Trust when asked to check the server's fingerprint. A terminal opens inside tmux; try the + button for new tabs, swipe to scroll, and the ⋯ menu for Find, Snippets and split panes.
> 4. Web Ports: tap the globe icon and open port 8888 to see a demo web page served from the server over SSH.
>
> Screen control connects to a Mac's built-in Screen Sharing and needs a Mac on the same network; it is optional and can be skipped.
> OmniSSH Pro subscriptions can be tested with a Sandbox account; the plans screen appears on first launch.

## After approval

You can keep the server for future reviews, or delete it and recreate it from this page next time.
To shut the review door without deleting the server, remove `/etc/ssh/sshd_config.d/50-omnissh-review.conf` and run `systemctl restart ssh`.
