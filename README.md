# kubernetes-rpi-cluster-dashboard
This project describes how to get k8s dashboard running on your rpi cluster without login -- development use only

## Deploy arm version of Kubernetes dashboard

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard-arm.yaml
```

## Need to grant access so we don't have log in every time.  Don't do this in production or any important system.

### Create file and create ClusterRoleBinding

```
dashboard-admin.yaml

apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```

### Edit to skip login

```
kubectl edit deployment/kubernetes-dashboard --namespace=kube-system
```

```
containers:
      - args:
        - --auto-generate-certificates
        - --enable-skip-login            # Add this to put in a skip creds option when logging in.
        image: k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
```

## Allow access to the dashboard pod

```
# Using a proxy to access.  Need to launch this command from the master node to accept connections
# Can use a service below to remove this requirement.

kubectl proxy --address=192.168.1.10 --port 8001 --accept-hosts '.*'
```

# Verify

```
http://192.168.1.10:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
```

## Edit the dashboard service to allow access to the node without using kubectl proxy

```
kubectl edit service/kubernetes-dashboard --namespace=kube-system
```

```
spec:
  clusterIP: 10.102.204.112
  externalIPs:            # Add this with the ip on next line
  - 192.168.1.10
  ports:
  - port: 443
    protocol: TCP
    targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard
  sessionAffinity: None
  type: LoadBalancer        # Change to LoadBalancer
```

### Dashboard can now be accessed using

```
https://192.168.1.10
```

## Resources

### Main dashboard project github

https://github.com/kubernetes/dashboard
https://github.com/kubernetes/dashboard/wiki/Installation

### Useful information used to get it running

https://stackoverflow.com/questions/46664104/how-to-sign-in-kubernetes-dashboard
https://github.com/kubernetes/dashboard/wiki/Access-control#admin-privileges
https://www.thehumblelab.com/deploying-kubernetes-dashboard-in-the-lab/
https://github.com/kubernetes/dashboard/wiki/Access-control
https://stackoverflow.com/questions/36270602/how-to-access-kubernetes-ui-via-browser
