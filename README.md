# Ansible Docker Swarm Cluster

Create and deploy an n-node docker swarm cluster in minutes, with [Traefik](https://containo.us/traefik/) as a reverse proxy and load balancer, [Portainer](https://www.portainer.io/) to manage containers and [Jenkins](https://www.jenkins.io/) as a CI/CD solution.

## Prerequisites

- One or more servers running Ubuntu 20.04 connected through a private network (tests were done using [GCP](https://console.cloud.google.com/)).
- [Ansible](https://www.ansible.com/).
- SSH access to these servers using your public key (you can achieve this by simply using `ssh-copy-id <hostname>`).

## Preparation

First clone this repo to your local machine

```bash
git clone https://github.com/rodrigoegimenez/ansible-docker-swarm-cluster.git
cd ansible-docker-swarm-cluster
```

Then edit the `hosts` file by replacing the ip of your master server and replacing/adding as many nodes as you wish to join your server (if just starting you can just have your master server and add nodes later on).

## Running

Once finished editing your `hosts` file you can run all the required commands using the bootstrap playbook:

```bash
ansible-playbook -i hosts swarm-bootstrap.yml
```

This command will start running the imported playbooks in the order they appear:

```ansible
- import_playbook: ssh-keyscan.yml
- import_playbook: docker-dependencies.yml
- import_playbook: master.yml
- import_playbook: workers.yml
- import_playbook: traefik/traefik.yml
- import_playbook: portainer/portainer.yml
- import_playbook: jenkins/jenkins.yml
```

After a couple minutes the cluster will be up and initialized and ansible will start to install traefik and the other services asking a prompt for user, password and email (for traefik), and for the domain names for all three services (to use with traefik reverse proxy). Make sure you point these (sub)domains to the master's public ip address.

A couple of more minutes and you should have your cluster up and running.

<!-- ### `ssh-keyscan.yml`

This playbook will add the public keys of all the nodes of your cluster (master and workers) to your `known_hosts` local file. -->

## Acknowledgement

This idea is heavily influenced by [Docker Swarm Rocks](https://dockerswarm.rocks/). Traefik and portainer's compose files were obtained from this guide.

Another source of inspiration, specially for the ansible part, was [this](https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04) DigitalOcean's guide to create a Kubernetes Cluster.
