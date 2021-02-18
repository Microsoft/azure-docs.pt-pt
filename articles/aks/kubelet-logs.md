---
title: Ver registos de kubelet no Serviço Azure Kubernetes (AKS)
description: Saiba como ver informações de resolução de problemas nos registos de kubelet dos nós do Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 31605d1b6129c03dcd860d78f937a41ae36502a7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578620"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Obter registos kubelet dos nós de cluster do Azure Kubernetes Service (AKS)

Como parte da operação de um cluster AKS, poderá ser necessário rever os registos para resolver um problema. Incorporada no portal Azure é a capacidade de visualizar registos para os componentes ou contentores [principais da AKS][aks-master-logs] [num cluster AKS][azure-container-logs]. Ocasionalmente, poderá necessitar de obter registos de *kubelet* de um nó AKS para efeitos de resolução de problemas.

Este artigo mostra-lhe como pode usar `journalctl` para ver os registos de *kubelet* num nó AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

## <a name="create-an-ssh-connection"></a>Criar uma ligação SSH

Em primeiro lugar, crie uma ligação SSH com o nó no qual precisa de visualizar os registos *de kubelet.* Esta operação é detalhada no documento de nós de [cluster do Serviço Azure Kubernetes (AKS).][aks-ssh]

## <a name="get-kubelet-logs"></a>Obter registos kubelet

Uma vez ligado ao nó, erte o seguinte comando para puxar os registos de *kubelet:*

```console
sudo journalctl -u kubelet -o cat
```

> [!NOTE]
> Para os nóns windows, os dados de registo estão dentro `C:\k` e podem ser vistos usando o comando *mais:*
> ```
> more C:\k\kubelet.log
> ```

A seguinte saída da amostra mostra os dados do registo de *kubelet:*

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

## <a name="next-steps"></a>Passos seguintes

Se precisar de informações adicionais sobre a resolução de problemas do mestre Kubernetes, consulte [os registos de nódos de kubernetes em AKS][aks-master-logs].

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: view-master-logs.md
[azure-container-logs]: ../azure-monitor/containers/container-insights-overview.md
