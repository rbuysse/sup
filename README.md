# ec2-docker-buildx

Creates a buildx cluster on AWS to use in GHA workflows when qemu is too slow

Provides two actions:

start:

1. Creates two instances
2. Bootstraps the buildx cluster
3. Installs GHA runner software with the --ephemeral option

stop:

1. Terminates the 

Assumes you have two pre-builts AMIs

AMD runner: Docker installed

ARM runner: Docker installed and daemon exposed on port 2375

Steps to expose docker daemon:

```
sudo vi /etc/docker/daemon.json

{
  "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2375"]
}

sudo vi /lib/systemd/system/docker.service
ExecStart=/usr/bin/dockerd --containerd=/run/containerd/containerd.sock

sudo systemctl daemon-reload

sudo systemctl restart docker.service
``` 


# Action configuration

Inputs:

  action:
    start - deploy a new cluster
    stop - destroy a running cluster

  amd_ami_id:
      AMI ID for the AMD instance. Should have docker installed.

  amd_instance_type:
      Instance Type for the AMD instance.

  arm_ami_id:
      AMI ID for the ARM instance. Should have Docker installed and daemon exposed on port 2375.

  arm_instance_type:
      Instance Type for the ARM instance

  gh_personal_access_token:
      GitHub Personal Access Token with "repo" permissions.

  label:
      Label applied to the created EC2 instances during creation.
      This is required when running the 'stop' action.

  security_group_id:
      Must allow outbound traffic to connect to GitHub.

  subnet:
    description: >-
      Subnet to apply to the instances
    required: false

outputs:

  label:
      Random value generated when creating a new cluster. This is used for job isolation.
