# Ansible Tower/AWX Resource Operator

---
> **_NOTE:_** This operator is experimental and is not considered supported or stable and ready for use
---

The purpose of this Operator is to allow managing Ansible Platform job resources that can interface with any infrastructure whether it be on an Openshift cluster or traditional infrastructure running off-cluster.

## Existing CRDs and Roles

Currently two CRDs are implemented with backing roles:


* AnsibleJob [CRD](deploy/crds/tower.ansible.com_joblaunch_crd.yaml) and [Role](roles/job/tasks/main.yml) given a Job Template name will run a job on the referenced Tower cluster
* Credential [CRD](deploy/crds/tower.ansible.com_credentials_crd.yaml) and [Role](roles/credential/tasks/main.yml) will create a Credential against the referenced Tower cluster
* Inventory [CRD](deploy/crds/tower.ansible.com_inventories_crd.yaml) and [Role](roles/inventory/tasks/main.yml) will create a Inventory against the referenced Tower cluster
* Job Template [CRD](deploy/crds/tower.ansible.com_jobtemplates_crd.yaml) and [Role](roles/job_template/tasks/main.yml) will create a Job Template against the referenced Tower cluster
* Module [CRD](deploy/crds/tower.ansible.com_modules_crd.yaml) and [Role](roles/module/tasks/main.yml) given a Module name will run the module against the referenced Tower cluster
* Organization [CRD](deploy/crds/tower.ansible.com_organizations_crd.yaml) and [Role](roles/organization/tasks/main.yml) will create a Organization against the referenced Tower cluster
* Permission [CRD](deploy/crds/tower.ansible.com_permissions_crd.yaml) and [Role](roles/permission/tasks/main.yml) will create a Permission against the referenced Tower cluster
* Project [CRD](deploy/crds/tower.ansible.com_projects_crd.yaml) and [Role](roles/project/tasks/main.yml) will create a Project against the referenced Tower cluster
* Team [CRD](deploy/crds/tower.ansible.com_teams_crd.yaml) and [Role](roles/team/tasks/main.yml) will create a Team against the referenced Tower cluster
* User [CRD](deploy/crds/tower.ansible.com_users_crd.yaml) and [Role](roles/user/tasks/main.yml) will create a User against the referenced Tower cluster


## Connectivity Secrets

All CRDs within this operator depend on a _Secret_ being defined within the desired namespace and this secret name must be given along with any of the CRs that wish, the _Secret_ itself has a particular k/v format:

    apiVersion: v1
    kind: Secret
    metadata:
      name: toweraccess
      namespace: aproject
      type: Opaque
    stringData:
      token: XXXXX
      host: https://towerhost.com

## Building the Operator

The current codebase has a fixed image location and should be modified, the examples below should also be modified to publish the images into a specific location. Start with building the operator image and publishing:

    $ operator-sdk build matburt/tower-resource-operator:0.1.0
    ...
    $ docker push matburt/tower-resource-operator:0.1.0
    ...

## Building the Job launch job container

The Tower Job launch CRD launches a k8s job in a dependent fashion. The k8s Job actually invokes the Tower Job and tracks it, this container needs to be built and published:

    $ docker build -t matburt/operator-job-run:latest -f build/Dockerfile.runner .

