---
title: Reparação automática de nós do Serviço Azure Kubernetes (AKS)
description: Saiba mais sobre a funcionalidade de reparação automática do nó e como o AKS corrige os nós dos trabalhadores partidos.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284845"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Serviço Azure Kubernetes (AKS) reparação automática

A AKS verifica continuamente o estado de saúde dos nódosos dos trabalhadores e realiza a reparação automática dos nódos se não forem saudáveis. Esta documentação descreve como o Azure Kubernetes Service (AKS) monitoriza os nós dos trabalhadores e repara nós de trabalhadores pouco saudáveis.  A documentação é para informar os operadores da AKS sobre o comportamento da funcionalidade de reparação do nó. É também importante notar que a plataforma Azure [realiza a manutenção em Máquinas Virtuais][vm-updates] que experimentam problemas. A AKS e a Azure trabalham em conjunto para minimizar as perturbações de serviço dos seus clusters.

> [!Important]
> A funcionalidade de reparação automática do nó não é suportada atualmente para piscinas de nós do Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como aks verifica por nóões não saudáveis

> [!Note]
> A AKS toma medidas de reparação em nódosos com a conta de utilizador **aks-remediador**.

A AKS usa regras para determinar se um nó é um estado pouco saudável e precisa de ser reparado. A AKS utiliza as seguintes regras para determinar se é necessária uma reparação automática.

* O nó reporta o estado do **NotReady** em controlos consecutivos dentro de um prazo de 10 minutos
* O nó não reporta um estado em 10 minutos.

Pode verificar manualmente o estado de saúde dos seus nódosos com kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Como funciona a reparação automática

> [!Note]
> A AKS toma medidas de reparação em nódosos com a conta de utilizador **aks-remediador**.

Este comportamento é para **conjuntos**de escala de máquina virtual .  A reparação automática dá vários passos para reparar um nó partido.  Se um nó está determinado a não ser saudável, aks tenta várias etapas de reparação.  Os passos são realizados por esta ordem:

1. Após o tempo de execução do recipiente ficar sem resposta durante 10 minutos, os serviços de tempo de execução falhados são reiniciados no nó.
2. Se o nó não estiver pronto dentro de 10 minutos, o nó é reiniciado.
3. Se o nó não estiver pronto dentro de 30 minutos, o nó é re-image.

> [!Note]
> Se vários nós não são saudáveis, são reparados um por um

## <a name="next-steps"></a>Passos seguintes

Utilize zonas de [disponibilidade][availability-zones] para aumentar a alta disponibilidade com as suas cargas de trabalho de cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
