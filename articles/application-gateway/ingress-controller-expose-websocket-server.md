---
title: Expor um servidor WebSocket ao Gateway de Aplicações
description: Este artigo fornece informações sobre como expor um servidor WebSocket ao Application Gateway com controlador de entrada para clusters AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297837"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Expor um servidor WebSocket ao Gateway de Aplicações

Tal como descrito na documentação Application Gateway v2 - [fornece suporte nativo para os protocolos WebSocket e HTTP/2](features.md#websocket-and-http2-traffic). Tenha em agrado que, tanto para o Gateway de Aplicação como para o Kubernetes Ingress - não existe uma definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte webSocket.

A implementação de Kubernetes YAML abaixo mostra a configuração mínima utilizada para implementar um servidor WebSocket, que é o mesmo que implementar um servidor web regular:
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

Dado que todos os pré-requisitos estão cumpridos, e você tem um Gateway de aplicação controlado por um Kubernetes Ingress no seu AKS, `ws.contoso.com` a implementação acima resultaria num servidor WebSockets exposto na porta 80 do IP público do seu Gateway de aplicação e no domínio.

O seguinte comando cURL testaria a implementação do servidor WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Sondas de saúde WebSocket

Se a sua implementação não definir explicitamente as sondas de saúde, o Application Gateway tentaria um HTTP GET no seu ponto final do servidor WebSocket.
Dependendo da implementação do servidor[(aqui está um que amamos](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)), podem ser necessários cabeçalhos específicos do WebSocket (por`Sec-Websocket-Version` exemplo).
Uma vez que o Gateway da aplicação não adiciona cabeçalhos WebSocket, a `400 Bad Request`resposta da sonda de saúde do Gateway de aplicação do seu servidor WebSocket será muito provável .
Como resultado, o Application Gateway marcará as suas cápsulas `502 Bad Gateway` como pouco saudáveis, o que acabará por resultar num para os consumidores do servidor WebSocket.
Para evitar isto, poderá ser necessário adicionar um manipulador HTTP`/health` GET para `200 OK`uma verificação de saúde ao seu servidor (por exemplo, que retorna).
