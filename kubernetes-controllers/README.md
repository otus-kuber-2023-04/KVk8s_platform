ReplicaSet поддерживает постоянным число подов указанных в манифесте. При примененни нового манифеста число реплик не изменилось, поэтому новая версия образа не приминилась. Она применяется только тогда, когда под будет удален, число реплик уменьшиться - создастя новый под:

kubectl get pods -l app=frontend -o=jsonpath='{.items[0:3].spec.containers[0].image}'
kk74/hipster_shop_frontend:0.0.1 kk74/hipster_shop_frontend:0.0.1 kk74/hipster_shop_frontend:0.0.1⏎

kubectl get pods       (multinode-demo/default) (KVk8s_platform/kubernetes-controllers) 18:18:42
NAME             READY   STATUS    RESTARTS   AGE
frontend-nzvk7   1/1     Running   0          85s
frontend-sdtq4   1/1     Running   0          85s
frontend-wzm4x   1/1     Running   0          85s

kubectl delete pod frontend-wzm4x
pod "frontend-wzm4x" deleted

kubectl get pods -l app=frontend -o=jsonpath='{.items[0:3].spec.containers[0].image}'
kk74/hipster_shop_frontend:0.0.2 kk74/hipster_shop_frontend:0.0.1 kk74/hipster_shop_frontend:0.0.1⏎

Видно, что после удаления одного из подов - версия изменилась.



