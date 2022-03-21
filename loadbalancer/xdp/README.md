# XDP eBPF

```sh
# Webserver
docker run -itd --name=http1 --hostname=http1 feisky/webserver
docker run -itd --name=http2 --hostname=http2 feisky/webserver

# Client
docker run -itd --name=client alpine

# LB (XDP would be setup in xdp-proxy)
docker run -itd --name=lb --privileged -v /sys/kernel/debug:/sys/kernel/debug alpine
```



clang -g -O2 -target bpf -D__TARGET_ARCH_x86 -I/usr/include/x86_64-linux-gnu -I. -c xdp-proxy.bpf.c -o xdp-proxy.bpf.o
bpftool gen skeleton xdp-proxy.bpf.o > xdp-proxy.skel.h

clang -g -O2 -target bpf -D__TARGET_ARCH_x86 -I/usr/include/x86_64-linux-gnu -I. -c xdp-proxy-v2.bpf.c -o xdp-proxy-v2.bpf.o
bpftool gen skeleton xdp-proxy-v2.bpf.o > xdp-proxy-v2.skel.h

----------------------- user prog ----------------------
clang -g -O2 -Wall -I. -c xdp-proxy.c -o xdp-proxy.o
clang -Wall -O2 -g xdp-proxy.o -static -lbpf -lelf -lz -o xdp-proxy


clang -g -O2 -Wall -I. -c xdp-proxy-v2.c -o xdp-proxy-v2.o
clang -Wall -O2 -g xdp-proxy-v2.o -static -lbpf -lelf -lz -o xdp-proxy-v2

----------------------- 

# For Ubuntu20.10+
sudo apt-get install -y  make clang llvm libelf-dev libbpf-dev bpfcc-tools libbpfcc-dev linux-tools-$(uname -r) linux-headers-$(uname -r)

# For RHEL8.2+
sudo yum install libbpf-devel make clang llvm elfutils-libelf-devel bpftool bcc-tools bcc-devel


git config --global user.email "barry.wang0520@gmail.com"
git config --global user.name "guu13"

test