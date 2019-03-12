---
title: Vista kubelet regista no Azure Kubernetes Service (AKS)
description: Saiba como ver informações de resolução de problemas nos registos kubelet de nós do Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: iainfou
ms.openlocfilehash: b381145fef7e6fb399fac3387ab01fdc9a51b154
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534025"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Obter registos kubelet de nós de cluster do Azure Kubernetes Service (AKS)

Como parte de um cluster do AKS a funcionar, terá de rever os registos para resolver um problema. Incorporado no portal do Azure é a capacidade de ver os registos para o [AKS dominar componentes] [ aks-master-logs] ou [contentores num cluster do AKS][azure-container-logs]. Ocasionalmente, poderá ter de obter *kubelet* registos a partir de um nó AKS para fins de resolução de problemas.

Este artigo mostra-lhe como pode utilizar `journalctl` para ver o *kubelet* faz logon num nó AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

## <a name="create-an-ssh-connection"></a>Crie uma ligação SSH

Em primeiro lugar, crie uma ligação SSH com o nó no qual precisa exibir *kubelet* registos. Esta operação está detalhada na [SSH em nós de cluster do Azure Kubernetes Service (AKS)] [ aks-ssh] documento.

## <a name="get-kubelet-logs"></a>Obter registos kubelet

Depois de ter ligado ao nó, execute o seguinte comando para extrair os *kubelet* registos:

```console
sudo journalctl -u kubelet -o cat
```

Saída de exemplo a seguir mostra a *kubelet* registos de dados:

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>Passos Seguintes

Se precisar de informações adicionais de resolução de problemas do mestre de Kubernetes, veja [ver registos de nó principal no AKS do Kubernetes][aks-master-logs].

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: view-master-logs.md
[azure-container-logs]: ../azure-monitor/insights/container-insights-overview.md
