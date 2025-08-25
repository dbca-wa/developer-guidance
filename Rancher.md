# Rancher Deployment Setup Guide

This guide outlines the step-by-step process for setting up and deploying applications using DBCA's Rancher infrastructure.

## 1. Getting Access

### Prerequisites

-   **GitHub Organisation Membership**: Join the DBCA GitHub organisation at https://github.com/dbca-wa
    -   Contact your supervisor for assistance with this step
-   **Rancher Permissions**: Submit a ticket in Freshservice for CRUD permissions

### Rancher Instances

-   **Test Environment**: https://rancher-uat.dbca.wa.gov.au
-   **Production Environment**: https://rancher.dbca.wa.gov.au

### Permission Request Template

Submit a ticket at https://dbca.freshservice.com/support/tickets/new with the following details:

```
Subject: BCS Rancher Test and Prod Permissions
Division: BCS
System ID: Security / N/A
Project ID: [Leave blank]

Description:

Hi there,

I am a part of the [Your team] development team and require access to
Rancher test and Rancher Prod to create, read, update and delete deployments.
Could you please grant the permissions so I may work on deploying my apps?

You may reach out to my line manager ([Your line manager]) for more information.

Kind regards,
[Your name]
```

## 2. Creating a Namespace

1. Navigate to **Cluster → Projects/Namespaces**
2. Click the **Create Namespace** button
3. Configure the namespace:
    - **Name**: Provide a unique name
    - **Description**: Optional description
    - **Container Resource Limit**: Leave blank
    - **Pod Security Admission**: Leave blank
4. Set Labels & Annotations:
    - **Key**: `kubernetes.io/metadata.name`
    - **Value**: The app namespace name
5. Click **Save**

## 3. Creating a Deployment

1. Navigate to **Workloads**
2. Click **Create** (top right)
3. Select **Deployment**
4. Configure basic settings:
    - **Name**: Enter a unique deployment name
    - **Container Name**: Set a reference name
    - **Container Image**: Add Docker image link (e.g., `ghcr.io/dbca-wa/science-projects-service:3.4.4`)
5. For simple static sites, click **Create**

### Advanced Configuration (Optional)

-   **Additional Containers**: Click **Add Container** and repeat the process
-   **Init Containers**: For containers that need to run before others, set container type to "Init Container" instead of "Standard Container"
-   **Storage**: Can be added later (see Storage section)

## 4. Creating a Service

1. Navigate to **Service Discovery → Services**
2. Click **Create** (top right)
3. Select **Cluster IP**
4. Configure service:
    - **Namespace**: Ensure correct namespace is selected
    - **Name**: Enter unique name (e.g., `plastids-cluster-ip`)
5. Configure Service Ports:
    - **Port Name**: (e.g., `frontend`)
    - **Port**: The listening port (e.g., `8080`)
    - **Protocol**: TCP
    - **Target Port**: Service target port (e.g., `8080`)
    - Add additional ports if required (e.g., `3000`, `80`)
6. Configure Selectors:
    - **app**: Set to the application name
    - **status**: Set to `uat` or `prod`
7. Click **Create**

## 5. Creating Ingress Rules

1. Navigate to **Service Discovery → Ingresses**
2. Click **Create** (top right)
3. Configure ingress:
    - **Namespace**: Ensure correct namespace is selected
    - **Name**: Enter unique name (e.g., `plastids-ingress`)
4. Configure routing:
    - **Request Host**: Domain where webapp will be accessed (e.g., `pilbseq-uat.dbca.wa.gov.au`)
    - **Path**: Select **Prefix**
    - **Route**: Set to `/`
    - **Target Service**: Select the service created in step 4
    - **Port**: Use the port from the previous step
5. Additional paths (optional): Click **Add Path** and repeat
6. Additional rules (optional): Click **Add Rule** for different request hosts
7. Click **Create**

## 6. Creating Storage (Advanced - Optional)

> **Note**: Storage is not always required. Only follow these steps for applications that need persistent storage.

### Creating Persistent Volume Claims

1. Navigate to **Storage → PersistentVolumeClaims**
2. Click **Create** (top right)
3. Configure storage:
    - **Namespace**: Ensure correct namespace is selected
    - **Name**: Enter unique name (e.g., `plastids-media-test`)
    - **Request Storage**: Set to `8` (GB)
4. Click **Create**

### Attaching Storage to Deployment

1. Navigate to **Workloads → Deployments → [Your App]**
2. Click the **vertical ellipsis** (⋮) in the top right
3. Select **Edit Config**
4. Go to the **Storage** section
5. Set mount point (e.g., `/usr/src/app/backend/files`)
6. Click **Save**

---
