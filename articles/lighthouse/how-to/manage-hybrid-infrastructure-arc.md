---
title: Gerir infraestrutura híbrida em escala com Azure Arc
description: Aprenda a gerir eficazmente as máquinas dos seus clientes e os clusters Kubernetes fora de Azure.
ms.date: 09/15/2020
ms.topic: how-to
ms.openlocfilehash: 2ffbe9019398896c594b7cb0e0424d2b5f4dc37a
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605334"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Gerir infraestrutura híbrida em escala com Azure Arc

Como prestador de serviços, pode ter a bordo vários inquilinos de clientes para o [Farol de Azure.](../overview.md) O Azure Lighthouse permite que os prestadores de serviços realizem operações em escala em vários inquilinos do Azure Ative Directory (Azure AD) de uma só vez, tornando as tarefas de gestão mais eficientes.

[O Azure Arc](../../azure-arc/overview.md) ajuda a simplificar ambientes complexos e distribuídos em todos os locais, bordas e multicloud, permitindo a implantação de serviços Azure em qualquer lugar e estendendo a gestão do Azure a qualquer infraestrutura.

Com [os servidores Azure Arc ativados (pré-visualização),](../../azure-arc/servers/overview.md)os clientes podem gerir quaisquer máquinas Windows e Linux hospedadas fora do Azure na sua rede corporativa, da mesma forma que gerem máquinas virtuais nativas do Azure. Ao associar uma máquina virtual híbrida ao Azure, esta torna-se ligada e será processada como um recurso no Azure. Os prestadores de serviços podem então gerir estas máquinas não-Azure, juntamente com os recursos Azure dos seus clientes.

[Azure Arc habilitado Kubernetes (pré-visualização)](../../azure-arc/kubernetes/overview.md) permite que os clientes anexem e configurem aglomerados Kubernetes dentro ou fora de Azure. Quando um cluster Kubernetes estiver ligado ao Arco de Azure, aparecerá no portal Azure, com um ID do Gestor de Recursos Azure e uma identidade gerida. Os clusters estão ligados às subscrições padrão do Azure, estão localizados num grupo de recursos, e podem receber tags como qualquer outro recurso Azure.

Este tópico fornece uma visão geral de como os prestadores de serviços podem usar servidores ativados Azure Arc (pré-visualização) e Azure Arc habilitado Kubernetes (pré-visualização) de uma forma escalável para gerir o ambiente híbrido dos seus clientes, com visibilidade em todos os inquilinos geridos do cliente.

> [!TIP]
> Embora nos refiramos a prestadores de serviços e clientes neste tópico, esta orientação também se aplica às [empresas que utilizam o Farol Azure para gerir vários inquilinos.](../concepts/enterprise.md)

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers-preview"></a>Gerir servidores híbridos em escala com servidores ativados a Azure Arc (pré-visualização)

> [!NOTE]
> Os servidores ativados Azure Arc estão atualmente em pré-visualização. Não recomendamos para cargas de trabalho de produção neste momento.

Como prestador de serviços, pode gerir no local máquinas Windows Server ou Linux fora do Azure que os seus clientes ligaram à sua subscrição utilizando o [agente Azure Connected Machine](../../azure-arc/servers/agent-overview.md).

Ao visualizar recursos para uma subscrição delegada no portal Azure, verá estas máquinas conectadas rotuladas com **Azure Arc**. Pode gerir estas máquinas conectadas utilizando construções Azure, como a Azure Policy e a marcação, da mesma forma que geriria os recursos Azure do cliente. Você também pode trabalhar em todos os inquilinos clientes para gerir todas as máquinas híbridas conectadas em conjunto.

Por exemplo, pode [garantir que o mesmo conjunto de políticas são aplicados através das máquinas híbridas dos clientes.](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md) Também pode utilizar o Azure Security Center para monitorizar a conformidade em todos os ambientes híbridos dos seus clientes, ou [utilizar o Azure Monitor para recolher dados diretamente das suas máquinas híbridas](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) num espaço de trabalho do Log Analytics. [As extensões de máquinas virtuais](../../azure-arc/servers/manage-vm-extensions.md) podem ser implantadas em VMs não-Azure Windows e Linux, simplificando a gestão das máquinas híbridas do cliente.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes-preview"></a>Gerir clusters híbridos de Kubernetes em escala com Azure Arc habilitado Kubernetes (pré-visualização)

> [!NOTE]
> Azure Arc habilitado Kubernetes está atualmente em pré-visualização. Não recomendamos para cargas de trabalho de produção neste momento.

Você pode gerir clusters Kubernetes que foram [ligados à subscrição de um cliente com Azure Arc](../../azure-arc/kubernetes/connect-cluster.md), assim como se estivessem a funcionar em Azure.

Se o seu cliente criou uma [conta principal de serviço para a bordo de clusters Kubernetes para Azure Arc,](../../azure-arc/kubernetes/create-onboarding-service-principal.md)pode aceder a esta conta principal de serviço a bordo e gerir clusters. Isto pode ser feito pelos utilizadores do inquilino gerente a quem foi concedido o papel "Kubernetes Cluster - Azure Arc Onboarding" Azure built-in quando a subscrição que contém a conta principal do serviço foi [a bordo do Farol de Azure.](onboard-customer.md)

Pode implementar [configurações](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) e [gráficos helm](../../azure-arc/kubernetes/use-gitops-with-helm.md) usando GitOps para clusters conectados.

Também pode monitorizar os agrupamentos conectados com o Azure Monitor e [utilizar a Política Azure para aplicar configurações de cluster à escala](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Passos seguintes

- Explore os arranques e amostras no [repositório Azure Arc GitHub.](https://github.com/microsoft/azure_arc) 
- Saiba mais sobre [cenários suportados para servidores ativados do Azure Arc](../../azure-arc/servers/overview.md#supported-scenarios).
- Conheça as [distribuições de Kubernetes suportadas pela Azure Arc.](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions)
- Saiba como [implementar uma política à escala.](policy-at-scale.md)
- Aprenda a [utilizar registos de monitores Azure à escala](monitor-at-scale.md).

