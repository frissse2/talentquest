# Ansible

This folder contains the Ansible automation used for platform bootstrap and recovery.

## Responsibilities

Ansible is responsible for:
- applying Talos-related bootstrap steps
- bootstrapping FluxCD
- installing secret decryption prerequisites
- preparing base platform prerequisites
- validating bootstrap success

## Non-responsibilities

Ansible is not used for long-term application deployment or day-to-day Kubernetes resource management.

Those are handled through FluxCD from the GitOps repository.

## Rule

If a resource should continue to exist and be reconciled in Kubernetes, it should normally live in the GitOps repository, not in an Ansible task.
