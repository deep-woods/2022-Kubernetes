# <span id='top'>Bonus - ☸️Kubeadm</span>

[[Setup Vagrant]](#vagrant)  
[[]](#)  
[[🤲Hands-on]](#)  
[[References]](#ref)

<br>

## <span id='vagrant'>Setup Vagrant</span>

[[Top]](#top)

Environment: Play with K8S lab

1.  [Install `Vagrant`](https://phoenixnap.com/kb/how-to-install-vagrant-on-centos-7)

Download the installation package

    wget https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.rpm

Install Vagrant on your CentOS machine

    yum –y localinstall vagrant_2.2.19_x86_64.rpm

    vagrant ––version

2.  Clone repo.

        git clone https://github.com/kodekloudhub/certified-kubernetes-administrator-course.git

        Cloning into 'certified-kubernetes-administrator-course'...
        remote: Enumerating objects: 3996, done.
        remote: Counting objects: 100% (374/374), done.
        remote: Compressing objects: 100% (316/316), done.
        remote: Total 3996 (delta 127), reused 189 (delta 58), pack-reused 3622
        Receiving objects: 100% (3996/3996), 65.31 MiB | 6.78 MiB/s, done.
        Resolving deltas: 100% (1933/1933), done.

        [node1 ~]$ ls
        anaconda-ks.cfg  certified-kubernetes-administrator-course

        [node1 ~]$ cd certified-kubernetes-administrator-course ; ls
        README.md  Vagrantfile  docs  images  ubuntu

3.  Check `Vagrantfile`.

        # Define the number of master and worker nodes
        # If this number is changed, remember to update setup-hosts.sh script with the new hosts IP details in /etc$
        NUM_MASTER_NODE = 1
        NUM_WORKER_NODE = 2

        IP_NW = "192.168.56."
        MASTER_IP_START = 1
        NODE_IP_START = 2

4.  Check `vagrant` status.

        $ vagrant status

        No usable default provider could be found for your system.

        Vagrant relies on interactions with 3rd party systems, known as
        "providers", to provide Vagrant with resources to run development
        environments. Examples are VirtualBox, VMware, Hyper-V.

        The easiest solution to this message is to install VirtualBox, which
        is available for free on all major platforms.

        If you believe you already have a provider available, make sure it
        is properly installed and configured. You can see more details about
        why a particular provider isn't working by forcing usage with
        `vagrant up --provider=PROVIDER`, which should give you a more specific
        error message for that particular provider.

<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/kubernetes-for-the-absolute-beginners-hands-on/
- Install Vagrant https://phoenixnap.com/kb/how-to-install-vagrant-on-centos-7
- Download Vagrant https://releases.hashicorp.com/vagrant/2.2.19/
