NGINX Controller Publish API
============================

Upsert (create and update) API Publishing definitions in NGINX Controller.
Publishing an API is the act of linking an API definition version to a Gateway(s). The result of configuring this link is that the configuration is applied to the Gateway and published to a Developer Portal (if one is configured).

Requirements
------------

[NGINX Controller](https://www.nginx.com/products/nginx-controller/)

Role Variables
--------------

### Required Variables

`nginx_controller.fqdn` - The hostname or DNS of the NGINX Controller instance.

`nginx_controller.auth_token` - An authentication token for the NGINX Controller API (the nginx_controller_generate_token role outputs this).

`nginx_controller_publish_api.metadata.name` - Name of the API Definition

`nginx_controller_publish_api.desiredState.apiDefinitionVersionRef` - The version reference section.

`nginx_controller_publish_api.desiredState.gatewayRefs` - The gateway reference section.

### Template Variables

This role has multiple template related variables. The descriptions and defaults for all these variables can be found in **[vars/main.yml](./vars/main.yml)**

Dependencies
------------

Example Playbook
----------------

To use this role you can create a playbook such as the following (let's name it `nginx_controller_publish_api.yaml` for the purposes of this example).

```yaml
- hosts: localhost
  gather_facts: no
  vars:
    nginx_controller_user_email: "user@example.com"
    nginx_controller_user_password: "mySecurePassword"
    nginx_controller_fqdn: "controller.mydomain.com"
    nginx_controller_validate_certs: false

  tasks:
    - name: Retrieve the NGINX Controller auth token
      include_role:
        name: nginxinc.nginx_controller_generate_token

    - name: Publish the API version
      include_role:
        name: nginxinc.nginx_controller_publish_api
      vars:
        nginx_controller_environment: "prod"
        nginx_controller_application: "sports-results"
        nginx_controller_publish_api:
          metadata:
            name: "f1-v1-prod"
            displayName: "F1 Results ðŸ�Ž"
            tags:
            - tagOne
            - tagTwo
          desiredState:
            apiDefinitionVersionRef:
            - ref: "/services/api-definitions/f1-results/versions/v1"
            gatewayRefs:
            - ref: "/services/environments/prod/gateways/apigw"
```

You can then run `ansible-playbook nginx_controller_publish_api.yaml` to execute the playbook.

Alternatively, you can also pass/override any variables at run time using the `--extra-vars` or `-e` flag like so `ansible-playbook nginx_controller_publish_api.yaml -e "nginx_controller_user_email=user@company.com nginx_controller_user_password=notsecure nginx_controller_fqdn=controller.example.local nginx_controller_validate_certs=false"`

You can also pass/override any variables by passing a `yaml` file containing any number of variables like so `ansible-playbook nginx_controller_publish_api.yaml -e "@nginx_controller_publish_api_vars.yaml"`

License
-------

[Apache License, Version 2.0](./LICENSE)

Author Information
------------------

[Brian Ehlert](https://github.com/brianehlert)

&copy; [NGINX, Inc.](https://www.nginx.com/) 2020
