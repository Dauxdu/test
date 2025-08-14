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
  -device virtio-net,netdev=n1 \
  -netdev user,id=n1,dns=10.0.8.4,hostfwd=tcp::2222-:22 \
  -drive if=pflash,format=raw,readonly=on,file=$PREFIX/share/qemu/edk2-x86_64-code.fd \
  -drive file=alpine.qcow2,if=virtio \
  -cdrom alpine.iso \
  -nographic
```

> you can get number of useable cpus using `nproc` and total memory using `free -m | grep -oP '\d+' | head -n 1`

# resolv.conf <YOUT_LOCAL_GATEWAY>
```Bash
echo "nameserver 10.0.8.1" > /etc/resolv.conf
echo "nameserver 1.1.1.1" >> /etc/resolv.conf
```

# apk/repositories
```Bash
echo "https://dl-cdn.alpinelinux.org/alpine/v3.22/main" > /etc/apk/repositories
echo "https://dl-cdn.alpinelinux.org/alpine/v3.22/community" >> /etc/apk/repositories
```

```Bash
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
echo "qemu-system-x86_64 \
  -machine q35 \
  -cpu qemu64 \
  -smp 2 \
  -m 1G \
  -device virtio-net,netdev=n1 \
  -netdev user,id=n1,hostfwd=tcp::2222-:22 \
  -drive if=pflash,format=raw,readonly=on,file=$PREFIX/share/qemu/edk2-x86_64-code.fd \
  -drive file=alpine.qcow2,if=virtio \
  -nographic" >> ~/alpine/run_qemu.sh
```

```Bash
chmod +x run_qemu.sh
```
```Bash
bash run_qemu.sh
```
