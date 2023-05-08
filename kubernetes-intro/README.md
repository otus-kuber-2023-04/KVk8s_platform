1. Установлен и запущен minikube, kubectl, k9s
2. Kubernetes грантирует синхронизацию жалемого и текущего состояния. Благодаря мониторингу внтури кластера происходит восстановление работоспособности компонентов. Однако, реализация самого механизма в может быть разной. 

"силами" кластера (задействуется планировщик и kubelet):
kubectl describe pod coredns-787d4945fb-mvrqr -n kube-system | grep Control
Controlled By:  ReplicaSet/coredns-787d4945fb

"силами" ноды (задействуется kubelet):
kubectl describe pod kube-apiserver-minikube -n kube-system | grep Control
Controlled By:  Node/minikube

3. Подготовлен Dockerfile, собран обра и размещен в Docker Hub

4. Подготовлен манифест web-pod.yaml, запущен в minikube

5. Hipster-shop - не запускался из-за переменных окружения
