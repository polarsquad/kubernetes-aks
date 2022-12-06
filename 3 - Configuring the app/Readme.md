# Configuring apps

## ConfigMaps

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfig
data:
  my-config: configData
```

## Secrets

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  my-secret: eW91IGdvdCBpdCE=
```

## Using the configuration data in deployments

You can specify how to use the configuration in your deployment's `spec.template.spec.containers.env`
For example:
```yaml
          env:
            - name: MY_CONFIG
              valueFrom:
                configMapKeyRef:
                  name: myconfig
                  key: my-config
            - name: MY_SECRET
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: my-secret
```

This maps configuration data from configMaps and secrets into variables one at a time. 

Edit the yaml-file from the very first exercise and add the secret and config maps. Also add the configuration to the deployment as described above. Make the changes with `kubectl apply`and confirm that the deployment starts as expected.

You can also load multiple properly named variables from one configMap at once like this:

```yaml
          env: []
          envFrom:
          - configMapRef:
              name: redis-config
```
The configMap would have to look like this to match the expected variable names:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfig
data:
  MY_CONFIG: configData
``` 

## Mounting configuration data as files

You might also want to mount configuration data as files inside the deployment. You'd need to specify a volume containing the data and mount the volume inside the container like this:
```yaml
...
    spec:
      containers:
      - image: nginxinc/nginx-unprivileged
        name: nginx
        ports:
        - containerPort: 8080
        readinessProbe:
          periodSeconds: 5
          successThreshold: 1
          httpGet:
            path: /
            port: 8080
        volumeMounts:
        - name: content
          mountPath: /usr/share/nginx/html/content
      volumes:
      - name: content
        configMap:
          name: demo-content
...
```

This example assumes content stored in a configMap such as this:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-content
data:
  hello.txt: Hello world!
  filler.txt: |
    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod
    tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim
    veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
    commodo consequat. Duis aute irure dolor in reprehenderit in voluptate
    velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat
    cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id
    est laborum.
```
