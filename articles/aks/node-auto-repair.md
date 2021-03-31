---
title: Reparação automática de nós do Serviço Azure Kubernetes (AKS)
description: Saiba mais sobre a funcionalidade de reparação automática do nó e como a AKS corrige os nós de trabalhador quebrados.
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89490110"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Serviço Azure Kubernetes (AKS) autorreparação do nó

A AKS verifica continuamente o estado de saúde dos nós dos trabalhadores e executa a reparação automática dos nós se ficarem insalubres. Este documento informa os operadores sobre como a funcionalidade de reparação automática de nós se comporta tanto para os nós Windows como linux. Além das reparações da AKS, a plataforma Azure VM [realiza manutenção em Máquinas Virtuais][vm-updates] que também experimentam problemas. Os VMs AKS e Azure trabalham em conjunto para minimizar as perturbações de serviço para clusters.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como a AKS verifica os nódoas não saudáveis

A AKS usa regras para determinar se um nó não é saudável e precisa de ser reparado. A AKS utiliza as seguintes regras para determinar se é necessária uma reparação automática.

* O nó reporta o estado do **NotReady** em verificações consecutivas dentro de um prazo de 10 minutos
* O nó não reporta um estado dentro de 10 minutos.

Pode verificar manualmente o estado de saúde dos seus nós com kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Como funciona a reparação automática

> [!Note]
> A AKS inicia operações de reparação com o **aks-correcttor de** conta de utilizador .

Se um nó não for saudável com base nas regras acima referidas e permanecer insalubre durante 10 minutos consecutivos, serão tomadas as seguintes medidas.

1. Reinicie o nó
1. Se o reboot não for bem sucedido, reimagem o nó
1. Se a reimagem não for bem sucedida, crie e reimagem um novo nó

Se nenhuma das ações for bem sucedida, as reparações adicionais são investigadas por engenheiros da AKS. Se vários nós não forem saudáveis durante uma verificação de saúde, cada nó é reparado individualmente antes de começar outra reparação.

## <a name="next-steps"></a>Passos seguintes

Utilize [Zonas de disponibilidade][availability-zones] para aumentar a disponibilidade com as cargas de trabalho do cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
