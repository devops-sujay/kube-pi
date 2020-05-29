# KubePI : 

 Hosts.yml

```
all:
  vars:
    ip_master: 192.168.86.132
  children:
    nodes:
      hosts:
        pi1:
          ansible_host: 192.168.86.133
        pi2:
          ansible_host: 192.168.86.139
        pi3:
          ansible_host: 192.168.86.144
        pi4:
          ansible_host: 192.168.86.143
          master: true
```

Playbook Installation 

```
- hosts: master
  roles:
    - kubpi
```

Create the cluster

```
ansible-playbook -i hosts.yml -u pi -k -b install-kub.yml
```

Reset all

```
ansible-playbook -i hosts.yml -u pi -k -b reset-all.yml
```

Playbook to reset the cluster

```
- hosts: all
  become: yes
  tasks:

  - name: kubeadm reset
    shell: kubeadm reset -f

  - name: check clean docker
    shell: docker ps -aq | wc -l
    register: docker_container_exist

  - name: clean docker
    shell: docker rm -f $(docker ps -aq)
    when: docker_container_exist.stdout != "0"

  - name: suppression paquet kubernetes
    apt:
      name:
       - kubectl
       - kubadm
       - kubernetes-cni
       - kubelet
      purge: yes
      autoremove: yes
      state: absent
```

