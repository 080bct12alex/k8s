# Helm
- Helm is a package manager for Kubernetes — like how apt is for Ubuntu or npm is for Node.js.
- It helps  install, upgrade, and manage applications on a Kubernetes cluster easily.

**🧩 What Helm Does**

Helm packages Kubernetes manifests (YAML files) into a single unit called a Chart.

A Chart = a reusable template for a Kubernetes app  like   App installer package (.exe, .deb, .apk)

A Release = an instance of a Chart deployed on your cluster like An installed copy of the app

A Repository =  where charts are stored — just like Google Play Store (e.g : [ArtifactHub.io](https://artifacthub.io))




> Helm is like a “Kubernetes App Store + Installer + Version Manager”.
⚙️ Key Helm Commands



**🧠 Why Use Helm?**

✅ Simplifies deployment (no need for multiple YAML files)
✅ Makes updates and rollbacks easy
✅ Encourages reusability with templates
✅ Manages complex apps with dependencies (like databases, backend, frontend)

---
**🧠 Summary**
---
- Kubernetes needs many YAML files to deploy an app (Deployment, Service, Ingress, etc.).

- Helm bundles all those files into one chart.

  - configure, install, upgrade, or delete that app easily — just like using npm install or apt install.


  

  