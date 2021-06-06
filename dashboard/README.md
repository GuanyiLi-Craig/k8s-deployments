# K8S Dashboard

## Get latest K8S dashboard and deploy

```bash
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml
```

## Create service account and role

In `admin-service-user.yaml`, enter below values:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

In `admin-user-role.yaml`, enter below values:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Apply changes to deploy them to K3S cluster

```bash
sudo k3s kubectl create -f admin-service-user.yaml -f admin-user-role.yaml
```

## Expose service as NodePort to access from remote

Change `type: ClusterIP` to `type:NodePort` in `kubernetes-dashboard` by

```bash
sudo k3s kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
```

Get dashboard service port

```bash
sudo k3s kubectl get svc kubernetes-dashboard -n kubernetes-dashboard

>NAME                   TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
>kubernetes-dashboard   NodePort   10.43.244.142   <none>        443:30246/TCP   9m18s
```
## Get token and access dashboard

Get token by

```bash
sudo k3s kubectl -n kubernetes-dashboard describe secret admin-user-token
```

Then access the dashboard from link: https://\<master-node-ip\>:30246



