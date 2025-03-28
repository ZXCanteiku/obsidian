## Kubernetes (Istio), OpenShift, Docker
aliases: 
	- ""

---

### Kubernetes (Istio), OpenShift, Docker

- [[Архитектура Kubernetes]]
- [[Архитектура Istio]]
### **Kubernetes vs Istio: в чём разница?**

| **Функция**            | **Kubernetes**         | **Istio**                                      |
| ---------------------- | ---------------------- | ---------------------------------------------- |
| Балансировка           | Kube-proxy (L4)        | Envoy Proxy (L7, продвинутый)                  |
| Service Discovery      | CoreDNS, ClusterIP     | Envoy Proxy                                    |
| Безопасность           | RBAC, NetworkPolicy    | mTLS, JWT, Zero-trust Security                 |
| Observability          | Basic Logs             | Tracing, Prometheus, Kiali                     |
| Развертывание          | Rolling Update, HPA    | Canary, A/B-тесты, Fault Injection             |
| Гибкость маршрутизации | Ограниченная (Service) | Полный контроль трафика (Istio VirtualService) |



---
Tags:
Author: [[]]
Related: [[]]
URL: -- 