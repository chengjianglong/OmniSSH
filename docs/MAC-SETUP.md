# Set up a Mac as a test target

These steps need your admin password or a sign-in, so you run them yourself. Run each command in Terminal.

## 1. Install tools

```bash
brew install tmux mosh
```

```bash
brew install --cask tailscale-app
```

Open **Tailscale** from Applications, approve the VPN/system extension prompt, and sign in. Then install **Tailscale** from the App Store on your iPhone/iPad and sign in with the **same account**.

## 2. Authorize a key

Authorize your existing Mac key, so the Mac can test SSH to itself:

```bash
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
```

Later, add your iPhone's public key (from whichever SSH app you test with, and eventually from our app) to `~/.ssh/authorized_keys` the same way, one key per line.

## 3. Restrict SSH to keys only

```bash
sudo cp ~/Projects/mobile-ssh/setup/050-mobile-ssh.conf /etc/ssh/sshd_config.d/ && sudo sshd -t && echo "config OK"
```

`sshd -t` checks the config. Don't continue if it prints errors.

## 4. Turn on Remote Login

System Settings › General › Sharing › **Remote Login** → On. Click the ⓘ and limit access to your user only.

## 5. Check it works

```bash
ssh -o PasswordAuthentication=no localhost 'echo SSH OK'
```

```bash
tailscale status
```

Note the Mac's MagicDNS name (e.g. `your-mac.tailnet-name.ts.net`) and its `100.x.y.z` address.

## Undo

```bash
sudo rm /etc/ssh/sshd_config.d/050-mobile-ssh.conf
```

Then turn Remote Login off in System Settings.
