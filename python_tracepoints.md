## So you want to trace a Python function

* First, get a working setup - you can use the Vagrantfile in this repo

* Check that we have tplist (for the Vagrantfile, it's installed with the
  `*-bpfcc` suffic). We can use this to look at python3's available tracepoints:
```
tplist-bpfcc -l python3
```
This should give a list of tracepoints that looks like this:
```
b'/usr/local/bin/python3' b'python':b'function__entry'
b'/usr/local/bin/python3' b'python':b'function__return'
b'/usr/local/bin/python3' b'python':b'line'
b'/usr/local/bin/python3' b'python':b'import__find__load__start'
b'/usr/local/bin/python3' b'python':b'import__find__load__done'
b'/usr/local/bin/python3' b'python':b'audit'
b'/usr/local/bin/python3' b'python':b'gc__start'
b'/usr/local/bin/python3' b'python':b'gc__done'
```

* Assuming that's working, we can now check available probes for a running process, in this case a running `python3` REPL:
```
tplist -p $(pgrep python)
```

* You can also filter, as well as getting more information about the arguments
  available to each probe. For example, to get only the python related
  tracepoints, as well as extra args:

```
tplist-bpfcc *python* -p $(pgrep python) -v
```

Returning:

```
b'python':b'function__entry' [sema 0x593080]
  1 location(s)
  3 argument(s)
b'python':b'function__return' [sema 0x593082]
  1 location(s)
  3 argument(s)
b'python':b'line' [sema 0x59308c]
  1 location(s)
  3 argument(s)
b'python':b'import__find__load__start' [sema 0x593092]
  1 location(s)
  1 argument(s)
b'python':b'import__find__load__done' [sema 0x593094]
  1 location(s)
  2 argument(s)
b'python':b'audit' [sema 0x593096]
  1 location(s)
  2 argument(s)
b'python':b'gc__start' [sema 0x59308e]
  1 location(s)
  1 argument(s)
b'python':b'gc__done' [sema 0x593090]
  1 location(s)
  1 argument(s)
```

* This will give you the correct probe to attach to, including the correct process name. Remember to prefix with `usdt:`
* In this example, we'll print the module, name and line number of the
  functions we're entering:

Example:
```
tplist -p $(pgrep python3)

sudo bpftrace -e 'usdt:/proc/3499/root/usr/bin/python3.10:python:function__entry { printf("Module: %s Function: %s Line: %u\n", str(arg0), str(arg1), arg2) }'
```

Output:
```
Attaching 1 probe...
Module: /usr/lib/python3.10/rlcompleter.py Function: complete Line: 67
Module: /usr/lib/python3.10/rlcompleter.py Function: complete Line: 67
Module: <stdin> Function: <module> Line: 1
Module: <stdin> Function: <module> Line: 1
Module: <stdin> Function: add Line: 1
```

### Further reading
List of bpftrace builtins: https://man.archlinux.org/man/bpftrace.8.en#BUILTINS
