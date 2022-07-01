# 输出日志检查报错

```
ssh  -vvvvA -i `find . | grep confer-server-test.pem` ec2-user@ec2-18-210-213-143.compute-1.amazonaws.com

OpenSSH_8.6p1, LibreSSL 3.3.5
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 21: include /etc/ssh/ssh_config.d/* matched no files
debug1: /etc/ssh/ssh_config line 54: Applying options for *
debug2: resolve_canonicalize: hostname 18.210.213.143 is address
debug3: expanded UserKnownHostsFile '~/.ssh/known_hosts' -> '/Users/wen/.ssh/known_hosts'
debug3: expanded UserKnownHostsFile '~/.ssh/known_hosts2' -> '/Users/wen/.ssh/known_hosts2'
debug1: Authenticator provider $SSH_SK_PROVIDER did not resolve; disabling
debug3: ssh_connect_direct: entering
debug1: Connecting to 18.210.213.143 [18.210.213.143] port 22.
debug3: set_sock_tos: set socket 3 IP_TOS 0x48
debug1: Connection established.
debug1: identity file ./keys/confer-server-test.pem type -1
debug1: identity file ./keys/confer-server-test.pem-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_8.6
kex_exchange_identification: Connection closed by remote host
Connection closed by 18.210.213.143 port 22
```

# 成功连接的输出

```
ssh -v -i `find . | grep teamlink-conference-key-virginia.pem` ec2-user@54.175.243.206

OpenSSH_8.6p1, LibreSSL 3.3.5
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 21: include /etc/ssh/ssh_config.d/* matched no files
debug1: /etc/ssh/ssh_config line 54: Applying options for *
debug1: Authenticator provider $SSH_SK_PROVIDER did not resolve; disabling
debug1: Connecting to 54.175.243.206 [54.175.243.206] port 22.
debug1: Connection established.
debug1: identity file ./keys/teamlink-conference-key-virginia.pem type -1
debug1: identity file ./keys/teamlink-conference-key-virginia.pem-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_8.6
debug1: Remote protocol version 2.0, remote software version OpenSSH_7.4
debug1: compat_banner: match: OpenSSH_7.4 pat OpenSSH_7.0*,OpenSSH_7.1*,OpenSSH_7.2*,OpenSSH_7.3*,OpenSSH_7.4*,OpenSSH_7.5*,OpenSSH_7.6*,OpenSSH_7.7* compat 0x04000002
debug1: Authenticating to 54.175.243.206:22 as 'ec2-user'
debug1: load_hostkeys: fopen /Users/wen/.ssh/known_hosts: No such file or directory
debug1: load_hostkeys: fopen /Users/wen/.ssh/known_hosts2: No such file or directory
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts: No such file or directory
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts2: No such file or directory
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: algorithm: curve25519-sha256
debug1: kex: host key algorithm: ssh-ed25519
debug1: kex: server->client cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: kex: client->server cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: expecting SSH2_MSG_KEX_ECDH_REPLY
debug1: SSH2_MSG_KEX_ECDH_REPLY received
debug1: Server host key: ssh-ed25519 SHA256:zZ2PAynXu2/Yk/kLOZxOsTUzz5t+qYWul83HIdSqaNo
debug1: load_hostkeys: fopen /Users/wen/.ssh/known_hosts: No such file or directory
debug1: load_hostkeys: fopen /Users/wen/.ssh/known_hosts2: No such file or directory
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts: No such file or directory
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts2: No such file or directory
debug1: hostkeys_find_by_key_hostfile: hostkeys file /Users/wen/.ssh/known_hosts does not exist
debug1: hostkeys_find_by_key_hostfile: hostkeys file /Users/wen/.ssh/known_hosts2 does not exist
debug1: hostkeys_find_by_key_hostfile: hostkeys file /etc/ssh/ssh_known_hosts does not exist
debug1: hostkeys_find_by_key_hostfile: hostkeys file /etc/ssh/ssh_known_hosts2 does not exist
The authenticity of host '54.175.243.206 (54.175.243.206)' can't be established.
ED25519 key fingerprint is SHA256:zZ2PAynXu2/Yk/kLOZxOsTUzz5t+qYWul83HIdSqaNo.
```
