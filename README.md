# Hyperion

[![License Apache 2][badge-license]][LICENSE]
![Version][badge-release]


## Description

A `Vagrantfile` is provided if you want to use it in virtual machines.
This installation creates a [Kubernetes][] system on a cluster of Ubuntu VMs

Install dependencies :

- [virtualbox][] (>= 4.3.10)
- [vagrant][] (>= 1.6)
- [ansible][]


### Kubernetes master

- maintains the state of the [Kubernetes][] server runtime
- API server
- Scheduler
- Registries (nodes, pod, service)
- Storage

### Kubernetes nodes

- Represents the Host where containers are created
- Components : PODs, Kubelet, Proxy

### Kubernetes components

- [etcd][] : A highly available key-value store for shared configuration and service discovery.
- apiserver : Provides the API for Kubernetes orchestration.
- controller-manager : Enforces Kubernetes services.
- scheduler : Schedules containers on hosts.
- proxy : Provides network proxy services.
- kubelet : Processes a container manifest so the containers are launched according to how they are described.

## Deployment

Initialize environment:

    $ make init

### Local

* Creates the cluster :

        $ make create

* Configure the cluster :

        $ make vagrant-master
        $ make vagrant-nodes

* Destroy the cluster:

        $ make destroy


### Cloud

* Setup an inventory file, like that :

        ```bash
        [masters]
        10.1.1.1 ansible_connection=ssh ansible_ssh_user=hyperion ansible_python_interpreter=/usr/bin/python2

        [nodes]
        10.1.1.11 ansible_connection=ssh ansible_ssh_user=hyperion ansible_python_interpreter=/usr/bin/python2
        10.1.1.12 ansible_connection=ssh ansible_ssh_user=hyperion ansible_python_interpreter=/usr/bin/python2

        [etcd]
        10.1.1.1 ansible_connection=ssh ansible_ssh_user=hyperion ansible_python_interpreter=/usr/bin/python2
        ```

* Configure the cluster :

        $ make master inventory=<inventory_filename>
        $ make nodes inventory=<inventory_filename>



## Usage

* Check [Kubernetes][] status :

        $ curl http://10.245.1.100:8080/
        {
          "paths": [
             "/api",
             "/api/v1",
             "/api/v1beta3",
             "/healthz",
             "/healthz/ping",
             "/logs/",
             "/metrics",
             "/static/",
             "/swagger-ui/",
             "/swaggerapi/",
             "/ui/",
             "/version"
           ]
        }

* You could see the dashboard on the master : `http://....:8080/ui`

* You could use the ``kubectl`` binary to manage your cluster :

        $ bin/kubectl -s 10.245.1.100:8080 version
        Client Version: version.Info{Major:"0", Minor:"19", GitVersion:"v0.19.1", GitCommit:"bb63f031d4146c17113b059886aea66b09f6daf5", GitTreeState:"clean"}
        Server Version: version.Info{Major:"0", Minor:"19", GitVersion:"v0.19.1", GitCommit:"bb63f031d4146c17113b059886aea66b09f6daf5", GitTreeState:"clean"}

        $ bin/kubectl -s 10.245.1.100:8080 get nodes
        NAME      LABELS    STATUS

        $ bin/kubectl -s 10.245.1.100:8080 get namespaces
        NAME      LABELS    STATUS
        default   <none>    Active

        $ bin/kubectl -s 10.245.1.100:8080 cluster-info
        Kubernetes master is running at 10.245.1.100:8080

* Creates namespaces :

        $ bin/kubectl -s 10.245.1.100:8080 create -f namespaces/namespace-admin.json
        $ bin/kubectl -s 10.245.1.100:8080 create -f namespaces/namespace-dev.json
        $ bin/kubectl -s 10.245.1.100:8080 create -f namespaces/namespace-prod.json
        $ bin/kubectl -s 10.245.1.100:8080 get namespaces
        NAME      LABELS    STATUS
        admin     name=admin   Active
        default   <none>    Active
        development   name=development   Active
        production   name=production   Active


## Debug

### Ping all hosts

    $ ansible all -m ping -i <inventory>

### Check connection to hosts

You could retrieve facts from hosts to check connections :

    $ ansible all -m setup -a "filter=ansible_distribution*" -i <inventory>



## Contributing

See [CONTRIBUTING](CONTRIBUTING.md).


## License

See [LICENSE][] for the complete license.


## Changelog

A [changelog](ChangeLog.md) is available


## Contact

Nicolas Lamirault <nicolas.lamirault@gmail.com>


[Hyperion]: https://github.com/nlamirault/hyperion
[COPYING]: https://github.com/nlamirault/hyperion/blob/master/COPYING
[Issue tracker]: https://github.com/nlamirault/hyperion/issues

[kubernetes]: http://kubernetes.io/
[etcd]: https://github.com/coreos/etcd

[vagrant]: https://www.vagrantup.com
[virtualbox]: https://www.virtualbox.org/
[ansible]: http://www.ansible.com/

[badge-license]: https://img.shields.io/badge/license-Apache_2-green.svg
[badge-release]: https://img.shields.io/github/release/nlamirault/hyperion.svg
