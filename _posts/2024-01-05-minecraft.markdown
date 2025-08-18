---
layout: post
title: Minecraft
categories: blog
---

Finally got around to giving this a proper go.  I had a little 1-replica deployment going on the Pi cluster during Lockdown but back then I hadn't figured out PVCs or MetalLB, so it wasn't very scalable.  However we did spend some good time in there.

Now, when his friends come over, I can spin up a new server for them so they don't wreck his three personal ones.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minecraft
  labels:
    app: minecraft

spec:
  replicas: 1
  selector:
    matchLabels:
      app: minecraft
  template:
    metadata:
      labels:
        app: minecraft
    spec:
      containers:
        - name: minecraft
          image: itzg/minecraft-server
          volumeMounts:
            - mountPath: /data
              name: minecraft-vol
              subPath: data
          ports:
            - containerPort: 25565
          env:
            - name: "EULA"
              value: "TRUE"
      volumes:
        - name: minecraft-vol
          persistentVolumeClaim:
            claimName: minecraft-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: minecraft-svc
spec:
  selector:
    app: minecraft
  ports:
    - protocol: TCP
      port: 25565
      targetPort: 25565
  type: LoadBalancer

```
