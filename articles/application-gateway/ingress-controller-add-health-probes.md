---
title: Adicione sondas de saúde às suas cápsulas AKS
description: Este artigo fornece informações sobre como adicionar sondas de saúde (prontidão e/ou vivacidade) a cápsulas AKS com um Gateway de Aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795596"
---
# <a name="add-health-probes-to-your-service"></a>Adicione sondas de saúde ao seu serviço
Por padrão, o controlador Ingress fornecerá uma sonda HTTP GET para as cápsulas expostas.
As propriedades da sonda podem ser personalizadas adicionando `deployment` / `pod` uma [Sonda de Prontidão ou Vivacidade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) à sua especificação.

## <a name="with-readinessprobe-or-livenessprobe"></a>Com `readinessProbe` ou`livenessProbe`
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

Referência Kubernetes API:
* [Sondas de contentores](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Ação httpget](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`e `livenessProbe` são suportados quando `httpGet`configurados com .
> * A sondagem numa porta que não a exposta na cápsula não é suportada atualmente.
> * `HttpHeaders`não `InitialDelaySeconds` `SuccessThreshold` são apoiados.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Sem ou sem `readinessProbe``livenessProbe`
Se as sondas acima não forem fornecidas, então o `Path` Controlador Ingress `backend-path-prefix` assuma `path` que o `ingress` serviço é acessível especificado para anotação ou o especificado na definição para o serviço.

## <a name="default-values-for-health-probe"></a>Valores padrão para sonda de saúde
Para qualquer imóvel que não possa ser inferido pela sonda de prontidão/vivacidade, os valores predefinidos são definidos.

| Propriedade da sonda Gateway de aplicação | Valor Predefinido |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |