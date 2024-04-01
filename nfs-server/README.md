## Create NFS Server in GKE

Reference: <https://medium.com/%E8%BC%95%E9%AC%86%E5%B0%8F%E5%93%81-pks%E8%88%87k8s%E7%9A%84%E9%BB%9E%E6%BB%B4/%E5%9C%A8gke%E4%B8%8A%E4%BD%BF%E7%94%A8readwrite-many%E7%9A%84disk-9945ff67a4d>

Create a ReadWrite Many Disk on GKE

### Using NFS Server for other services

- Create a PV

```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: {{ .Values.nfs.name }}
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: {{ .Values.nfs.name }}
  nfs:
    path: {{ .Values.nfs.path }}
    server: {{ .Values.nfs.host }}
```

- Create a PVC

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: {{ include "your-app.fullname" . }}-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 2Gi
  storageClassName: {{ .Values.nfs.name }}

```

- Values.yaml

```
nfs:
  name: $YOUR_PV_NAME
  host: nfs-server.default.svc.cluster.local
  path: $YOUR_PATH
```

- Deployment.yaml

```
conatiners:
    ...
    volumeMounts:
        - name: data-persistent-storage
        mountPath: $APP_MOUNT_PATH
    ...
volumes:
    - name: data-persistent-storage
      persistentVolumeClaim:
      claimName: $YOUR-APP_PVC
```
