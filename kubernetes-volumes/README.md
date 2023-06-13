## Minio

Применен манифест, в результате получен работающий statefulset. 

## *

Создаем секреты
`kubectl create secret generic minio-secret --from-literal='access_key=minio' --from-literal='secret_key=minio123'`

Применяем "подкрученный" манифест
`kubectl apply -f minio-statefulset.yaml`

убеждаемся, что все нормально
`kubectl get statefulsets`
