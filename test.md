To render the **Mermaid HLD diagram** on **GitHub**, follow these steps:

---

## ✅ Step-by-Step: How to Use Mermaid Diagrams on GitHub

### ✅ 1. **Create a Markdown File**

Save the content in a `.md` file (e.g., `cloud_hld.md`):

````markdown
# High-Level Design Diagram for Cloud Architecture

```mermaid
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

    A & B --> C
    C --> D
    D --> E & F
    F --> G
    F --> H & I
    E & F & G --> K
    K --> J
    H & I --> J

    L -.-> E & F & G & H & I & K
    M -.-> E & F & G & H & I & K
    N -.-> E & F & G & H & I & K
    O -.-> E & F & G & H & I & K
````

``````

> ⚠️ Important: Backticks used above to wrap the Mermaid code block are 3 backticks: ````` before and after the `mermaid` block (replace ` with ```).

---

### ✅ 2. **Commit the `.md` File to GitHub**
- Push the file to a GitHub repository.
- Example repo structure:
``````

my-repo/
└── cloud\_hld.md

```

---

### ✅ 3. **View in GitHub**
- Go to your `.md` file in the repository.
- GitHub **automatically renders Mermaid diagrams** inside Markdown preview (as of early 2023).

---

### 🧪 Tip: Test Before Upload
Use [https://mermaid.live](https://mermaid.live) to test and preview your diagram before committing it to GitHub.

---

Would you like me to generate a `.md` file for download containing this Mermaid code?
```
