# 输出日志检查报错

```
ssh -v -i `find . | grep confer-server-test.pem` ubuntu@18.210.213.143

OpenSSH_8.6p1, LibreSSL 3.3.5
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 21: include /etc/ssh/ssh_config.d/* matched no files
debug1: /etc/ssh/ssh_config line 54: Applying options for *
debug1: Authenticator provider $SSH_SK_PROVIDER did not resolve; disabling
debug1: Connecting to 18.210.213.143 [18.210.213.143] port 22.
debug1: Connection established.
debug1: identity file ./keys/confer-server-test.pem type -1
debug1: identity file ./keys/confer-server-test.pem-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_8.6
kex_exchange_identification: Connection closed by remote host
Connection closed by 18.210.213.143 port 22
```
