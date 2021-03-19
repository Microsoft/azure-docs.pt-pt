---
title: Adicione sondas de saúde às suas cápsulas AKS
description: Este artigo fornece informações sobre como adicionar sondas de saúde (prontidão e/ou viva) a cápsulas AKS com um Gateway de aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8c8b8b0090877db7abc8fae0e44f928e8b10dcf5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807997"
---
# <a name="add-health-probes-to-your-service"></a>Adicione sondas de saúde ao seu serviço
Por predefinição, o controlador Ingress irá providenciar uma sonda HTTP GET para as cápsulas expostas.
As propriedades da sonda podem ser personalizadas adicionando uma [Sonda de Prontidão ou Liveness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) às suas `deployment` / `pod` especificações.

## <a name="with-readinessprobe-or-livenessprobe"></a>Com `readinessProbe` ou `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Referência API da Kubernetes:
* [Sondas de Contentores](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [ação httpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` e `livenessProbe` são suportados quando configurados com `httpGet` .
> * A sondagem numa porta que não a que está exposta na cápsula não é suportada atualmente.
> * `HttpHeaders`Não `InitialDelaySeconds` `SuccessThreshold` são apoiados.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Sem `readinessProbe` ou `livenessProbe`
Se as sondas acima não forem fornecidas, então o Controlador ingress assume que o serviço é alcançável em `Path` `backend-path-prefix` especificação para anotação ou o `path` especificado na `ingress` definição para o serviço.

## <a name="default-values-for-health-probe"></a>Valores padrão para sonda de saúde
Para qualquer imóvel que não possa ser deduzido pela sonda de prontidão/vivacidade, os valores predefinidos são definidos.

| Propriedade da sonda Gateway de aplicação | Valor Predefinido |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |