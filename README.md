# ebpf-playground
Learning more about eBPF @ Recurse

## Setup
* ssh into your Heap node of choice
* clone this repo:
```
git clone https://github.com/spike01/ebpf-playground.git
cd ebpf-playground
```
* start up Vagrant
```
vagrant up
vagrant ssh
```
* while in the VM - check that everything is working:
```
sudo opensnoop-bpfcc
sudo bpftrace -e 'BEGIN { printf("hello world\n"); }'

# Bonus commands, or try anything from
# https://github.com/iovisor/bpftrace/blob/master/docs/tutorial_one_liners.md
sudo bpftrace -e 'tracepoint:syscalls:sys_enter_openat { printf("%s %s\n", comm, str(args->filename)); }'
```
* when you're done
```
vagrant down
# or
vagrant destroy
```
