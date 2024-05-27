SSH - OPENSSH (THANKS, PUFFY!)
==============================
(*) Connect to a server using SSH: `ssh user@host`
(*) Generate a SSH key: `ssh-keygen`
(*) Generate SSH public key from the private key: `ssh-keygen -y -f privatekey.pem > publickey.pem`
(*) Copy `~/.ssh/id_rsa.pub` to remote-server.org: `$ ssh-copy-id -i ~/.ssh/is_rsa.pub username@remote-server.org`
(*) (Powershell) Copy SSH id without ssh-copy-id: `Get-Contents ~/.ssh/id_rsa.pub | ssh user@123.45.67.89 "cat >> ~/.ssh/authorized_keys"`
(*) Sometimes SSH still won't use your key - make sure you've added it: `eval "$(ssh-agent -s) && ssh-add ~/.ssh/NAMEOFYOURKEY`
(*) SSH connection through host in the middle: `ssh -J user@reachable_host user@unreacheable_host`
(*) Specify the ID and port that SSH should use: `$ ssh-copy-id -i ~/.ssh/id_ed25519.pub -p 221 username@remote-server.org`
(*) Copy a file to a remote server: `scp path/to/local_file user@host:/path/remote_file`
(*) Make an encrypted archive of local dir/ on remote machine using ssh: `tar -c dir/ | gzip | gpg -c | ssh user@remote 'dd of=dir.tar.gz.gpg'`
(*) Use a specific key to copy a file: `scp -i ~/.ssh/private_key local_file remote_host:/path/remote_file`
(*) Infinite loop ssh: `while true; do ssh login@10.0.0.1; if [[ $? -ne 0 ]]; then echo "Nope, keep trying!"; fi; sleep 10; done`
(*) Port forwarding: `ssh -L8888:localhost:80 -i nov15a.pem ubuntu@123.21.167.60`
(*) Append a pub key from pem file and save in remote server accessing with another key: `ssh-keygen -y -f user-key.pem | ssh user@host -i already_on_remote_server_key.pem 'cat >> ~/.ssh/authorized_keys'`
(*) Backup VPS disk to another host: `ssh root@vps.example -p22 "cat /dev/sda1 | gzip -1 - " > vps.sda1.img.gz`
(*) Test your bash skills: `ssh bandit0@bandit.labs.overthewire.org -p 2220`


##### Tool: [ssh](https://www.openssh.com/)

###### Escape Sequence

```
# Supported escape sequences:
~.  - terminate connection (and any multiplexed sessions)
~B  - send a BREAK to the remote system
~C  - open a command line
~R  - Request rekey (SSH protocol 2 only)
~^Z - suspend ssh
~#  - list forwarded connections
~&  - background ssh (when waiting for connections to terminate)
~?  - this message
~~  - send the escape character by typing it twice
```

###### Compare a remote file with a local file

```bash
ssh user@host cat /path/to/remotefile | diff /path/to/localfile -
```

###### SSH connection through host in the middle

```bash
ssh -t reachable_host ssh unreachable_host
```

###### Run command over SSH on remote host

```bash
cat > cmd.txt << __EOF__
cat /etc/hosts
__EOF__

ssh host -l user $(<cmd.txt)
```

###### Get public key from private key

```bash
ssh-keygen -y -f ~/.ssh/id_rsa
```

###### Get all fingerprints

```bash
ssh-keygen -l -f .ssh/known_hosts
```

###### SSH authentication with user password

```bash
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no user@remote_host
```

###### SSH authentication with publickey

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i id_rsa user@remote_host
```

###### Simple recording SSH session

```bash
function _ssh_sesslog() {

  _sesdir="<path/to/session/logs>"

  mkdir -p "${_sesdir}" && \
  ssh $@ 2>&1 | tee -a "${_sesdir}/$(date +%Y%m%d).log"

}

# Alias:
alias ssh='_ssh_sesslog'
```

###### Using Keychain for SSH logins

```bash
### Delete all of ssh-agent's keys.
function _scl() {

  /usr/bin/keychain --clear

}

### Add key to keychain.
function _scg() {

  /usr/bin/keychain /path/to/private-key
  source "$HOME/.keychain/$HOSTNAME-sh"

}
```

###### SSH login without processing any login scripts

```bash
ssh -tt user@host bash
```

###### SSH local port forwarding

Example 1:

```bash
# Forwarding our local 2250 port to nmap.org:443 from localhost through localhost
host1> ssh -L 2250:nmap.org:443 localhost

# Connect to the service:
host1> curl -Iks --location -X GET https://localhost:2250
```

Example 2:

```bash
# Forwarding our local 9051 port to db.d.x:5432 from localhost through node.d.y
host1> ssh -nNT -L 9051:db.d.x:5432 node.d.y

# Connect to the service:
host1> psql -U db_user -d db_dev -p 9051 -h localhost
```

  * `-n` - redirects stdin from `/dev/null`
  * `-N` - do not execute a remote command
  * `-T` - disable pseudo-terminal allocation

###### SSH remote port forwarding

```bash
# Forwarding our local 9051 port to db.d.x:5432 from host2 through node.d.y
host1> ssh -nNT -R 9051:db.d.x:5432 node.d.y

# Connect to the service:
host2> psql -U postgres -d postgres -p 8000 -h localhost
```

___