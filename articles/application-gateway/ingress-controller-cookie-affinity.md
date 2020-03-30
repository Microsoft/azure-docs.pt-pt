---
title: Ativar a finidade baseada em cookies com o Gateway da Aplicação
description: Este artigo fornece informações sobre como ativar a afinidade baseada em cookies com um Gateway de Aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795984"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Ativar a afinidade baseada em Cookies com um Gateway de Aplicação
Tal como descrito na Documentação de Gateway de [Aplicação Azure,](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)o Application Gateway suporta a finidade baseada em cookies, o que significa que pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento.

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
