### Geth Helm Chart

#### Installation

```
helm install --namespace blockchain geth ./helm-geth
```

#### Chart Configuration

Args

```
args:
  - --mainnet
  - --datadir=/data
  - --http
  - --http.addr=0.0.0.0
  - --http.port=8545
  - --http.api=eth,net,web3
  - --http.corsdomain=*
  - --authrpc.addr=0.0.0.0
  - --authrpc.port=8551
  - --authrpc.vhosts=*
  - --authrpc.jwtsecret=/jwt/jwt.hex
  - --metrics
  - --metrics.addr=0.0.0.0
  - --metrics.port=6060
```

  Ports

```
ports:
- name: engine
    port: 8551
    targetPort: 8551
- name: http-rpc
    port: 8545
    targetPort: 8545
- name: metrics
    port: 6060
    targetPort: 6060
```

JWT Secret Generation 
(shared by Geth and Lighthouse)

```
openssl rand -hex 32 > jwt.hex
kubectl create secret generic geth-jwt --namespace blockchain --from-file=jwt.hex=jwt.hex
```

Volumes

```
# PVC for geth data persistence

persistence:
  enabled: true
  size: 2000Gi
  storageClass:
  accessModes:
    - ReadWriteOnce
  mountPath: /data
  name: geth-data

volumeMounts:
  - name: jwt
    mountPath: /jwt
    readOnly: true
    
volumes:
  - name: jwt
    secret:
      secretName: geth-jwt
      items:
        - key: jwt.hex
          path: jwt.hex
```
