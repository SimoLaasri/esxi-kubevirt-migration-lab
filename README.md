# ESXi to KubeVirt Migration Lab

An internship lab where I explored moving an Ubuntu virtual machine from **VMware ESXi** to **Kubernetes with KubeVirt**.

I set up the Kubernetes environment with kubeadm and containerd, installed KubeVirt, prepared a source VM on nested ESXi, and documented the migration workflow. **The complete migration was not validated:** my local environment did not have enough resources to finish the end-to-end test.

This repository presents that work and its limits. The original VMs and working files are no longer available, so it contains the report and technical notes rather than a working migration tool.

## Work completed and remaining

| Stage | Status recorded in the report |
|---|---|
| Ubuntu Server control-plane and worker VMs | Configured in VMware Workstation |
| Kubernetes cluster | Deployed using kubeadm and containerd |
| KubeVirt | Installation procedure documented |
| Source environment | Nested ESXi and an Ubuntu source VM prepared |
| Disk export and transfer | Workflow and screenshots documented |
| Disk import, guest boot, and application checks | End-to-end result not validated |

## Architecture

```mermaid
flowchart LR
    E["Nested ESXi<br/>Ubuntu source VM"] --> X["Export VM disk<br/>VMDK"]
    X -. "planned conversion and import" .-> S["Kubernetes persistent storage"]
    S -. "not validated end to end" .-> V["KubeVirt VirtualMachine"]
    C["kubeadm control plane"] --> W["Worker node<br/>containerd + KubeVirt"]
    W --> V
```

The lab ran on a Windows host using VMware Workstation. KubeVirt runs virtual machines managed through Kubernetes; it does not convert an existing guest operating system into a container.

## Project materials

- [Report in French — PDF](reports/rapport-migration-fr.pdf) · [Original Word document](reports/rapport-migration-fr.docx)
- [Migration workflow and technical corrections](docs/migration-workflow.md)
- [Evidence, scope, and rebuild checklist](docs/evidence-and-limitations.md)

The PDF was exported from the supplied Word document for easier browsing. The Word document remains the original source; page layout can differ between renderers.

<details>
<summary>Historical screenshot: Kubernetes node listing</summary>

![Report page containing the Kubernetes node listing and start of the KubeVirt installation section](assets/kubernetes-nodes-report.png)

Source: page 26 of the exported PDF. This is historical report evidence, not a new cluster validation.

</details>

## What I learned

I gained practical experience with Linux networking, containerd, kubeadm, nested virtualization, and Kubernetes resources. The storage step also made an important distinction clear: copying a virtual disk to a node is not enough to make it a bootable KubeVirt volume.

The next step would be to rebuild on a host with more memory, record compatible versions, import a converted disk through a supported storage workflow, and verify the guest's boot, network connectivity, and data integrity.

## Résumé en français

Ce stage portait sur la préparation d’une migration de VM depuis ESXi vers Kubernetes avec KubeVirt. J’ai mis en place le laboratoire Kubernetes et documenté les étapes de préparation et de transfert. La migration complète n’a pas pu être validée à cause des ressources limitées de mon environnement local. Ce dépôt conserve le rapport et explique les vérifications qui restent à effectuer.

**Author:** Mohammed Laasri.
