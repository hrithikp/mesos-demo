# Mesos Demo

## Cluster Provisioning

### Infrastructure

- Demo Cluster
	- 1 Master Node
	- N <= 10 Worker Nodes
- Fault Tolerant Cluster
	- 2 Master Nodes
	- N <= 100 Worker Nodes
- High Availibility Cluster
	- 3 Master Nodes
	- N <= 1000 Worker Nodes

Current version does not have a distributed file system and thus makes data persistence an issue. However, that limitation is has many solutions available, below is an example using AWS.

Database:

- MySQL deployed and managed via RDS
- MongoDb deployed and managed via MMS(very similar to RDS, runs on AWS)
- DynamoDb alternate NoSQL database

File Storage:

- Create EC2 instances with EBS mounts that DO NOT terminate with instance.
- Use S3 for file storage, either streaming directly or doing periodic sync.

Note: With the limitation of persistence also comes a big advantage of being able to scale down to optimize for cost.


### Mesos

Installation and configuration of mesos and related software packages is handled via ansible scripts.

```
cd ansible
cp hosts.sample hosts
// Modify hosts file to match your cluster config
cp ansible.cfg.sample ansible.cfg
// Update the keypath in ansible.cfg
ansible-playbook mesos-cluster.yml
```

## App Containers

### Example Drupal Application
```
cd example/drupal
// Create local environment
make local
// Connect to the local environment via shell
docker exec -it drupal bash
// To install a dev db into mysql
// First connect to local environment via shell
make install
```

### Example NodeJS Application

### Example IPython Notebook

## Launching Apps

### Local

### Cluster
