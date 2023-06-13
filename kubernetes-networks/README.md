# Проверки в POD

*readnessProbe* - проверка после успешного прохождения которой, Pod готов принимать и обслуживать трафик. Состояние определяется в describe: Conditions (Ready: True)

*livenessProbe* - предназначена для определения условия когда требуется перезагрузка пода.

В случае проверки процесса - это не имеет смысла, ввиду того что процесс может быть активным, но не принимать соединения.
Имеет смысл - если процесс может "упасть".

# Deployment

Состояние Pod оказывает влияние на Deployment: Conditions - строчка Available. Корректная работа Deployment: Status=True для Available и Progressing.

Используя разные значения maxSurge, maxUnavailable можно релизовывать стратегию Deployment: Rolling Update (постепенная замена старых, постепенное удаление новых). Recreate (все поды удаляются, новые создаются)

# Service

ClusterIP обеспечивает подключение к конечным Pod сервиса чрез одну "точку входа", однако при этом каждый под будет обслуживать запросы случайным образом (простейшая балансировка).

Под капотом: iptables или ipvs.

# LoadBalancer и Ingress

MetalLB - обеспечивает выделение адресов из пула для service, к которым можно получить доступ "снаружи", с небольшой доработкок: нужно добавить подсеть на сетевой интерфейс.

#  DNS через MetalLB
minikube start --addons=metallb --addons=ingress --extra-config kube-proxy.mode=ipvs --extra-config kube-proxy.ipvs.strictARP=true --driver=hyperkit --cpus 2 --memory 4096
cd coredns
kubectl apply -f ../metallb-config.yaml
minikube ip
<MK_IP>
sudo route add 172.17.252.0/24 <MK_IP>
kubectl apply -f dns-lb.yaml
dig @172.17.252.11 ns.dns.cluster.local

# Ingress для Dashboard
`minikube start --addons=metallb --addons=ingress --extra-config kube-proxy.mode=ipvs --extra-config kube-proxy.ipvs.strictARP=true --driver=hyperkit --cpus 2 --memory 4096`

Применяем манифест для стандартного dashboard
`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml`

Применяем манифест для ингресс
`kubectl apply -f dashboard-ingress.yaml`

Применяем манифесты для управления аккаунтом и получения токена
`kubectl apply -f service-account.yaml`

`kubectl apply -f binding.yaml`

`kubectl -n kubernetes-dashboard create token admin-user`

Получаем IP-адрес для доступа
`kubectl -n kubernetes-dashboard get ingress | grep "dashboard-ingress" | awk -F' ' '{ print $4}'` 

**Для проверки открыть ссылку https://<IP-FROM-LAST-COMMAND>/dashboard/
Использовать Token - результат вывода команды  create token...**

# CANARY

стартуем minikube
`minikube start --addons=metallb --addons=ingress --extra-config kube-proxy.mode=ipvs --extra-config kube-proxy.ipvs.strictARP=true --driver=hyperkit --cpus 2 --memory 4096`

Применяем манифест для деплоя v 1
`kubectl apply -f app-v1.yaml`
Применяем манифест ingress для 1 версии
`kubectl apply -f canary-ingress-v1.yaml`
Получаем адрес, по которому доступен ingress 
`kubectl get ingress | grep "my-app-v1" | awk -F' ' '{ print $4}'`
Смотрим статус pod. Ждем Running 
`kubectl get pods`
Получаем
`curl http://адрес ingress`

Применяем манифест для деплоя v 2
`kubectl apply -f app-v2.yaml`
Canary ingress  
`kubectl apply -f canary-ingress.yaml`
Получаем адрес, по которому доступен ingress 
`kubectl get ingress | grep "canary" | awk -F' ' '{ print $4}'`
Смотрим статус pod. Ждем Running 
`kubectl get pods`
Получаем
`curl http://адрес ingress`

