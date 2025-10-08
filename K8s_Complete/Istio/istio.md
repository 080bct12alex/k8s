# Istio 

- Istio is a service mesh for Kubernetes (and other environments).
- It helps manage how microservices communicate — securely, reliably, and observably — without changing your app code.


**🔁 How It Works**

- Istio injects Envoy sidecars into each pod.

- All service-to-service communication flows through the proxies.

- The control plane (istiod) configures those proxies.

- You define rules in YAML — Istio applies them cluster-wide.

**🧪 Example: Simple Traffic Split**
```yml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: my-app
spec:
  hosts:
    - my-app
  http:
    - route:
        - destination:
            host: my-app
            subset: v1
          weight: 80
        - destination:
            host: my-app
            subset: v2
          weight: 20

```
- ➡️ 80% traffic goes to version v1, 20% to v2.

**Ingress VS Istio**

- Actually, Istio includes an Ingress Gateway —
so you can replace or extend the default Kubernetes Ingress with Istio’s more advanced Gateway + VirtualService combo.

| Analogy                                      | Explanation                                                                           |
| -------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Ingress = Gatekeeper**                     | Controls **who** and **how** requests enter your cluster.                             |
| **Istio = Traffic Manager + Security Guard** | Controls **all** traffic **inside and outside**, monitors, secures, and optimizes it. |



| Use Case                                                  | Use...      |
| --------------------------------------------------------- | ----------- |
| Just need to expose a web app to the internet             | **Ingress** |
| Want traffic splitting, retries, or observability         | **Istio**   |
| Need service-to-service encryption (mTLS)                 | **Istio**   |
| Running small app or static site                          | **Ingress** |
| Running microservices, A/B testing, zero-downtime deploys | **Istio**   |
