# AKS storage

Applications running in AKS clusters may need to store and retrieve data from a persistent file system store.
A number of different [storage classes](https://kubernetes.io/docs/concepts/storage/storage-classes/) exist and are available to be used in AKS, each with different advantages and constraints.

Kubernetes typically treats individual pods as ephemeral, disposable resources.
Applications have different approaches available to them for using and persisting data.
A _volume claim_ represents a way to store, retrieve, and persist data across pods and through the application lifecycle.

Traditional volume claims are created as Kubernetes resources backed by Azure storage.
Data volumes can use [Azure Disk](https://learn.microsoft.com/en-us/azure/virtual-machines/disks-types), [Azure Files](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-planning), or [Azure Blobs](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-overview).

Reference: <https://learn.microsoft.com/en-us/azure/aks/concepts-storage>

## Storage class choices

AKS clusters are deployed with a number of predefined [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/) for volume claims, each defining the type and tier of storage used.
The appropriate storage class to use is context-dependent and must be assessed per system.

**The choice of storage class used depends on the operational needs of the running workload.**
It is impossible to account for all different operational scenarios; the table below describes several common ones and what might be the most appropriate storage class to use.

| Scenario                                                         | Storage class | Rationale                                                           | Limitations                                                                          |
| ---------------------------------------------------------------- | ------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Static assets (image/video/etc.), write once, read many times    | Azure Blob    | Scalability, much lower storage cost than Azure File/Disk, scalable | Relatively low performance, not a "real" filesystem, no file locking guarantee       |
| NFS mount simultaneously writeable by >1 workload                | Azure File    | Required for multi-node access to NFS mount                         | Slightly more expensive than Azure Disk, no inherent user/group permissions          |
| File system only ever used by single workload (e.g. statefulset) | Azure Disk    | "Traditional" mounted volume                                        | No ability for simultaneous usage by multiple nodes, doesn't support _ReadWriteMany_ |
| Ephemeral/temporary working space                                | In-memory     | High performance, low latency, disposable, nil additional cost      | Space availability (workload memory limit)                                           |

## Azure Blob Storage

Azure Blob Storage (Blob stands for "Binary Large OBject") is a cloud-based object storage service, designed to store large amounts of unstructured data (somewhat equivalent of Amazon S3 storage). It is accessible via REST APIs and client libraries (SDKs). It has the advantages of being much cheaper than other storage classes and almost infinitely scalable, at the cost of not being suitable for mounted file operations.

It is ideal for bulk storage of static files (i.e. write once, read many). User-uploaded media (images, videos, etc.) or system outputs (CSV exports, etc.) are ideal candidates for this storage class.

Systems should interact with Blob storage via a software SDK as an object store. The Microsoft-supported Python package is [azure-storage-blob](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-python). In addition, Blob storage has excellent support for usage within Django via [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) package.
Example DBCA Django project making use of blob storage: <https://github.com/dbca-wa/prs>

## Azure Files

Use [Azure Files](https://learn.microsoft.com/en-us/azure/aks/azure-files-csi) to mount a Server Message Block (SMB) version 3.1.1 share or Network File System (NFS) version 4.1 share backed by an Azure storage account to pods. Azure Files let you share data across multiple nodes and pods. This storage class is mounted on cluster nodes as a file system, and is suitable for applications requiring fast local file operations shared between multiple pods at once.

**NOTE**: due to the lack of granular user and group Unix permissions for this filesystem (everything is effectively chmod 777), it is unsuitable for use with some applications that require more restricted or granular file permissions.

## Azure Disk

Use [Azure Disk](https://learn.microsoft.com/en-us/azure/aks/azure-disk-csi) to create a Kubernetes _DataDisk_ resource. This storage class is mounted on an individual cluster as a file system, and is suitable for applications requiring fast local file operations.

**NOTE**: because Azure Disk is mounted as _ReadWriteOnce_, these disks are only available to a single node at once. For storage volumes needing to be accessible by pods on multiple nodes simultaneously, use **Azure Files**.
