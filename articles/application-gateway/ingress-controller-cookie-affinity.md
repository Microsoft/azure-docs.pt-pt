---
title: Ativar afinidade baseada em cookies com o Gateway de Aplicação
description: Este artigo fornece informações sobre como ativar a afinidade baseada em cookies com um Gateway de aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397421"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Ativar afinidade baseada em cookies com um Gateway de aplicação
Conforme descrito na Documentação do [Gateway de Aplicação Azure,](./application-gateway-components.md#http-settings)o Gateway de Aplicação suporta afinidade baseada em cookies, o que significa que pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento.

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