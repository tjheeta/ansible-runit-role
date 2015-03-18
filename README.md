This role installs a statically compiled runit binary that will work on all distributions x86_64 and i386. It also supports compiling the binary locally before uploading it to the target hosts. Currently, the startup of runit is set to use either upstart or /etc/rc.local .

Variables:

```
runit_build: /tmp/tmp_ansible_build/  # if we are building the binary, this is the build dir
runit_use_precompiled_binary: true    # whether to use the included binaries, disables the build
runit_binary_dir: /usr/local/bin/     # where to install the binaries
runit_service_dir: /etc/service/      # the default service directory that is being monitored
```

