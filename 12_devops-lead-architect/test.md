# DevOps & Microservices Migration – Q&A

This README captures my current role, responsibilities, and best practices in a clear Q&A format. Each answer is enclosed in a GitHub alert box for visual distinction, with full formatting support.

---

## ❓ 1. What is your professional background, current role, and core responsibilities?

> [!IMPORTANT]
> ### 🔹 Key Domains & Team Structure
> - **Domains:** Billing, Customers, GSM, Locations, Accounts, Provisioning, and more.
> - **Team Model:** Multiple dedicated **development** and **DevOps** teams per microservice domain.
>
> ---
>
> ### 🚀 Current Role
> - **Dev Lead** for the migration from a legacy monolith → **modern microservices target architecture**.
> - Active member of the **DevOps team** responsible for **Products**, **Customers**, **Accounts**, and related microservices.
>
> ---
>
> ### 🛠️ Core Responsibilities
> - Implementing and improving **DevOps practices & processes**.
> - **Containerization** (<u>Docker</u>)
> - **Orchestration** (<u>Kubernetes – EKS</u>)
> - **CI/CD + GitOps** (<u>GitHub Actions + ArgoCD</u>)
> - **Infrastructure as Code** (<u>Terraform, Ansible</u>)
>
> ---
>
> ### ⚙️ Day‑to‑Day Focus (MLOps & Platform)
> - **MLOps:** Build training data pipelines, **observability**, CI/CD, **model registry**, IaC, cost optimization.
> - Cross‑cutting: Enable teams with self‑service infrastructure, maintain reliability, and drive automation.

---

## ❓ 2. What best practices do you follow, and what does your daily/weekly work involve?

> [!TIP]
> ### 🤝 Agile Collaboration
> - Closely collaborate with multiple development teams using **Agile** methodology.
> - Attend **sprint planning**, **backlog refinement**, and **daily stand‑ups**.
> - Understand weekly sprints + monthly roadmaps → this helps me *anticipate* upcoming infrastructure & deployment needs.
>
> ---
>
> ### 📦 Common Requests I Handle from Dev Teams
> - **Provision new environments / clusters** (IaC‑first approach).
> - Create **modular, reusable EKS clusters** via Terraform.
> - **Kubernetes manifests & deployment troubleshooting**.
> - **Containerization support** (especially for junior developers) – writing / reviewing Dockerfiles.
> - **Git branching strategy** enforcement & repository hygiene.
> - Setting up GitHub webhooks, repository settings, <u>branch protection rules</u>.
>
> ---
>
> ### 📚 Documentation & Knowledge Sharing
> - Create & maintain **best‑practice guides** (e.g., *"How to containerize a microservice"*).
> - Share documentation across teams – especially those without dedicated DevOps engineers.
>
> ---
>
> ### 🔁 Weekly Rhythm
> * Daily stand‑ups, pair programming on complex infrastructure tasks  
> * Proactive review of upcoming features to align cluster/resource needs  
> * Continuous improvement of CI/CD pipelines and <u>GitOps</u> workflows (ArgoCD + GitHub Actions)  
> * **Infrastructure as Code** reviews (Terraform modules, Ansible roles)  

---

**✨ Formatting note:** The boxes above contain examples of **bold**, *italic*, <u>underline</u>, headings, and horizontal rules (`---`) – all fully supported in GitHub’s alert blocks.
