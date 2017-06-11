# Purpose

Spin up different OS types using Vagrant and learn Ansible at the same time if
desired. Some distros also include Desktop versions.

## Requirements

-   [Ansible]
-   [Vagrant]
-   [Virtualbox]


-   For using [Alpine] boxes the `vagrant-alpine` plugin is **required**.

```bash
vagrant plugin install vagrant-alpine
```

-   Also for [Alpine] boxes, they use `NFS` for their `/vagrant` synced_folder.
    This means you will be prompted for `sudo` password when spinning up a box.
    This can be changed to not prompt by references
    [here](https://www.vagrantup.com/docs/synced-folders/nfs.html).

    **tl;dr**

    For `OS X`, `sudoers` should have this entry:

```bash
        Cmnd_Alias VAGRANT_EXPORTS_ADD = /usr/bin/tee -a /etc/exports
        Cmnd_Alias VAGRANT_NFSD = /sbin/nfsd restart
        Cmnd_Alias VAGRANT_EXPORTS_REMOVE = /usr/bin/sed -E -e /*/ d -ibak /etc/exports
        %admin ALL=(root) NOPASSWD: VAGRANT_EXPORTS_ADD, VAGRANT_NFSD, VAGRANT_EXPORTS_REMOVE
```

For `Ubuntu Linux` , `sudoers` should look like this:

```bash
Cmnd_Alias VAGRANT_EXPORTS_CHOWN = /bin/chown 0\:0 /tmp/*
Cmnd_Alias VAGRANT_EXPORTS_MV = /bin/mv -f /tmp/* /etc/exports
Cmnd_Alias VAGRANT_NFSD_CHECK = /etc/init.d/nfs-kernel-server status
Cmnd_Alias VAGRANT_NFSD_START = /etc/init.d/nfs-kernel-server start
Cmnd_Alias VAGRANT_NFSD_APPLY = /usr/sbin/exportfs -ar
%sudo ALL=(root) NOPASSWD: VAGRANT_EXPORTS_CHOWN, VAGRANT_EXPORTS_MV, VAGRANT_NFSD_CHECK, VAGRANT_NFSD_START, VAGRANT_NFSD_APPLY
```

For `Fedora Linux`, `sudoers` might look like this (given your user belongs to the vagrant group):

```bash
Cmnd_Alias VAGRANT_EXPORTS_CHOWN = /bin/chown 0\:0 /tmp/*
Cmnd_Alias VAGRANT_EXPORTS_MV = /bin/mv -f /tmp/* /etc/exports
Cmnd_Alias VAGRANT_NFSD_CHECK = /usr/bin/systemctl status --no-pager nfs-server.service
Cmnd_Alias VAGRANT_NFSD_START = /usr/bin/systemctl start nfs-server.service
Cmnd_Alias VAGRANT_NFSD_APPLY = /usr/sbin/exportfs -ar
%vagrant ALL=(root) NOPASSWD: VAGRANT_EXPORTS_CHOWN, VAGRANT_EXPORTS_MV, VAGRANT_NFSD_CHECK, VAGRANT_NFSD_START, VAGRANT_NFSD_APPLY
```

## Usage

```bash
git clone https://github.com/mrlesmithjr/vagrant-box-templates
cd vagrant-box-templates
```

Find the OS of your choice and spin up a node or more.
Each distro folder has a `nodes.yml` file which you can change the number of
nodes to spin up if desired.

If you would like to change `disks|interfaces|port_forwards` feel free to
uncomment those sections and adjust them as needed.

`Ubuntu/xenial64/server/nodes.yml`:

```yaml
    ---
    - name: 'node0'
      ansible_groups:
        - 'test-nodes'
      box: 'mrlesmithjr/xenial64'
      desktop: false
      # disks:
      #   - size: 10
      #     controller: "SATA Controller"
      #   - size: 10
      #     controller: "SATA Controller"
      # interfaces:
      #   - ip: 192.168.250.10
      #     auto_config: true
      #     method: 'static'
      #   - ip: 192.168.1.10
      #     auto_config: false
      #     method: 'static'
      #     network_name: 'network-1'
      mem: 512
      provision: false
      vcpu: 1
      # port_forwards:
      #   - guest: 80
      #     host: 8080
      #   - guest: 443
      #     host: 4433
```

When you are ready to spin up:

```bash
vagrant up
```

For example if I want to spin up a Ubuntu Trusty server node:

```bash
    cd vagrant-box-templates/Ubuntu/trusty64/server
    vagrant up
    vagrant ssh
```

If you are interested in learning Ansible ensure that Ansible is installed on
your host machine. There is an included `playbook.yml` file in the root folder
to use as a skeleton playbook. Within the root folder there is also an
included `requirements.yml` file which includes some basics Ansible roles that
can be installed to get an understanding of using this method. You will need to
install the roles on your host machine which can be done by:

```bash
sudo ansible-galaxy install -r requirements.yml -f
```

If you would like to provision the nodes when they startup you will need to
set `provision: true` in the `nodes.yml`.

```yaml
    - name: 'node0'
      ansible_groups:
        - 'test-nodes'
      box: 'mrlesmithjr/xenial64'
      desktop: false
      # disks:
      #   - size: 10
      #     controller: "SATA Controller"
      #   - size: 10
      #     controller: "SATA Controller"
      # interfaces:
      #   - ip: 192.168.250.10
      #     auto_config: true
      #     method: 'static'
      #   - ip: 192.168.1.10
      #     auto_config: false
      #     method: 'static'
      #     network_name: 'network-1'
      mem: 512
      provision: true
      vcpu: 1
      # port_forwards:
      #   - guest: 80
      #     host: 8080
      #   - guest: 443
      #     host: 4433
```

Most files are symlinked into each distro folder to keep a consistent and easy
method of changing things around. Feel free to change as needed.

And when you are all done with your Vagrant node(s) you can tear everything down
and cleanup easily by running the following:

`Non-Windows`:

```bash
    ./cleanup.sh
```

`Windows`:

```bat
    cleanup.bat
```

## License

BSD

## Author Information

Larry Smith Jr.

-   [@mrlesmithjr]
-   <http://everythingshouldbevirtual.com>
-   mrlesmithjr [at] gmail.com

[@mrlesmithjr]: https://www.twitter.com/mrlesmithjr

[ansible]: https://www.ansible.com

[vagrant]: https://www.vagrantup.com/

[virtualbox]: https://www.virtualbox.org/
