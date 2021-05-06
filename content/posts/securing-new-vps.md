---
title: Setting up and securing a brand spankin' new vps with vultr
date: 2021-04-28
---

# Setting up and securing a brand spankin' new vps with vultr

I'll try to keep this brief but here's how I went from a fresh ubuntu 20.04 [VPS on vultr](https://www.vultr.com/?ref=7614094) to a secured VPS on vultr

## Spin up a new VPS

First things first, install the vultr CLI and spin up that new VPS

```sh
brew tap vultr/vultr-cli
brew install vultr-cli
export VULTR_API_KEY=your_api_key
vultr-cli server create --region <region-id> --plan <plan-id> --os <os-id> --hostname <hostname>
```

## Secure that VPS, pronto

I read somewhere that you have ~10 minutes to secure a vanilla ubuntu VPS before it gets compromised by some script kiddie or a crypto miner or something. So there's no time to waste here, don't just spin up your VPS and walk away, you have to do this as soon as possible.

First, ssh into the thing as root, since you don't have another user yet

```sh
ssh root@<server host ip>
```

Next, run [this script from digitalocean](https://github.com/do-community/automated-setups/blob/master/Ubuntu-18.04/initial_server_setup.sh)

Be sure to change `USERNAME=sammy` to `USERNAME=admin` or whatever you want

Now that that's done, it's time to [double down](https://en.wikipedia.org/wiki/Double_Down_(sandwich)) on [security with tailscale](https://tailscale.com/kb/1077/secure-server-ubuntu-18-04/).

I've copied over the steps here in case you're too lazy to click that link:

```sh
ssh admin@<server host ip>

curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/bionic.gpg | sudo apt-key add -
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/bionic.list | sudo tee /etc/apt/sources.list.d/tailscale.list

sudo apt-get update
sudo apt-get install tailscale

sudo tailscale up

ip addr show tailscale0

ssh admin@<copied 100.x.y.z address>

# Restrict ssh to tailscale ip only
sudo ufw allow in on tailscale0 to any port 22
sudo ufw allow 41641/udp

sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw delete 22/tcp
sudo ufw reload
sudo service ssh restart
```

Now you can test that tailscale is working by trying to ssh into your VPSs IP:

```sh
ssh admin@<server host ip>
ssh: connect to host <server host ip> port 22: Operation timed out
```

That should fail with a timeout, now try tailscale's ip (don't forget to connect to tailscale first)

```sh
ssh admin@<copied 100.x.y.z address>
```

That's that! Your VPS is now as secure as it will ever be. Don't worry about udp on 41641, that's totally fine haha
