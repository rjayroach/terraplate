# Terraplate-roles

These are example roles the demonstrate how you can build your own Ansible roles that will work within the Terraplate templating environment

## Example Role

This will create a Route53 subdomain

### vars/main.yml

```yaml
---
module_meta:
  ns: dns
  name: subdomain
  path: aws/subdomain

variables:
  name:
    description: "The subdomain's FQDN"

remote_state_vars:
  parent_zone_id:
    description: 'The zone id of the parent domain'
    reference: 'domain.zone_id'

output_vars:
  zone_id:
    description: 'The zone id of the created domain'
    value: 'aws_route53_zone.subdomain.zone_id'
```

### files/main.tf

```hcl
# Create a subdomain
resource "aws_route53_zone" "subdomain" {
  comment = "Managed by Terraform"
  name    = "${var.name}"
}

# Create NS records for the subdomain in the parent domain
resource "aws_route53_record" "subdomain_ns" {
  name    = "${var.name}"
  ttl     = "300"
  type    = "NS"
  zone_id = "${var.parent_zone_id}"
  records = [
    "${aws_route53_zone.subdomain.name_servers.0}",
    "${aws_route53_zone.subdomain.name_servers.1}",
    "${aws_route53_zone.subdomain.name_servers.2}",
    "${aws_route53_zone.subdomain.name_servers.3}"
  ]
}
```

### tasks/main.yml

```yaml
---
- include_role:
    name: terraplate

- include_role:
    name: terraplate
    tasks_from: render
```
