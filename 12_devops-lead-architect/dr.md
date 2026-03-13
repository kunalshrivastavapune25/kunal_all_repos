graph TD
    subgraph Region A (Primary - us-east-1)
        EKS_A[EKS Cluster - Primary]
        APP_A(Application Pods)
        PV_A[Persistent Volumes - EBS/EFS]
        VELERO_A(Velero Server - in EKS_A)
        S3_VELERO_A[S3 Bucket - Velero Backups]
        DB_A[Database - RDS Multi-AZ]
        LB_A[Application Load Balancer]
    end

    subgraph Region B (Secondary - us-west-2 - Warm Standby)
        EKS_B[EKS Cluster - Standby]
        APP_B(Application Pods - Deployed post-DR)
        PV_B[Persistent Volumes - Replicated/Restored]
        S3_VELERO_B[S3 Bucket - Replicated Velero Backups]
        DB_B[Database - RDS Read Replica]
        LB_B[Application Load Balancer - Ready]
    end

    subgraph DR Orchestration
        R53[Route 53 Global DNS]
        IaC[CloudFormation/Terraform]
        SSM[AWS Systems Manager Automation]
    end

    APP_A -- Writes/Reads --> PV_A
    APP_A -- Writes/Reads --> DB_A
    LB_A -- Routes Traffic --> APP_A

    VELERO_A -- Backs up K8s Objects + CSI Snapshots --> S3_VELERO_A
    S3_VELERO_A -- S3 Cross-Region Replication (CRR) --> S3_VELERO_B
    PV_A -- EBS Cross-Region Snapshot Copy --> PV_B
    PV_A -- EFS Replication --> PV_B
    DB_A -- RDS Cross-Region Read Replica --> DB_B

    R53 -- Primary record targets --> LB_A
    R53 -- Failover record targets --> LB_B

    click R53 "DNS Failover"
    click VELERO_A "Velero Backup Process"
    click S3_VELERO_A "S3 CRR"
    click PV_A "EBS/EFS Replication"
    click IaC "Infra Provisioning (if Cold Standby)"
    click SSM "DR Runbook Automation"

    style EKS_A fill:#f9f,stroke:#333,stroke-width:2px
    style EKS_B fill:#ccf,stroke:#333,stroke-width:2px
    style S3_VELERO_A fill:#cfc,stroke:#333,stroke-width:1px
    style S3_VELERO_B fill:#cfc,stroke:#333,stroke-width:1px
    style PV_A fill:#fcc,stroke:#333,stroke-width:1px
    style PV_B fill:#ccf,stroke:#333,stroke-width:1px
    style DB_A fill:#fcc,stroke:#333,stroke-width:1px
    style DB_B fill:#ccf,stroke:#333,stroke-width:1px

    %% Failover Path
    S3_VELERO_B -- Velero Restore --> EKS_B
    PV_B -- Attaches/Mounts --> EKS_B
    DB_B -- Promoted to Primary --> EKS_B
    EKS_B -- Deploys --> APP_B
    APP_B -- Served by --> LB_B
