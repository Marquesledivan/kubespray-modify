Prerequisites
You need to install Ansible in your computer and have an account into AWS. If you need to run it in another cloud your need to change the provisioning playbook.

To create the instance is needed that you set two environment variables, AWS_SECRET_ACCESS_KEY and AWS_ACCESS_KEY_ID, with your AWS account info.

export AWS_ACCESS_KEY_ID="SHDJSJHDJBNTTS"
export AWS_SECRET_ACCESS_KEY="hSs8s8282kkdbJzUdddd/ss/o+ser"
Install PIP:

curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
Install boto3 and ansible:

pip install ansible
pip install boto3

You need also have a key pair file, for example my-key-pair.pem, that is used to connect into your AWS instances.

Installing
After you create the variables, you need to clone this repo.

git clone <repo>
Access the playbook directory:

cd k8s-canary-deploy-example/provisioning-playbook/
Open the file roles/common/vars/main.yml, and edit any option that you like, for example, the name of your key pair file from AWS.

instance_type: t2.medium #type of instance
security_group: ledivan-cluster #name of security group that will be created
image: ami-0d2505740b82f7948 #ami Ubuntu 18.04 TLS official
keypair: my-key-pair.pem #your key pair file
region: us-east-1 # region that will be created the instances
count: 3 #number of instances
Load your key pair "my-key-pair.pem" that you specified in the in the var file using ssh-keyscan.

ssh-add path/of/my-key-pair.pem
Execute the playbook:

ansible-playbook -i hosts main.yml

####################

# Install dependencies from ``requirements.txt``

sudo pip install -r requirements.txt


When the playbook finishes, you can check in hosts file the IP of new instances:

vim inventory/kubernetes/inventory.ini

# ## Configure 'ip' variable to bind kubernetes services on a
# ## different ip than the default iface
# ## We should set etcd_member_name for etcd cluster. The node that is not a etcd member do not need to set the value, or can set the empty string value.
[all]
master.ledivan.com ansible_host=54.233.106.44
node1.ledivan.com  ansible_host=18.231.176.26
node2.ledivan.com  ansible_host=52.67.4.226  

# ## configure a bastion host if your nodes are not directly reachable
# bastion ansible_host=x.x.x.x ansible_user=some_user

[kube-master]
master.ledivan.com


[etcd]
master.ledivan.com

[kube-node]
node1.ledivan.com
node2.ledivan.com

[k8s-cluster:children]
kube-master
kube-node



######EXECUTE############

ansible-playbook --flush-cache -u ubuntu -i inventory/kubernetes/inventory.ini cluster.yml --become --become-user=root -e ignore_assert_errors=yes -b -v --private-key=

