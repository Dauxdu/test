```Bash
pkg update -y && pkg upgrade -y
pkg install wget qemu-system-x86-64-headless qemu-utils -y
```

```Bash
mkdir alpine && cd alpine
```
4. Download Alpine Linux (virt optimized) ISO: https://www.alpinelinux.org/downloads/

```Bash
wget -O alpine.iso <YOUT_VIRTUAL_ISO_URL>
```

```Bash
qemu-img create -f qcow2 alpine.qcow2 15G
```

```Bash
qemu-system-x86_64 \
  -machine q35 \
  -cpu qemu64 \
  -smp 2 \
  -m 1G \
  -device virtio-net,netdev=n1,dns=<YOUT_LOCAL_DNS>,1.1.1.1,8.8.8.8,9.9.9.9 \
  -netdev user,id=n1,hostfwd=tcp::2222-:22 \
  -drive if=pflash,format=raw,readonly=on,file=$PREFIX/share/qemu/edk2-x86_64-code.fd \
  -drive file=alpine.qcow2,if=virtio \
  -cdrom alpine.iso \
  -nographic
```


> you can get number of useable cpus using `nproc` and total memory using `free -m | grep -oP '\d+' | head -n 1`

```Bash
echo -e "nameserver <YOUT_LOCAL_DNS>\nnameserver 1.1.1.1" > /etc/resolv.conf
```

```Bash
   echo 'https://dl-cdn.alpinelinux.org/alpine/v3.22/main' >> /etc/apk/repositories
   echo 'https://dl-cdn.alpinelinux.org/alpine/v3.22/community' >> /etc/apk/repositories
```

```
setup-alpine
```

8. Login with username `root` (no password)
```Bash
apk update
apk add docker
```

```Bash
service docker start
rc-update add docker
service docker status
```

```Bash
poweroff
```

```Bash
echo "qemu-system-x86_64 \\
  -machine q35 \\
  -cpu qemu64 \\
  -smp 4 \\
  -m 2048 \\
  -drive file=alpine.qcow2,if=virtio \\
  -netdev user,id=n1,hostfwd=tcp::2222-:22 \\
  -device virtio-net,netdev=n1 \\
  -nographic" >> ~/alpine/run_qemu.sh
```

```Bash
chmod +x run_qemu.sh
```
```Bash
bash run_qemu.sh
```
