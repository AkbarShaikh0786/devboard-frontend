# Kubernetes Learning — Simple Pod Deployment

A hands-on exercise covering namespace creation, pod deployment, and port-forwarding with `kubectl`.

## What this covers

- Creating a Kubernetes `Namespace`
- Deploying a single `Pod` into a custom namespace
- Verifying pod status with `kubectl get pods`
- Accessing the pod locally via `kubectl port-forward`

## Files

### `namespace.yml`

```yaml
kind: Namespace
apiVersion: v1
metadata:
  name: devboard-ns
```

### `pod.yml`

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: devboard-frontend
  namespace: devboard-ns

spec:
  containers:
  - name: devboard-frontend
    image: akbarshaikh0786/devboard-frontend
    ports:
    - containerPort: 4173
```

> Note: `pod.yml` references the `devboard-ns` namespace, which needs to exist before applying this manifest (create it the same way as `namespace.yml`, just with `name: devboard-ns`).

## Commands used

```bash
# Apply the pod manifest
kubectl apply -f pod.yml

# Check pod status in the devboard-ns namespace
kubectl get pods -n devboard-ns

# Forward local port 8082 to the pod's container port 4173
kubectl port-forward pod/devboard-frontend -n devboard-ns 8082:4173 --address 0.0.0.0

# List all namespaces
kubectl get ns
```

## Result

```
NAME                READY   STATUS    RESTARTS   AGE
devboard-frontend   1/1     Running   0          17s
```

The pod ran successfully, and the frontend (served on port 4173 inside the container) was reachable locally on `http://<host>:8082` via port-forwarding.

## Next steps / ideas to extend this

- Expose the pod internally with a `Service` instead of relying on `port-forward`
- Add an `Ingress` for external access
- Convert the bare `Pod` into a `Deployment` for restarts/scaling
- Add resource requests/limits to the container spec
