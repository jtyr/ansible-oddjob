oddjob
======

Role which helps to install oddjob - D-Bus service which runs odd jobs on
behalf of client applications.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
---

- name: Default usage
  hosts: myhost1
  roles:
    - oddjob

- name: Example of how to install an additional oddjob helper
  hosts: myhost2
  vars:
    # Install oddjob-mkhomedir package
    oddjob_helper_pkgs:
      - oddjob-mkhomedir
  roles:
    - oddjob

- name: Example of how to configure oddjob
  hosts: myhost3
  vars:
    # Change the max UID for the listall interface method of the service section
    oddjob_config_service_interface_method_listall__default:
      - allow:
        - ^min_uid: 0
        - ^max_uid: 500
  roles:
    - oddjob
```


Role variables
--------------

List of variables used by the role:

```
# Package to be installed (you can force a specific version here)
oddjob_pkg: oddjob

# Helper packages to be installed (you can force a specific version here)
oddjob_helper_pkgs: []

# Path to the config file
oddjob_config_file: /etc/oddjobd.conf


# Default listall interface method of the service section
oddjob_config_service_interface_method_listall__default:
  - allow:
    - ^min_uid: 0
    - ^max_uid: 0

# Custom listall interface method of the service section
oddjob_config_service_interface_method_listall__custom: []

oddjob_config_service_interface_method_listall_opts:
  - ^name: listall

# Final listall interface method of the service section
oddjob_config_service_interface_method_listall:
  - mehod: "{{
      oddjob_config_service_interface_method_listall_opts +
      oddjob_config_service_interface_method_listall__default +
      oddjob_config_service_interface_method_listall__custom
    }}"


# Default list interface method of the service section
oddjob_config_service_interface_method_list__default:
  - allow: ''

# Custom list interface method of the service section
oddjob_config_service_interface_method_list__custom: []

oddjob_config_service_interface_method_list_opts:
  - '^name': list

# Final list interface method of the service section
oddjob_config_service_interface_method_list:
  - method: "{{
      oddjob_config_service_interface_method_list_opts +
      oddjob_config_service_interface_method_list__default +
      oddjob_config_service_interface_method_list__custom
    }}"


# Default quit interface method of the service section
oddjob_config_service_interface_method_quit__default:
  - allow:
    - ^user: root

# Custom quit interface method of the service section
oddjob_config_service_interface_method_quit__custom: []

oddjob_config_service_interface_method_quit_opts:
  - ^name: quit

# Final quit interface method of the service section
oddjob_config_service_interface_method_quit:
  - method: "{{
      oddjob_config_service_interface_method_quit_opts +
      oddjob_config_service_interface_method_quit__default +
      oddjob_config_service_interface_method_quit__custom
    }}"


# Default reload interface method of the service section
oddjob_config_service_interface_method_reload__default:
  - allow:
    - ^user: root

# Custom reload interface method of the service section
oddjob_config_service_interface_method_reload__custom: []

# Options of the reload interface method of the service section
oddjob_config_service_interface_method_reload_opts:
  - ^name: reload

# Final reload interface method of the service section
oddjob_config_service_interface_method_reload:
  - method: "{{
      oddjob_config_service_interface_method_reload_opts +
      oddjob_config_service_interface_method_reload__default +
      oddjob_config_service_interface_method_reload__custom
    }}"


# Options of the service section
oddjob_config_service_opts:
  - ^name: com.redhat.oddjob

# Default service section
oddjob_config_service__default:
  - ^name: com.redhat.oddjob
  - object:
    - ^name: /com/redhat/oddjob
    - interface: "{{
        oddjob_config_service_opts +
        oddjob_config_service_interface_method_listall +
        oddjob_config_service_interface_method_list +
        oddjob_config_service_interface_method_quit +
        oddjob_config_service_interface_method_reload
      }}"

# Custom service section
oddjob_config_service__custom: []

# Inner part of the service section
oddjob_config_service__inner: "{{
  oddjob_config_service__default +
  oddjob_config_service__custom
}}"

# Final service section
oddjob_config_service:
  - service: "{{ oddjob_config_service__inner }}"


# Default list of includes
oddjob_config_include__default:
  - include:
    - ^ignore_missing: "yes"
    - /etc/oddjobd.conf.d/*.conf
  - include:
    - ^ignore_missing: "yes"
    - /etc/oddjobd-local.conf

# Custom list of includes
oddjob_config_include__custom: []

# Final list of includes
oddjob_config_include: "{{
  oddjob_config_include__default +
  oddjob_config_include__custom
}}"


# Default jobd config
oddjob_config__default:
  - oddjobconfig: "{{
      oddjob_config_service +
      oddjob_config_include
    }}"

# Custom jobd config
oddjob_config__custom: []

# Final jobd config
oddjob_config: "{{
  oddjob_config__default +
  oddjob_config__custom
}}"
```


Dependencies
------------

* [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
