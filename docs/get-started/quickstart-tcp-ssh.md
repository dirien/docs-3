 # Quick-start: Tunnel a private SSH server over inlets PRO

In this tutorial we will use inlets-pro to access your computer behind NAT or a firewall. We'll do this by tunnelling SSH over inlets-pro, and clients will connect to your exit-server.

Scenario: You want to allow SSH access to a computer that doesn't have a public IP, is inside a private network or behind a firewall. A common scenario is connecting to a Raspberry Pi on a home network or a home-lab.

> You will need an inlets PRO license, [start a 14-day free trial](https://inlets.dev/).

## Setup your exit node with `inletsctl`

For this tutorial you will need to have an account and API key with one of the [supported providers](https://github.com/inlets/inletsctl#featuresbacklog), or you can create an exit-server manually and install inlets PRO there yourself.

For this tutorial, the [DigitalOcean provider will be used](https://m.do.co/c/8d4e75e9886f). You can get [free credits on DigitalOcean with this link](https://m.do.co/c/8d4e75e9886f).

Create an API key in the DigitalOcean dashboard with Read and Write permissions, and download it to a file called `do-access-token` in your home directory.

You need to know the IP of the machine you to connect to on your local network, for instance `192.168.0.35` or `127.0.0.1` if you are running inlets PRO on the same host as SSH.

You can use the `inletsctl` utility to provision exit-servers with inlets PRO preinstalled, it can also download the `inlets-pro` CLI.

```bash
curl -sLSf https://inletsctl.inlets.dev | sh
sudo mv inletsctl /usr/local/bin/
sudo inletsctl download
```

# Create an exit node

## A) Automate your exit node

```bash
inletsctl create \
  --access-token-file ~/do-access-token \
  --region lon1 \
  --pro
```

> Pick a region close to your network, you can see other options with `--help`

After the machine has been created, `inletsctl` will output a sample command for the `inlets-pro client` command:

```bash 
inlets-pro client --url "wss://206.189.114.179:8123/connect" \
    --token "4NXIRZeqsiYdbZPuFeVYLLlYTpzY7ilqSdqhA0HjDld1QjG8wgfKk04JwX4i6c6F" \
```

Don't run this command, but note down the `--url` and `--token` parameters for later

## B) Manual setup of your exit node

Use B) if you want to provision your virtual machine manually, or if you already have a host from another provider.

Log in to your remote exit node with `ssh` and obtain the binary using `inletsctl`:

```bash
curl -sLSf https://inletsctl.inlets.dev | sh
sudo mv inletsctl /usr/local/bin/
sudo inletsctl download
```

Find your public IP:

```bash
export IP=$(curl -s ifconfig.co)
```

Confirm the IP with `echo $IP` and save it, you need it for the client

Get an auth token and save it for later to use with the client

```bash
export TOKEN="$(head -c 16 /dev/urandom |shasum|cut -d'-' -f1)"

echo $TOKEN
```

Start the server:

```bash
sudo inlets-pro server \
  --auto-tls \
  --common-name $IP \
  --token $TOKEN
```

If running the inlets client on the same host as SSH, you can simply set `PROXY_TO_HERE` to `localhost`. Or if you are running SSH on a different computer to the inlets client, then you can specify a DNS entry or an IP address like `192.168.0.15`.

If using this manual approach to install inlets PRO, you should create a systemd unit file.

### Configure the internal SSH server's listening port

It's very likely (almost certain) that your exit server will already be listening for traffic on the standard ssh port `22`. Therefore you will need to configure your internal server to use an additional TCP port such as `2222`.

Once configured, you'll still be able to connect to the internal server on port 22, but to connect via the tunnel, you'll use port `2222`

Add the following to  `/etc/ssh/sshd_config`:

```bash
Port 22
Port 2222
```

For (optional) additional security, you could also disable password authentication, but make sure that you have inserted your SSH key to the internal server with `ssh-copy-id user@ip` before reloading the SSH service.

```bash
PasswordAuthentication no
```

Now need to reload the service so these changes take effect

```bash
sudo systemctl daemon-reload
sudo systemctl restart sshd
```

Check that you can still connect on the internal IP on port 22, and the new port 2222.

Use the `-p` flag to specify the SSH port:

```bash
export IP="192.168.0.35"

ssh -p 22 $IP "uptime"
ssh -p 2222 $IP "uptime"
```

### Start the inlets PRO client

First download the inlets-pro client onto the machine running ssh:

```bash
sudo inletsctl download
```

Use the command from earlier to start the client on the server:

```bash 
export IP="206.189.114.179"
export TCP_PORTS="2222"
export LICENSE_FILE="$HOME/LICENSE.txt"
export UPSTREAM="localhost"

inlets-pro client --url "wss://$IP:8123/connect" \
  --token "4NXIRZeqsiYdbZPuFeVYLLlYTpzY7ilqSdqhA0HjDld1QjG8wgfKk04JwX4i6c6F" \
  --license-file "$LICENSE_FILE" \
  --upstream "$UPSTREAM" \
  --ports $TCP_PORTS
```

You can alter UPSTREAM to point to another machine accessible from where you are running the inlets-pro client such as 192.168.0.10.

### Try it out

Verify the installation by trying to SSH to the public IP, using port `2222`.

```bash 
ssh -p 2222 user@206.189.114.179
```

You should now have access to your server via SSH over the internet with the IP of the exit server.

You can also use other compatible tools like `sftp`, `scp` and `rsync`, just make sure that you set the appropriate port flag. The port flag for sftp is `-P` rather than `-p`.

## Wrapping up

The principles in this tutorial can be adapted for other protocols that run over TCP such as MongoDB or PostgreSQL, just adapt the port number as required.

* [Quick-start: Tunnel a private database over inlets PRO](https://docs.inlets.dev/#/get-started/quickstart-tcp-database)
* [Purchase inlets PRO for personal or commercial use](https://inlets.dev/)
