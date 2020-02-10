# Create a Raspberry Pi based Kubernetes cluster with Ansible

## Why?

* Raspberry Pi's are cheap
* Ansible is awesome
* Kubernetes is hot
* It's fun!

# Prerequisites

## Hardware

* Raspberry Pi 3 B+ (3 or more)
* USB powerport (like Xtorm) to supply power to the RPI's
* Class 10 SD Cards (16G or 32G)
* Gigabit Network Switch (5 ports or more)
* Ethernet cables for your RPI's (1 for every RPi plus 1 for the core port)
* Wired Network connections with access to the internet
* Stackable RPi case
* Control Station (Mac, Linux or Windows computer)

## Software

* [Raspbian Hypriot](https://blog.hypriot.com/downloads/) (install this on each Raspberry Pi)
* Raspberry Pi's should have static IP addresses
  * Requirement for Kubernetes and Ansible inventory
  * The Ansible common role will set it in the **dhcpcd.conf** file
* Allow Hypriot to rename the hostname
  * The Ansible common role will set the **user_data** file to allow this
* Ability to SSH into all Raspberry Pis and escalate privileges with sudo
  * The **pirate** user is fine just change its password
  * Use __'ssh-copy-id'__ on your control station to copy your SSH public key to the nodes
* [Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html) 2.7 or higher
  * To be able to run the playbooks
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
  * If you are administering the cluster from your control station, `kubectl` 

## Clone the repository:

```
git clone http://git.digitalinfo.nl/nico/k8s-rpi.git
```

## Modify ansible.cfg and inventory

Modify the **inventory** file to suit your environment. Change the names to your liking and the IPs to the addresses of your Raspberry Pi's.

If your SSH user on the Raspberry Pi's are not the Raspbian default `pi` user modify `remote_user` in the `ansible.cfg`.

## Modify the versions in the group_vars all file

Modify the **all** file to suit the versions being used for the software packages

## Confirm Ansible is working with your Raspberry Pi's:

```
ansible -m ping all
```
This may fail to ping if you have not setup SSH keys and only configured your Pi's with passwords
## Deployment

```
ansible-playbook site.yml
```

# Interact with Kubernetes

## From your control station

* Copy the `/etc/kubernetes/admin.conf` file from the __master__ node to your control station as `$HOME/.kube/config`
    * (or name it different but then export the KUBECONFIG environment variable to point to this file)

Test your Kubernetes cluster is up and running:

```
kubectl get nodes
```

The output should look something like this:

```
NAME       STATUS    ROLES     AGE       VERSION
master     Ready     master    18h       v1.15.0
node1      Ready     <none>    18h       v1.15.0
node2      Ready     <none>    18h       v1.15.0
```


