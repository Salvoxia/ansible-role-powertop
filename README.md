# Ansible Role: Powertop
[![Build Status](https://img.shields.io/github/actions/workflow/status/salvoxia/ansible-role-powertop/ci.yml?label=molecule&logo=ansible&style=flat-square)](https://github.com/Salvoxia/ansible-role-powertop/actions/workflows/ci.yml)
[![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/salvoxia/ansible-role-powertop?color=EE0000&label=release&logo=ansible&style=flat-square)](https://galaxy.ansible.com/ui/standalone/roles/salvoxia/powertop/)
[![Ansible Galaxy Downloads](https://img.shields.io/badge/dynamic/json?color=blueviolet&label=Galaxy%20Downloads&logo=ansible&style=flat-square&query=%24.download_count&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2F39742%2F%3Fformat%3Djson)](https://galaxy.ansible.com/ui/standalone/roles/salvoxia/powertop/)
[![MIT LIcense](https://img.shields.io/github/license/salvoxia/ansible-role-powertop?style=flat-square)](https://github.com/Salvoxia/ansible-role-powertop/blob/main/LICENSE)

Installs [Powertop](https://github.com/fenrus75/powertop) on Debian based systems, from source if the package manager's version does not support your CPU properly.
Also installs a one-shot type service to automatically optimize power consumption at system startup.

__Key Features:__
  - Set `powertop_state` to either install or remove Powertop
  - Install Powertop from a specific source tag instead of package manager in case the package manager provided version is too old
  - Switch between specific versions installed from source
  - Switch between installation from package manager and source (or vice versa)

__Dependencies:__
None

__Compatibility:__  
This role is tested with
  - Debian 11 Bullseye
  - Debian 12 Bookworm
  - Ubuntu 22.04 LTS Jammy Jellyfish
  - Ubuntu 24.04 LTS Noble Numbat

By default, the role will check for a compatible operating system and version (any of the above). To disable that check, set the role variable `powertop_verify_os` to `false`.  

## Installing from Source

By default the role will install Powertop using the package manager. If the system's package manager comes with an older Powertop package, it is possible to install Powertop from source. The role will automatically install all build dependencies, check out the desired source version, compile and install Powertop from source.  
It is also possible to use this role for updating Powertop installed from source to a newer version (or downgrade to an older version). The old version is uninstalled before the new version is installed.
The role supports switching between Powertop installed by package manager and installed from source (and vice versa).

The following variables control installation from source:
<table>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td> 
      
`powertop_install_from_source`
    </td>
    <td>
Flag indicating whether to install Powertop from source or not.<br>All the variables below have no effect if not set to `true`.<br>Default: `false`
    </td>
  </tr>
  <tr>
    <td> 
      
`powertop_source_repository`
    </td>
    <td>
The URL to the Git Repository to compile Powertop from.<br>Default: `https://github.com/fenrus75/powertop.git`
    </td>
  </tr>
  <tr>
    <td> 
      
`powertop_source_tag`
    </td>
    <td>
The Git Tag to check out before compiling Powertop.<br>Can be a branch name as well.<br>Default: `v2.15`
    </td>
  </tr>
  <tr>
    <td> 
      
`powertop_libtraceevent_install_from_source`
    </td>
    <td>
Flag indicating whether to install `libtraceevent` from source or not. This is a dependency for compiling Powertop, which might be too old when installed from package manager.<br>If set to `false`, it is the user's responsibility to make sure this dependency is met.<br>Default: `true`
    </td>
  </tr>
  <tr>
    <td> 
      
`powertop_libtraceevent_source_repository`
    </td>
    <td>
The URL to the Git Repository to compile `libtraceevent` from. <br>Default: `https://git.kernel.org/pub/scm/libs/libtrace/libtraceevent.git`
    </td>
  </tr>
  <tr>
    <td> 
      
`powertop_libtraceevent_source_tag`
    </td>
    <td>
The Git Tag to check out before compiling `libtraceevent`.<br>Can be a branch name as well.<br>Default: `libtraceevent-1.8.4`
    </td>
  </tr>
  <tr>
    <td> 
      
`powertop_libtracefs_install_from_source`
    </td>
    <td>
Flag indicating whether to install `libtracefs` from source or not. This is a dependency for compiling Powertop, which might be too old when installed from package manager.<br>If set to `false`, it is the user's responsibility to make sure this dependency is met.<br>Default: `true`
    </td>
  </tr>
  <tr>
    <td> 
      
`powertop_libtracefs_source_repository`
    </td>
    <td>
The URL to the Git Repository to compile `libtracefs` from.<br>Default: `https://git.kernel.org/pub/scm/libs/libtrace/libtracefs.git`
    </td>
  </tr>
  <tr>
    <td> 
      
`powertop_libtracefs_source_tag`
    </td>
    <td>
The Git Tag to check out before compiling `libtracefs`.<br>Can be a branch name as well.<br>Default: `libtracefs-1.8.1`
    </td>
  </tr>
</table>

## Role Variables

Available variables are listed below, along with default values (see [`defaults/main.yml`](defaults/main.yml)).

### `powertop_state`
```yaml
powertop_state: present
```

Determines whether Powertop should be installed or not installed on the target system.
Allowed values in this role are `present`  and `absent`.

### `powertop_verify_os`
```yaml
powertop_verify_os: true
```

Controls whether the role verifies if the target system is running a supported operating system. 

### `powertop_manage_service`

```yaml
powertop_manage_service: true
```
Controls whether a one-shot type systemd service is installed to run `powertop --auto-tune` at system startup.

### `powertop_service_name`

```yaml
powertop_service_name: "powertop"
```

The name of the Powertop systemd service. Only has any effect if `powertop_manage_servce` is set to `true`.

## Example Playbook (Install from Package Manager)

```yaml
- hosts: all
  gather_facts: true
  roles:
  - role: salvoxia.powertop
```

## Example Playbook (Install from Source)

```yaml
- hosts: all
  gather_facts: true
  roles:
  - role: salvoxia.powertop
    powertop_install_from_source: true
    powertop_source_tag: v2.15
```

## Cheat Sheet

Run a Docker container with systemd:
```bash
sudo docker run --tmpfs /tmp --tmpfs /run -v /sys/fs/cgroup:/sys/fs/cgroup:rw --cgroupns=host --privileged --name sysd --rm geerlingguy/docker-debian11-ansible
```

## License
MIT

## Author Information

This role was created in 2024 by Salvoxia (salvoxia@blindfish.info).