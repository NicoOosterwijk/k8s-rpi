# Create a Raspberry Pi based Kubernetes cluster with Ansible

## Why?

* Raspberry Pis are cheap
* Ansible is awesome
* Kubernetes is hot

# Prerequisites

## Hardware

* Raspberry Pi 3 (3 or more)
* Class 10 SD Cards
* Gigabit Network Switch (5 ports or more)
* Wired Network connections with access to the internet

## Software

* [Raspbian Buster Lite](https://www.raspberrypi.org/downloads/raspbian/) (installed on each Raspberry Pi)

* Raspberry Pis should have static IPs
    * Requirement for Kubernetes and Ansible inventory
    * The Ansible common role will set it in the dhcpcd.conf file

* Ability to SSH into all Raspberry Pis and escalate privileges with sudo
    * The pi user is fine just change its password
    * Use __'ssh-copy-id'__ on your control station to copy your SSH public key to the nodes

* [Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html) 2.7 or higher
    * To be able to run the playbooks

* [`kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
    * If you are administering the cluster from your control station, `kubectl` 


## Download the latest release or clone the repo:

```
git clone https://git.digitalinfo.nl/nico/k8srpi.git
```

## Modify ansible.cfg and inventory

Modify the `inventory` file to suit your environment. Change the names to your liking and the IPs to the addresses of your Raspberry Pi's.

If your SSH user on the Raspberry Pis are not the Raspbian default `pi` user modify `remote_user` in the `ansible.cfg`.

## Confirm Ansible is working with your Raspberry Pis:

```
ansible -m ping all
```
This may fail to ping if you have not setup SSH keys and only configured your Pi's with passwords
## Deployment

```
ansible-playbook site.yml
```

# Interact with Kubernetes

## From you control station

* Copy the /etc/kubernetes/admin.conf file from the master node to your control station as $HOME/.kube/config
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

## Dashboard

You can also install the non-HTTPS version of the Kubernetes dashboard. This is not recommended for production clusters but, it simplifies the setup. Access the dashboard by running:

```
kubectl proxy
```

Then open a web browser and navigate to:
[http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)
