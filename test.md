graph TD

    subgraph "Users & Clients"
        A[Users on Web/Mobile GUIs]
        B[External Systems/APIs]
    end

    subgraph "Security & Entry Point"
        C[Firewall / WAF]
        D[API Gateway]
    end

    subgraph "Application Layer (PaaS & SaaS)"
        E[Frontend Dashboards & Client Interfaces]
        F[Backend Servers / Application Logic]
        G[Middleware for Real-time Management]
    end

    subgraph "Data Layer"
        H[Databases - SQL/NoSQL]
        I[Object Storage - Blobs/Files]
    end

    subgraph "Core Infrastructure (IaaS)"
        J[Virtual Network]
        K[Compute - Virtual Machines / Containers]
    end

    subgraph "Cross-Cutting Services"
        L[Identity & Access Management - IAM]
        M[Monitoring & Logging]
        N[Encryption Services - Key Vault]
        O[Redundancy & Backup]
    end

    %% Flow Connections
    A & B --> C
    C --> D
    D --> E & F
    F --> G
    F --> H & I
    E & F & G --> K
    K --> J
    H & I --> J

    %% Cross-Cutting Impact
    L -.-> E & F & G & H & I & K
    M -.-> E & F & G & H & I & K
    N -.-> E & F & G & H & I & K
    O -.-> E & F & G & H & I & K

    %% Optional: Legend subgraph
    subgraph Legend [Legend]
        direction LR
        L_IAM((IAM))
        M_MON((Monitoring))
        N_ENC((Encryption))
        O_BCK((Backup))
    end
