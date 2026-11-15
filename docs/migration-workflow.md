# Migration workflow

This explains the report's approach and the work needed to complete it. It is a migration plan, not a tested runbook.

## 1. Inventory the source VM

Record the guest OS, CPU architecture, BIOS/UEFI mode, disk controller, disk chain, network configuration, and services. Check whether snapshots or multiple VMDK extents need consolidation. Plan a maintenance window and retain the source disk until the imported guest has been validated.

The report uses an Ubuntu Server source VM on a nested ESXi instance. It does not establish compatibility for arbitrary VMware workloads.

## 2. Build a compatible Kubernetes and KubeVirt environment

The original lab used two Ubuntu Server 22.04 VMs, kubeadm, and containerd. A rebuild also needs:

- Enough host and guest memory for Kubernetes, KubeVirt, ESXi, and the source guest.
- Hardware virtualization exposed to the worker node; check `/dev/kvm` and the applicable nested virtualization settings.
- Compatible Kubernetes, containerd, KubeVirt, and guest-tool versions.
- Matching kubelet/containerd cgroup configuration and a functioning CNI plugin.
- A persistent storage provider suitable for KubeVirt VM disks.

The report uses the old `apt.kubernetes.io` repository. Follow the current [kubeadm installation guide](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/) instead of copying those package commands.

Select a KubeVirt release compatible with that cluster. The operator and the custom resource are separate manifests; the report repeats the operator command once. See the [KubeVirt installation guide](https://kubevirt.io/user-guide/cluster_admin/installation/).

Useful inspection commands on a rebuilt cluster:

```bash
kubectl get nodes -o wide
kubectl get pods -A
kubectl -n kubevirt get kubevirt
kubectl get storageclass
```

These inspect prerequisites; they do not prove a migration succeeded.

## 3. Export, inspect, and convert the disk

Export a powered-off or otherwise consistent source VM using the supported VMware workflow. Preserve the original export. Inspect the disk with `qemu-img info`, and convert a copy to a format accepted by the selected import workflow, such as QCOW2 or raw. Account for virtual disk size, not just the size of a compressed export.

No disk is included in this repository and no conversion result is claimed.

## 4. Import into persistent storage

The report proposes a `hostPath` PV pointing directly to `/home/master/ubuntu.vmdk`. This is not a complete KubeVirt import procedure:

- The worker running the VM may be a different node from the one holding the file.
- A filesystem volume normally needs a correctly prepared guest disk, not an arbitrary VMDK mounted as the PV root.
- Storage permissions, capacity, volume mode, access modes, and scheduling still need to match the VM.
- A manually declared `storageClassName` does not create a storage provisioner.

A future rebuild should select a supported disk import path, such as [Containerized Data Importer (CDI)](https://kubevirt.io/user-guide/storage/containerized_data_importer/). Create the destination volume/DataVolume using the chosen storage class, upload or import the prepared disk, and wait for import completion before starting the VM. Follow the selected versions' documentation for accepted formats and upload commands.

The original hostPath manifests are not published here as ready-to-run examples because they would imply a working storage path that was never validated.

## 5. Define and start the guest

Create a KubeVirt `VirtualMachine` referencing the imported volume. Match guest architecture, firmware, disk bus, CPU/memory, and network configuration. Use the selected release's documented run strategy and `virtctl` version.

Verify separately:

1. The PVC is bound and any DataVolume import has completed.
2. The VirtualMachineInstance is scheduled to a suitable worker.
3. The serial console shows a successful guest boot.
4. Filesystems mount correctly and expected data is present.
5. Guest networking and required services work.
6. A controlled stop/start preserves the guest data.

Keep the source available until these checks pass. Reaching `Running` at the Kubernetes level alone does not prove guest or application health.
