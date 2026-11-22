# Перевірка 6 ендпоінтів до роботи

PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/api/status
{"status":"ok","appMode":"dev","version":"1.0.0","timestamp":"2026-11-22T16:40:29.649Z"}
PS D:\Web_programming\lab10\k8s-demo> curl.exe -s "http://localhost:8080/api/items?filter=a"
[{"id":1,"name":"Item A"}]
PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/api/slow
{"status":"ok","delayMs":2000}
PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/api/error
{"status":"ok","message":"No error"}
PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/healthz
ok
PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/ready
{"status":"ready"}


# Перевірка застосунка у контейнері

PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/api/status                                      
{"status":"ok","appMode":"dev","version":"1.0.0","timestamp":"2026-11-22T16:46:42.278Z"}   
PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/healthz                
ok     

# Запуск Minikube та перевірка кластера

PS D:\Web_programming\lab10> minikube profile list
┌──────────┬────────┬────────────┬──────────────┬─────────┬────────┬───────┬────────────────┬────────────────────┐
│ PROFILE  │ DRIVER │  RUNTIME   │      IP      │ VERSION │ STATUS │ NODES │ ACTIVE PROFILE │ ACTIVE KUBECONTEXT │
├──────────┼────────┼────────────┼──────────────┼─────────┼────────┼───────┼────────────────┼────────────────────┤
│ minikube │ docker │ containerd │ 192.168.49.2 │ v1.37.0 │ OK     │ 1     │ *              │ *                  │
└──────────┴────────┴────────────┴──────────────┴─────────┴────────┴───────┴────────────────┴────────────────────┘
PS D:\Web_programming\lab10> kubectl config get-contexts
CURRENT   NAME       CLUSTER    AUTHINFO   NAMESPACE
*         minikube   minikube   minikube   default
PS D:\Web_programming\lab10> kubectl config use-context minikube
Switched to context "minikube".
PS D:\Web_programming\lab10> kubectl config current-context
minikube
PS D:\Web_programming\lab10> kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   28m   v1.37.0
PS D:\Web_programming\lab10> kubectl get pods -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-559f6c778d-gtj87           1/1     Running   0          49s
kube-system   etcd-minikube                      1/1     Running   0          56s
kube-system   kindnet-cmq5z                      1/1     Running   0          49s
kube-system   kube-apiserver-minikube            1/1     Running   0          57s
kube-system   kube-controller-manager-minikube   1/1     Running   0          56s
kube-system   kube-proxy-ctp9b                   1/1     Running   0          49s
kube-system   kube-scheduler-minikube            1/1     Running   0          56s
kube-system   storage-provisioner                1/1     Running   0          54s


# Застосування маніфестів Kubernetes

PS D:\Web_programming\lab10\k8s-demo> kubectl get ns k8s-demo
NAME       STATUS   AGE
k8s-demo   Active   6m23s
PS D:\Web_programming\lab10\k8s-demo> kubectl get configmap -n k8s-demo
NAME               DATA   AGE
k8s-demo-config    4      6m25s
kube-root-ca.crt   1      6m31s
PS D:\Web_programming\lab10\k8s-demo> kubectl get secret -n k8s-demo
NAME              TYPE     DATA   AGE
k8s-demo-secret   Opaque   1      6m24s
PS D:\Web_programming\lab10\k8s-demo> kubectl get deployment,rs,pods,svc -n k8s-demo
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/k8s-demo-api   2/2     2            2           24m

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/k8s-demo-api-5c45559886   0         0         0       24m
replicaset.apps/k8s-demo-api-69779f94f    2         2         2       10m

NAME                               READY   STATUS    RESTARTS   AGE
pod/k8s-demo-api-69779f94f-cgsg8   1/1     Running   0          10m
pod/k8s-demo-api-69779f94f-q4x6p   1/1     Running   0          10m

NAME                   TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/k8s-demo-api   ClusterIP   10.109.41.31   <none>        80/TCP    24m


# Доступ через Service і kubectl port-forward

PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/api/status
{"status":"ok","appMode":"prod","version":"1.0.0","timestamp":"2026-11-22T17:47:31.185Z"}
PS D:\Web_programming\lab10\k8s-demo> curl.exe -s http://localhost:8080/api/items
[{"id":1,"name":"Item A"},{"id":2,"name":"Item B"},{"id":3,"name":"Item C"}]