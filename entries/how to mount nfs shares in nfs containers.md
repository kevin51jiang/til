---
title: how to mount nfs shares in nfs containers
permalink: how-to-mount-nfs-shares-in-nfs-containers
date: 2025-03-30T00:17:47-07:00
tags:
---

Do this in the main proxmox node shell

```
sudo mount -t nfs4 -o nfsvers=4.2 <your-ip>:/nasnfs /nas
```

- Here, nasnfs is the name of the NFS resource, and /nas is the mount directory.

Then, what you should do is

```
pct set <lxc-container-id> -mp0 /nas,mp=/mnt/nasnfs
pct set <lxc-container-id> -mp1 /nas2,mp=/mnt/nasnfs2
etc..
```

Source:
https://forum.proxmox.com/threads/tutorial-mounting-nfs-share-to-an-unprivileged-lxc.138506/post-726198
