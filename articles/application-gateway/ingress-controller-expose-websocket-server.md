---
title: Expor um servidor WebSocket ao Gateway de Aplicações
description: Este artigo fornece informações sobre como expor um servidor WebSocket a Application Gateway com controlador de entrada para clusters AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85207792"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Expor um servidor WebSocket ao Gateway de Aplicações

Conforme descrito na documentação do Gateway de Aplicação v2 - [fornece suporte nativo para os protocolos WebSocket e HTTP/2](features.md#websocket-and-http2-traffic). Por favor, note que tanto para o Gateway de Aplicações como para o Kubernetes Ingress - não existe uma definição configurável para ativar ou desativar seletivamente o suporte webSocket.

A implementação de Kubernetes YAML abaixo mostra a configuração mínima usada para implementar um servidor WebSocket, que é o mesmo que implementar um servidor web regular:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Dado que todos os pré-requisitos estão preenchidos, e você tem um Gateway de aplicação controlado por um Kubernetes Ingress no seu AKS, a implementação acima resultaria num servidor WebSockets exposto na porta 80 do IP público do seu Gateway de aplicação e do `ws.contoso.com` domínio.

O seguinte comando cURL testaria a implementação do servidor WebSocket:
```shell
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Sondas de Saúde WebSocket

Se a sua implementação não definir explicitamente as sondas de saúde, o Application Gateway tentará um HTTP GET no ponto final do seu servidor WebSocket.
Dependendo da implementação do servidor[(aqui está um que adoramos](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) podem ser necessários cabeçalhos específicos webSocket `Sec-Websocket-Version` (por exemplo).
Uma vez que o Application Gateway não adiciona cabeçalhos WebSocket, a resposta da sonda de saúde do Gateway de aplicação do seu servidor WebSocket será provavelmente `400 Bad Request` .
Como resultado, o Application Gateway marcará as suas cápsulas como pouco saudáveis, o que acabará por resultar num `502 Bad Gateway` para os consumidores do servidor WebSocket.
Para evitar isto, poderá ser necessário adicionar um manipulador HTTP GET para uma verificação de saúde no seu servidor `/health` (por exemplo, que devolve `200 OK` ).
