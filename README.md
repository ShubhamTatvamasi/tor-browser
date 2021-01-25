# tor-browser

Install tor-browser:
```bash
kubectl create deployment tor-browser --image=kasmweb/tor-browser:1.7.0
kubectl expose deployment tor-browser --port=6901 --name=tor-browser
```

Setup password:
```bash
kubectl exec -it deploy/tor-browser -- bash

# run this inside container
echo "password" | vncpasswd -f > /home/kasm-user/.vnc/passwd
```

Setup ingress:
```bash
kubectl apply -f - << EOF
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: tor-browser
  annotations:
    nginx.org/websocket-services: tor-browser
spec:
  tls:
    - hosts:
      - tor-browser.k8s.shubhamtatvamasi.com
      secretName: letsencrypt
  rules:
    - host: tor-browser.k8s.shubhamtatvamasi.com
      http:
        paths:
        - path: /
          backend:
            serviceName: tor-browser
            servicePort: 6901
EOF
```

Delete everything:
```bash
kubectl delete deploy/tor-browser svc/tor-browser ing/tor-browser
```
