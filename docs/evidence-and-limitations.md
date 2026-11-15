# Evidence and limitations

## Source and scope

The original source is `reports/rapport-migration-fr.docx`, supplied as *Sujet de projet.docx*. The repository also includes a PDF export for reading.

- Chapter 2 describes a Windows host, VMware Workstation, a two-node Kubernetes cluster, and nested ESXi.
- Chapter 3 covers VM creation, Netplan, containerd, kubeadm, KubeVirt installation, source VM creation, export/transfer, and proposed storage manifests.
- The conclusion explicitly says the complete migration could not be validated in the local environment because resources were limited.

The report does not provide a reliable project date or a complete version inventory. Its abstract mentions two company names inconsistently, so this repository does not attribute the internship to either company without clarification.

## Artifacts and verification

The original VMs, VM disks, and working YAML files are no longer available. The screenshots embedded in the report provide historical context; no new cluster has been created for this repository. The technical notes were written during portfolio preparation and identify corrections needed for a future rebuild.

No claim is made of a booted migrated guest, live migration, production deployment, automated migration, high availability, or performance/cost improvements.

## Future validation record

For a rebuild, record host resources and nested virtualization settings; Kubernetes, containerd, CNI, KubeVirt, CDI, and storage versions; the source disk format/checksum; import completion; guest boot output; networking/service checks; and persistence after restart. None of these new validation results is currently available.
