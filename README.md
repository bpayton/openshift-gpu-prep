This repo helps set up the prerequisites for this [blog post](https://blog.openshift.com/how-to-use-gpus-with-deviceplugin-in-openshift-3-10/).


### Request Access
Open a ticket with AWS support and request access to the p3.2xlarge instance. This will take a couple days to go through so do this first!

### Use CloudFormation to Set up Infrastructure
Once you have access to the instance, upload `CloudFormationTemplateOpenShift.yaml` to an S3 bucket. From there, copy the URL and create a new [CloudFormation Stack](https://console.aws.amazon.com/cloudformation/)

### Clone openshift-ansible
```
$ git clone https://github.com/openshift/openshift-ansible.git
$ cd openshift-ansible
$ git checkout origin/release-3.10
```
This gives us the playbooks to install OpenShift and its prerequisites.

### Set up your inventory file
Go to your [EC2 Dashboard](https://console.aws.amazon.com/ec2/v2/)  and grab the public DNS names for your worker node and your master node and paste them into your `inventory` file.

### Run the playbooks
1. `prepare.yaml`
	This playbook does some initial set up that is not included in the openshift-ansible prerequisites playbook. It's included in this repo.
	```
	$ ansible-playbook ~/Workspace/openshift-origin-ansible/prepare.yaml -i ~/Workspace/openshift-gpu-prep/inventory --key-file ~/.ssh/id_rsa
	```
2.  `prerequisites.yml`
	We can run this from the openshift-ansible repo. It will take a while
	```
	$ ansible-playbook -i ~/Documents/openshift-origin-ansible/inventory --key-file ~/.ssh/id_rsa \
	  ~/Documents/openshift-ansible/playbooks/prerequisites.yml
	```
3. `deploy_cluster.yml`
	Installs OpenShift!
	```
	$ ansible-playbook -i ~/Documents/openshift-origin-ansible/inventory --key-file ~/.ssh/id_rsa \
	  ~/Documents/openshift-ansible/playbooks/prerequisites.yml
  ```

### Done!
From here you can view your dashboard at https://[master node DNS name]:8443. You now have an OpenShift cluster installed with a master node and a worker node with a GPU. Next steps are to follow the [blog post](https://blog.openshift.com/how-to-use-gpus-with-deviceplugin-in-openshift-3-10/) to configure your cluster to consume the GPU resource!
