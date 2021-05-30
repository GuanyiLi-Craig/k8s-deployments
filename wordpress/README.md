# Wordpress

This is the first service I want to deploy on RPi K8S cluster. Based on official (wordpress deployment page)[https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/], I thought it would be straight forward but I was wrong. Because RPi require MySQL based on arm platform, so it is trivial to change MySQL image. Then the wordpres pod log shown failed to connect to mysql. So I found a workaround to make it work but by far, I have not been able to deploy Wordpress with MySQL without hardcoding.    

## Deploy

1. Deploy MySQL and Add Permission
	1. Deploy MySQL by `kubectl create -f mysql-deployment.yaml`
	2. Get MySQL pod IP by `kubectl get pods -o wide` 
	3. Access MySQL pod by `kubectl exec -it \<MySQL pod name\> /bin/bash`
	4. Add permission and wordpress database
```bash
mysql -p # input password: admin
mysql>use mysql;
mysql>CREATE USER 'root'@'<pod IP>' IDENTIFIED BY 'admin';
mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'<pod IP>' WITH GRANT OPTION;
mysql>CREATE DATABASE wordpress;
```
2. Deploy Wordpress
	1. Change WORDPRESS\_DB\_HOST value to MySQL pod IP
	2. Deploy wordpress by `kubectl create -f word-deployment.yaml`

## Checkout

Checkout pod logs and status to make sure everything is running. Then use port-forward to open wordpress remotely. 

```bash
kubectl port-forward --address 0.0.0.0 svc/wordpress 8081:80
```

Open a browser page and input \<master node ip\>:8081, it should show wordpress setup page. 

## Clean up

Run the following command to delete everything

```bash
kubectl delete -f wordpress-deployment.yaml
kubectl delete -f mysql-deployment.yaml
```
