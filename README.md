# How to stand up K8s with Kubespary(Terraform and Ansible)

### Stand up the hardware
```
git clone https://github.com/kubernetes-incubator/kubespray.git
cd kubespray
cd contrib/terraform/aws
edit the files for you aws account
terraform init
terraform apply -var-file=credentials.tfvars
Take the output from the terraform and copy/paste into kubespray/inventory/hosts

should look similar to this:

 [all]
kubernetes-production-master0 ansible_host=10.250.192.138
kubernetes-production-worker0 ansible_host=10.250.195.240
kubernetes-production-worker1 ansible_host=10.250.217.196
kubernetes-production-worker2 ansible_host=10.250.202.109
kubernetes-production-etcd0 ansible_host=10.250.203.28
bastion ansible_host=54.89.80.238
bastion ansible_host=54.209.28.146

[bastion]
bastion ansible_host=54.89.80.238
bastion ansible_host=54.209.28.146

[kube-master]
kubernetes-production-master0


[kube-node]
kubernetes-production-worker0
kubernetes-production-worker1
kubernetes-production-worker2


[etcd]
kubernetes-production-etcd0


[k8s-cluster:children]
kube-node
kube-master


[k8s-cluster:vars]
apiserver_loadbalancer_domain_name="kubernetes-elb-production-1927665643.us-east-1.elb.amazonaws.com"

```
```
### Setup Ansible from the README.md on Kubespray

# Install dependencies from ``requirements.txt``
sudo pip install -r requirements.txt

# Copy ``inventory/sample`` as ``inventory/mycluster``
cp -rfp inventory/sample inventory/mycluster

# Review and change parameters under ``inventory/mycluster/group_vars``
cat inventory/mycluster/group_vars/all/all.yml

I added these to mine:

ansible_userr: ubuntu 
ansible_ssh_private_key_file: ansible.pem
bootstrap_os: ubuntu


cat inventory/mycluster/group_vars/k8s-cluster/k8s-cluster.yml

cloud_proviider: aws
```
```
ansible-playbook -vvvv -b --user=ubuntu -i inventory/hosts cluster.yml -e ansible_user=ubuntu -e ansible_ssh_private_key_file=ansible.pem
```


