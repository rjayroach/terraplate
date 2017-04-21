# Terraplate

Terraplate gives you DRY [Terraform](https://terraform.io) templates and moudules (!)
and an anti bike shedding tool to provide a 'convention over configuration' environment for laying out multiple infrastructures over many environments

Terraplate does not actually run Terraform (or Terragrunt) to create or destroy infrastructure.
It's job is to generate the Terraform code that you run.

## Dependencies

- [Terraform](https://terraform.io)  ;-)
- [Terragrunt](https://github.com/gruntwork-io/terragrunt) to define variable values in tfvars files
- [Ansible](https://www.ansible.com) to define roles and playbooks to generate your terraform code


## Using

The Terraplate role itself does not generate any specific Terraform module or template. Your roles do that.
The Terraplate role provides tasks that will generate the Terraform artificats based on the values in your role.
Your role needs to define the following:

- vars/main.yml
- files/main.tf
- tasks/main.yml

See the [Terraplate Roles](https://github.com/rjayroach/terraplate-roles) for examples

In order to generate the Terraform code that your role defines you run an ansible playbook. Your playbook needs to:

- set the fact 'package' to a value
- include a role at the top 'terraplate/setup'

After that setup work is done your playbook should include the roles that you have defined.
See the [Terraplate Playbooks](https://github.com/rjayroach/terraplate-playbooks) for examples


## Concepts

### Component

This is an envrionment agnostic definition that:

- wraps the Terraform module defined by your role in a <module>tf
- includes a remote-state.tf and a provider.tf

### Instance

This is an environment specific definition that:

- includes a tfvars file with the values to feed to the component
