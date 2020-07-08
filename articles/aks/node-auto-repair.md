---
title: Reparação automática de nós do Serviço Azure Kubernetes (AKS)
description: Saiba mais sobre a funcionalidade de reparação automática do nó e como a AKS corrige os nós de trabalhador quebrados.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735630"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Serviço Azure Kubernetes (AKS) autorreparação do nó

A AKS verifica continuamente o estado de saúde dos nós dos trabalhadores e executa a reparação automática dos nós se ficarem insalubres. Este documento informa os operadores sobre como a funcionalidade de reparação automática de nós se comporta. Além das reparações da AKS, a plataforma Azure VM [realiza manutenção em Máquinas Virtuais][vm-updates] que também experimentam problemas. Os VMs AKS e Azure trabalham em conjunto para minimizar as perturbações de serviço para clusters.

> [!Important]
> A funcionalidade de reparação automática do nó não é suportada atualmente para piscinas de nó do Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como a AKS verifica os nódoas não saudáveis

> [!Note]
> A AKS toma medidas de reparação nos nós com o **aks-correcttor da**conta do utilizador .

A AKS usa regras para determinar se um nó não é saudável e precisa de ser reparado. A AKS utiliza as seguintes regras para determinar se é necessária uma reparação automática.

* O nó reporta o estado do **NotReady** em verificações consecutivas dentro de um prazo de 10 minutos
* O nó não reporta um estado dentro de 10 minutos.

Pode verificar manualmente o estado de saúde dos seus nós com kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Como funciona a reparação automática

> [!Note]
> A AKS inicia operações de reparação com o **aks-correcttor de**conta de utilizador .

A reparação automática é suportada por predefinição para clusters com um tipo de conjunto VM de **conjuntos de balanças de máquinas virtuais**. Se um nó for determinado como insalubre com base nas regras acima, a AKS reinicia o nó após 10 minutos insalubres consecutivos. Se os nós não saudáveis após a operação inicial de reparação, as reparações adicionais são investigadas por engenheiros da AKS.
  
Se vários nós não forem saudáveis durante uma verificação de saúde, cada nó é reparado individualmente antes de começar outra reparação.

## <a name="next-steps"></a>Próximos passos

Utilize [Zonas de disponibilidade][availability-zones] para aumentar a disponibilidade com as cargas de trabalho do cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
