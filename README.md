ci-operator/oc-template ansible role

        Parse and pre-process an OpenShift template to create dynamic inventory hosts for each resource object found therein. 

        This role will retrieve a template from a git repository and store its configuration parameters and repository 
        information in a configmap in the target namespace. If this configmap is present, default configuration parameters will
        be read from its contents and any configuration parameters provided by this role will override and update existing
        parameters in the configmap.

        Because this role stores files on the ansible host, a 'mgmt_dir' path must be provided for persistence and idempotency.
        The result of running this role for will be the creation of dynamic inventory hosts in the 'k8s_resource' group; one
        inventory host will be added for each unique resource definition found in the processed template.

        Note that the only changes made to a cluster by this role are to an 'app-config-*' configmap - this role applies neither
        the template itself nor the processed resource definitions to the cluster.
        This role is designed to pair with the 'ci-operator/resource' role which will apply to a kubernetes cluster any resource
        definition found in the 'resource' variable which is populated by this role in each new dynamic inventory host.



VARIABLES
        app_name: The name applied to the configmap - app-config-{{ app_name }}
        app_repo: Git repository URL where the Openshift template is found.
        app_branch: Git branch where the Openshift template is found (default: 'master')
        app_template: Filename in root of repository for the Openshift template.
        app_owner: Metadata string to be stored in app-config configmap.
        app_params: A dictionary of parameters which will be used to process the template.



EXAMPLE
  roles:
    - role: ci-operator/oc-template
      vars: 
        app_name: prometheus
        app_repo: git@gitlab.cee.redhat.com:josiah/templates/prometheus.git
        app_branch: master
        app_template: prometheus.yaml
        app_owner: "{{ owner_email }}"
        app_params:
          NAMESPACE: "{{ inventory_hostname }}"
          DOMAIN: "mydomain.com"
