Role Name
=========

Manage AWS Launch Configurations and Autoscaling Groups for Omnia-based deployments.

Example Playbook
----------------

* An example from tasks/release-prod.yml:

```
- name: deploy
  hosts: tag_Env_admin,&tag_Stack_st2,&tag_Layer_stackstorm
  gather_facts: False
  vars:
    previous_env: 'qa'
    env: 'prod'
    stack: 'app'
    layer: 'web'
    app_name: 'rails'
    app_repo: ...
    default_instance_type: ...

    - name: get AMI id from launch_configuration_json
      set_fact:
        ami_id_from_launch_configuration: "{{ launch_configuration_json.LaunchConfigurations[0].ImageId }}"
        when: set_vpc_json.Vpcs[0] is defined

  roles:
    - { role: reactiveops.get-vpc-facts-master, tags: ['facts'] }
    - { role: reactiveops.manage-asg-master, tags: ['asg'], new_ami_id: "{{ami_id_from_launch_configuration}}", web_elb: "{{env}}-{{stack}}-{{layer}}" }

```