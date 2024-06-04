
# Containers: A Historical and Practical Overview

This blog series and corresponding talks aim to provide a practical view of containers from a historical perspective, exploring modern cloud architectures layer by layer. Starting at the Linux Kernel level and culminating in the development of secure cloud-native applications, this journey will help you understand the intricate workings of containers and their integration into orchestration software like Kubernetes.

## Part I: Kernel Space

The first part focuses on Linux kernel-related topics to build a solid foundation for understanding containers. We will delve into the history of UNIX and Linux, discussing concepts like chroot, namespaces, and cgroups, and explore these through hands-on examples.

### What Are Containers?

Containers are often misunderstood as lightweight virtual machines (VMs). However, they are isolated groups of processes running on a single host, providing specific features that have evolved from various historical solutions within the Linux kernel.

### Key Requirements of Containers

1. Single Host: Containers must run on a single host.
2. Process Groups: Containers are groups of processes with a root process.
3. Isolation: Containers need to be isolated, which we'll define in detail.
4. Common Features: Containers must fulfill common features, which we will explore.

### Chroot: The Early Days

The concept of changing the root directory of a process (chroot) originated in UNIX Version 7 (1979) and later in BSD. In Linux, the chroot command changes the root directory for a running process, creating an isolated environment or "jail." However, chroot has limitations, including the ability to escape the jail.

### Creating a Simple Chroot Environment

Here's a basic example of creating a chroot environment:

```bash
mkdir -p new-root/{bin,lib64}
cp /bin/bash new-root/bin
cp /lib64/{ld-linux-x86-64.so*,libc.so*,libdl.so.2,libreadline.so*,libtinfo.so*} new-root/lib64
sudo chroot new-root








**Containers: A Historical and Practical Overview**

This  blog  series  and  corresponding  talks  aim  to  provide  a  practical  view  of  containers  from  a historical perspective, exploring modern cloud architectures layer by layer. Starting at the Linux Kernel level and culminating in the development of secure cloud-native applications, this journey will help you understand the intricate workings of containers and their integration into orchestration software like Kubernetes.

**Part I: Kernel Space**

The first part focuses on Linux kernel-related topics to build a solid foundation for understanding containers.  We  will  delve  into  the  history  of  UNIX  and  Linux,  discussing  concepts  like  chroot, namespaces, and cgroups, and explore these through hands-on examples.

**What Are Containers?**

Containers  are  often  misunderstood  as  lightweight  virtual  machines  (VMs).  However,  they  are isolated groups of processes running on a single host, providing specific features that have evolved from various historical solutions within the Linux kernel.

**Key Requirements of Containers**

1. Single Host: Containers must run on a single host.
1. Process Groups: Containers are groups of processes with a root process.
1. Isolation: Containers need to be isolated, which we'll define in detail.
1. Common Features: Containers must fulfill common features, which we will explore.

**Chroot: The Early Days**

The concept of changing the root directory of a process (chroot) originated in UNIX Version 7 (1979) and later in BSD. In Linux, the  chroot  command changes the root directory for a running process, creating an isolated environment or "jail." However, chroot has limitations, including the ability to escape the jail.

**Creating a Simple Chroot Environment**

Here's a basic example of creating a chroot environment:

mkdir -p new-root/{bin,lib64}

cp /bin/bash new-root/bin

cp /lib64/{ld-linux-x86-64.so\*,libc.so\*,libdl.so.2,libreadline.so\*,libtinfo.so\*} new-root/lib64 sudo chroot new-root

This setup is minimal and only provides a bash shell.

**Limitations of Chroot**

- Escape Potential: Privileged users can escape the jail.
- No Isolation: Processes and network devices are not isolated.

**Modern Alternatives: pivot\_root and Namespaces**

Modern container runtimes use  pivot\_root  instead of  chroot , providing better isolation by placing old mounts in a separate directory. Linux namespaces further enhance isolation by segregating process trees, network interfaces, and more.

**Practical Example with OCI Container**

Using tools like  skopeo  and  umoci , you can download and unpack an Open Container Initiative (OCI) container to create a more functional chroot environment:

skopeo copy docker://opensuse/tumbleweed:latest oci:tumbleweed:latest sudo umoci unpack --image tumbleweed:latest bundle

sudo chroot bundle/rootfs

Despite appearing functional, this setup lacks process and network isolation, demonstrating the need for namespaces.

**Conclusion**

This first part has introduced the basic concepts of containers, starting with historical solutions like chroot and progressing towards modern isolation techniques. The next parts will delve deeper into namespaces, cgroups, and other essential features that make up today's container ecosystems.

**Problems Encountered and Solutions** ### Problem 1: Files Not Found

Error:

cp: cannot stat '/lib64/libc.so\*': No such file or directory

Solution:

Search for and copy the files from their correct locations: find / -name 'libc.so\*'

find / -name 'libdl.so.2'

find / -name 'libreadline.so\*'

find / -name 'libtinfo.so\*'

Then, copy the found files:

mkdir -p new-root/{bin,lib64}

cp /bin/bash new-root/bin

cp /usr/lib/x86\_64-linux-gnu/ld-linux-x86-64.so.2 new-root/lib64 cp /usr/lib/x86\_64-linux-gnu/libc.so.6 new-root/lib64

cp /usr/lib/x86\_64-linux-gnu/libdl.so.2 new-root/lib64

cp /usr/lib/x86\_64-linux-gnu/libreadline.so.8 new-root/lib64

cp /usr/lib/x86\_64-linux-gnu/libtinfo.so.6 new-root/lib64

\### Problem 2: Command  sudo  Not Found

Error:

bash: sudo: command not found

Solution:

Run the commands directly as root:

umoci unpack --image tumbleweed:latest bundle

\### Problem 3:  chroot  Command Failed

Error:

chroot: failed to run command '/bin/sh': No such file or directory

Solution:

Create a symbolic link from  sh  to  bash : ln -s bash new-root/bin/sh

Then, set the  LD\_LIBRARY\_PATH  variable: LD\_LIBRARY\_PATH=/lib64 chroot new-root

\### Problem 4: Dependencies Not Found

Error:

/bin/sh: error while loading shared libraries: libtinfo.so.6: cannot open shared object file: No such file or directory

Solution:

Copy the necessary dependencies:

ldd /bin/ls

cp /lib/x86\_64-linux-gnu/libselinux.so.1 new-root/lib64 cp /lib/x86\_64-linux-gnu/libc.so.6 new-root/lib64

cp /lib/x86\_64-linux-gnu/libpcre2-8.so.0 new-root/lib64 cp /lib64/ld-linux-x86-64.so.2 new-root/lib64

\### Problem 5: TLS Certificate Error

Error:

tls: failed to verify certificate: x509: certificate signed by unknown authority

Solution:

Update the CA certificates package: apt-get update

apt-get install -y ca-certificates update-ca-certificates

Then, retry the operation with  skopeo :

skopeo copy docker://opensuse/tumbleweed:latest oci:tumbleweed:latest
