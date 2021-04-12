---
title: Reparação automática de nós do Serviço Azure Kubernetes (AKS)
description: Saiba mais sobre a funcionalidade de reparação automática do nó e como a AKS corrige os nós de trabalhador quebrados.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105209"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Serviço Azure Kubernetes (AKS) autorreparação do nó

A AKS monitoriza continuamente o estado de saúde dos nós dos trabalhadores e executa a reparação automática do nó se não for saudável. A plataforma Azure virtual machine (VM) [realiza manutenção em VMs][vm-updates] com problemas. 

Os VMs AKS e Azure trabalham em conjunto para minimizar as perturbações de serviço para clusters.

Neste documento, irá aprender como a funcionalidade de reparação automática de nós se comporta tanto para os nós Windows como para o Linux. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como a AKS verifica os nódoas não saudáveis

A AKS utiliza as seguintes regras para determinar se um nó não é saudável e precisa de ser reparado: 
* O nó informa o estado **do NotReady** em verificações consecutivas dentro de um prazo de 10 minutos.
* O nó não reporta nenhum estado em 10 minutos.

Pode verificar manualmente o estado de saúde dos seus nós com kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Como funciona a reparação automática

> [!Note]
> A AKS inicia operações de reparação com o **aks-correcttor de** conta de utilizador .

Se a AKS identificar um nó pouco saudável que permanece insalubre durante 10 minutos, a AKS toma as seguintes ações:

1. Reinicie o nó.
1. Se o reboot não for bem sucedido, reimagem o nó.
1. Se a reimagem não for bem sucedida, crie e reimagem um novo nó.

As reparações alternativas são investigadas por engenheiros da AKS se a reparação automática não for bem sucedida. 

Se a AKS encontrar múltiplos nós insalubres durante uma verificação de saúde, cada nó é reparado individualmente antes de começar outra reparação.

## <a name="next-steps"></a>Passos seguintes

Utilize [Zonas de disponibilidade][availability-zones] para aumentar a disponibilidade com as cargas de trabalho do cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
