
# k8s-taiga , kubernetes taiga deploy for GITLAB -CI and GITLAB regisrtry
This code build and push containers to your regstry.
It enables Google OAUTH2 login, or Gitlab OAUTH2, or public register with limited domains.

Next step - use Helm to deploy this to your K8S.

#make it in helm
helm install --dry-run --debug
 
 
A helm chart to deploy taiga with gitlab-ci.
Works in conjunction with the taiga-container-build
project (which provides the containers).

#params

Make your changes  -

/frontend/scripts/entrypoint.sh

/values.yaml

/.gitlab-ci.yml

## Usage

Copy values.yml.sample to values.yml. Fill
in the values (including getting your credentials
from your google [console](https://console.developers.google.com/apis/credentials)

## Persistent Storage
You can use ot /templates/storages.yaml  (some hardcode i think))

By default this uses the auto-provisioner to create three persistent
volumes (1 for postgresql, 1 for the git repo, 1 for backup).  This means
that if you delete the chart, you will delete the storage. If you
wish, you can manually create one or both volumes, and add them to the
values.yaml as

```
taiga_db_claim: pv-taiga-db-claim
taiga_volume_claim: pv-taiga-volume-claim
taiga_backup_claim: pv-taiga-backup-claim
```


And you may wish to create them as:

```
cat << EOF | kubectl apply -f -
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: retained
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
reclaimPolicy: Retain
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-taiga-volume-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: retained
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-taiga-backup-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: retained
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-taiga-db-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: retained
EOF
```



## License

This is all licensed under Apache 2.0
[license](https://www.apache.org/licenses/LICENSE-2.0).

[THX to Don Bowman ](https://github.com/donbowman/k8s-taiga)