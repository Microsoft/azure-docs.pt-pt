---
title: Habilitar afinidade baseada em cookie com o gateway de aplicativo
description: Este artigo fornece informações sobre como habilitar a afinidade baseada em cookie com um gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513578"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Habilitar afinidade baseada em cookie com um gateway de aplicativo
Conforme descrito na [documentação do gateway de aplicativo Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), o gateway de aplicativo dá suporte à afinidade baseada em cookie, o que significa que ele pode direcionar o tráfego subsequente de uma sessão de usuário para o mesmo servidor para processamento.

## <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
