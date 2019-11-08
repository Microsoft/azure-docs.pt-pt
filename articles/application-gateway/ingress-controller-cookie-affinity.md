---
title: Habilitar afinidade baseada em cookie com o gateway de aplicativo
description: Este artigo fornece informações sobre como habilitar a afinidade baseada em cookie com um gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795984"
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
