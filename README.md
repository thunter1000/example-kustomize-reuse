# Example Kustomize.

Quick example on re-usability with Kustomize. Preventing the need to re-define a similar CronJob.


## Resulting YAML.

```yaml
apiVersion: v1
data:
  config: example
kind: ConfigMap
metadata:
  name: example-config-1
---
apiVersion: v1
data:
  config: example
kind: ConfigMap
metadata:
  name: example-config-2
---
apiVersion: batch/v1
kind: CronJob
metadata:
  name: example-cron-job-1
spec:
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - env:
              valueFrom:
                configMapKeyRef:
                  key: config
                  name: example-config-1
            image: alpine
            imagePullPolicy: IfNotPresent
            name: example-cron-job
          restartPolicy: OnFailure
  schedule: '* * * * *'
---
apiVersion: batch/v1
kind: CronJob
metadata:
  name: example-cron-job-2
spec:
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - env:
              valueFrom:
                configMapKeyRef:
                  key: config
                  name: example-config-2
            image: alpine
            imagePullPolicy: IfNotPresent
            name: example-cron-job
          restartPolicy: OnFailure
  schedule: '* * * * 15'
```