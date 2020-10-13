---
title: Ativar afinidade baseada em cookies com o Gateway de Aplicação
description: Este artigo fornece informações sobre como ativar a afinidade baseada em cookies com um Gateway de aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807958"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Ativar afinidade baseada em cookies com um Gateway de aplicação
Conforme descrito na Documentação do [Gateway de Aplicação Azure,](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)o Gateway de Aplicação suporta afinidade baseada em cookies, o que significa que pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento.

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
