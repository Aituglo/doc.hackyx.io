---
description: Some tips using SSH
---

# SSH Tips

### Create a tunnel

```bash
ssh -L 127.0.0.1:3389:10.10.10.14:3389 root@server
```

### Create a Socks Proxy

```bash
ssh -D 1337 -q -C -N -f root@server
```
