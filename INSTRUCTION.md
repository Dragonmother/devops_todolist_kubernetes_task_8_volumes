# Kubernetes Volumes — Instructions

## 1. Deploy the application

Run the following command from the project root:

```bash
bash ./bootstrap.sh
```

This script applies the namespace, ConfigMap, Secret, PersistentVolume, PersistentVolumeClaim, and Deployment.

## 2. Validate that the application is running

Check the Deployment rollout status:

```bash
kubectl rollout status deployment/todoapp -n todoapp
```

Check the Pods:

```bash
kubectl get pods -n todoapp
```

The application Pod should have status `Running` and be ready.

## 3. Validate the PersistentVolume and PersistentVolumeClaim

```bash
kubectl get pv todoapp-pv
kubectl get pvc todoapp-pvc -n todoapp
```

Both resources should have status `Bound`.

Verify that the data directory is mounted inside the container:

```bash
kubectl exec -n todoapp deployment/todoapp -- ls -la /app/data
```

## 4. Validate ConfigMap files

List the files mounted from the ConfigMap:

```bash
kubectl exec -n todoapp deployment/todoapp -- ls -la /app/configs
```

Check that the `PYTHONUNBUFFERED` file exists and contains the expected value:

```bash
kubectl exec -n todoapp deployment/todoapp -- cat /app/configs/PYTHONUNBUFFERED
```

Expected output:

```text
1
```

Verify that the ConfigMap volume is mounted read-only:

```bash
kubectl get deployment todoapp -n todoapp -o yaml
```

Check that the `app-config-volume` mount at `/app/configs` has `readOnly: true`.

## 5. Validate Secret files

List the files mounted from the Secret:

```bash
kubectl exec -n todoapp deployment/todoapp -- ls -la /app/secrets
```

Verify that the `SECRET_KEY` file exists without displaying its value:

```bash
kubectl exec -n todoapp deployment/todoapp -- sh -c 'test -f /app/secrets/SECRET_KEY && echo "Secret file OK"'
```

Verify that the Secret volume is mounted read-only:

```bash
kubectl get deployment todoapp -n todoapp -o yaml
```

Check that the `app-secret-volume` mount at `/app/secrets` has `readOnly: true`.

**Security note:** Do not print or commit the actual Secret value.
