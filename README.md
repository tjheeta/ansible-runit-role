This role installs a statically compiled runit binary that will work on all distributions x86_64 and i386. It also supports compiling the binary locally before uploading it to the target hosts. Currently, the startup of runit is set to use either upstart or /etc/rc.local .

Variables:

```
runit_build: /tmp/tmp_ansible_build/  # if we are building the binary, this is the build dir
runit_use_precompiled_binary: true    # whether to use the included binaries, disables the build
runit_binary_dir: /usr/local/bin/     # where to install the binaries
runit_service_dir: /etc/service/      # the default service directory that is being monitored
```

Also a task to setup runit services in various other roles, similar to Chef LWRP runit_service.
The task that takes runit_scaffold_name, runit_scaffold_owner, runit_scaffold_run_content. It then:

* Creates a log directory in /var/log/$runit_scaffold_name
* Sets up /etc/sv/$runit_scaffold_name/log/run to log to /var/log/$runit_scaffold_name
* Takes the content of runit_scaffold_content and puts it into /etc/sv/$runit_scaffold_name/run
* Links /etc/sv/$runit_scaffold_name to /etc/service

Note that notifies on includes don't work yet and we'll need an extra task to restart
https://groups.google.com/forum/#!topic/ansible-project/Lhr0mqm91TQ

```
- include: ../../runit/tasks/scaffold.yml
  notify: restart_your_service
    vars:    
      runit_scaffold_name: yourservice
      runit_scaffold_owner: yourowner
      runit_scaffold_run_content: |
        #!/bin/sh
        exec 2>&1
        ulimit -n 8192
        exec chpst -u {{ runit_scaffold_owner }} /usr/local/bin/yourservice

# Note that notify on include doesn't work yet
# https://groups.google.com/forum/#!topic/ansible-project/Lhr0mqm91TQ
- name: Restart if changed
  shell: echo "Restarting..."
  notify: restart_your_service
  when: runit_scaffold_changed.changed == true


```
