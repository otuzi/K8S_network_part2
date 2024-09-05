# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 2»

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Инструкция](https://microk8s.io/docs/getting-started) по установке MicroK8S.
2. [Описание](https://kubernetes.io/docs/concepts/services-networking/service/) Service.
3. [Описание](https://kubernetes.io/docs/concepts/services-networking/ingress/) Ingress.
4. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.

------

### Задание 1. Создать Deployment приложений backend и frontend

1. Создать Deployment приложения _frontend_ из образа nginx с количеством реплик 3 шт.
2. Создать Deployment приложения _backend_ из образа multitool. 
3. Добавить Service, которые обеспечат доступ к обоим приложениям внутри кластера. 
4. Продемонстрировать, что приложения видят друг друга с помощью Service.
5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

### Ответ 

Deployment frontend: https://github.com/otuzi/K8S_network_part2/blob/main/deployment_frontend.yaml
Deployment backend: https://github.com/otuzi/K8S_network_part2/blob/main/deployment_backend.yaml

Service: https://github.com/otuzi/K8S_network_part2/blob/main/service_bff.yaml

вывод из консоли VM:
```
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ vim deployment_backend.yaml
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ vim deployment_frontend.yaml
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ vim service_bff.yaml
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl apply -f deployment_frontend.yaml
deployment.apps/frontend-deployment created
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl apply -f deployment_backend.yaml 
deployment.apps/backend-deployment created
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get pod
NAME                                  READY   STATUS    RESTARTS   AGE
backend-deployment-78dd5cd7c6-qz2wv   1/1     Running   0          6s
backend-deployment-78dd5cd7c6-z57tg   1/1     Running   0          6s
backend-deployment-78dd5cd7c6-zfnz6   1/1     Running   0          6s
frontend-deployment-f7cbdf744-5xkcx   1/1     Running   0          12s
frontend-deployment-f7cbdf744-lp8c5   1/1     Running   0          12s
frontend-deployment-f7cbdf744-xcwv8   1/1     Running   0          12s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get pod
NAME                                  READY   STATUS    RESTARTS   AGE
backend-deployment-78dd5cd7c6-qz2wv   1/1     Running   0          9s
backend-deployment-78dd5cd7c6-z57tg   1/1     Running   0          9s
backend-deployment-78dd5cd7c6-zfnz6   1/1     Running   0          9s
frontend-deployment-f7cbdf744-5xkcx   1/1     Running   0          15s
frontend-deployment-f7cbdf744-lp8c5   1/1     Running   0          15s
frontend-deployment-f7cbdf744-xcwv8   1/1     Running   0          15s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl apply -f service_bff.yaml 
service/common-service created
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get pod
NAME                                  READY   STATUS    RESTARTS   AGE
backend-deployment-78dd5cd7c6-qz2wv   1/1     Running   0          27s
backend-deployment-78dd5cd7c6-z57tg   1/1     Running   0          27s
backend-deployment-78dd5cd7c6-zfnz6   1/1     Running   0          27s
frontend-deployment-f7cbdf744-5xkcx   1/1     Running   0          33s
frontend-deployment-f7cbdf744-lp8c5   1/1     Running   0          33s
frontend-deployment-f7cbdf744-xcwv8   1/1     Running   0          33s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get svc
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)           AGE
common-service   ClusterIP   10.152.183.196   <none>        80/TCP,8080/TCP   7s
kubernetes       ClusterIP   10.152.183.1     <none>        443/TCP           75m
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl run mymultitool --image=praqma/network-multitool:latest -i --tty --rm -- sh
If you don't see a command prompt, try pressing enter.
/ # 
/ # curl common-service:80 -I
HTTP/1.1 200 OK
Server: nginx/1.27.1
Date: Thu, 05 Sep 2024 20:39:25 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Mon, 12 Aug 2024 14:21:01 GMT
Connection: keep-alive
ETag: "66ba1a4d-267"
Accept-Ranges: bytes

/ # 
```

------

### Задание 2. Создать Ingress и обеспечить доступ к приложениям снаружи кластера

1. Включить Ingress-controller в MicroK8S.
2. Создать Ingress, обеспечивающий доступ снаружи по IP-адресу кластера MicroK8S так, чтобы при запросе только по адресу открывался _frontend_ а при добавлении /api - _backend_.
3. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
4. Предоставить манифесты и скриншоты или вывод команды п.2.

### Ответ

ingress: https://github.com/otuzi/K8S_network_part2/blob/main/ingress.yaml

![nginx](./pictures/Screenshot%202024-09-06%20at%2000.20.55.png "frontend")

![api](./pictures/Screenshot%202024-09-06%20at%2000.20.39.png "backend")
------
