---
title: Adicionar investigações de integridade ao seu pods AKS
description: Este artigo fornece informações sobre como adicionar investigações de integridade (prontidão e/ou vida) a AKS pods com um gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795596"
---
# <a name="add-health-probes-to-your-service"></a>Adicionar investigações de integridade ao seu serviço
Por padrão, o controlador de entrada provisionará uma investigação HTTP GET para o pods exposto.
As propriedades de investigação podem ser personalizadas adicionando uma [investigação de preparação ou vida](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) ao seu `deployment`/especificação de `pod`.

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

Referência da API do kubernetes:
* [Investigações de contêiner](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Ação HttpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * Há suporte para `readinessProbe` e `livenessProbe` quando configurado com `httpGet`.
> * No momento, não há suporte para investigação em uma porta diferente daquela exposta no pod.
> * Não há suporte para `HttpHeaders`, `InitialDelaySeconds``SuccessThreshold`.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Sem `readinessProbe` ou `livenessProbe`
Se as investigações acima não forem fornecidas, o controlador de entrada fará uma suposição de que o serviço pode ser acessado no `Path` especificado para `backend-path-prefix` anotação ou o `path` especificado na definição de `ingress` para o serviço.

## <a name="default-values-for-health-probe"></a>Valores padrão para investigação de integridade
Para qualquer propriedade que não pode ser inferida pela investigação de preparação/vida, os valores padrão são definidos.

| Propriedade de investigação do gateway de aplicativo | Valor padrão |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |