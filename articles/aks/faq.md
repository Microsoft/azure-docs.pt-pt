---
title: Perguntas mais frequentes sobre para o Azure Kubernetes Service (AKS)
description: Fornece respostas para algumas das perguntas comuns sobre o Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: ae92a5c894b186a1c8b471c1b446a88299742aec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466380"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas mais frequentes sobre o Azure Kubernetes Service (AKS)

Este artigo aborda o frequentes perguntas sobre o Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Que regiões do Azure fornecem o Azure Kubernetes Service (AKS) hoje em dia?

Para obter uma lista completa de regiões disponíveis, consulte [AKS regiões e disponibilidade][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS suporta o dimensionamento automático de nó?

Sim, o dimensionamento automático está disponível através da [dimensionamento automático do Kubernetes] [ auto-scaler] no momento da elaboração 1.10 de Kubernetes. Para obter mais informações sobre como configurar e utilizar o dimensionamento automático de cluster, consulte [dimensionamento automático de Cluster no AKS][aks-cluster-autoscale].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>O AKS suporta Kubernetes controlo de acesso baseado em funções (RBAC)?

Sim, RBAC do Kubernetes está ativada por predefinição, quando os clusters são criados com a CLI do Azure. RBAC pode ser ativado nos clusters criados com o portal do Azure ou a modelos.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Pode implementar AKS em minha rede virtual existente?

Sim, pode implementar um cluster do AKS numa rede virtual existente com o [funcionalidade de rede avançada][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Posso restringir o servidor de API do Kubernetes para apenas ser acessível em minha rede virtual?

Neste momento, não. O servidor de API do Kubernetes é exposto como um público (FQDN) do nome de domínio completamente qualificado. Pode controlar o acesso ao seu cluster com [Kubernetes com base em função de controlo de acesso (RBAC) e Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Atualizações de segurança são aplicadas a nós de agente do AKS?

Sim, o Azure aplica automaticamente a patches de segurança para os nós do cluster com base numa agenda noturna. No entanto, é responsável por assegurar que nós são reiniciados conforme necessário. Tem várias opções para a execução de reinicializações de nó:

- Manualmente, por meio do portal do Azure ou a CLI do Azure.
- Ao atualizar o seu cluster do AKS. Atualizações do cluster automaticamente [cordão e drenagem de nós][cordon-drain], colocar cada nó de cópia de segurança com a imagem mais recente do Ubuntu e uma nova versão de patch ou uma versão secundária do Kubernetes. Para obter mais informações, consulte [atualizar um cluster do AKS][aks-upgrade].
- Usando [Kured](https://github.com/weaveworks/kured), um daemon de reinício de código-fonte aberto do Kubernetes. Kured é executado como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitoriza cada nó para a presença de um arquivo que indica que é necessário um reinício. Os reinícios de sistema operacional são geridos no cluster com o mesmo [cordão e drenagem processo] [ cordon-drain] como uma atualização do cluster.

Para obter mais informações sobre como utilizar kured, consulte [aplicar atualizações de segurança e de kernel para nós no AKS][node-updates-kured].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que dois grupos de recursos são criados com o AKS?

Cada implementação do AKS abrange dois grupos de recursos:

- O primeiro grupo de recursos é criado por si e contém apenas o recurso de serviço do Kubernetes. O fornecedor de recursos do AKS cria automaticamente o um segundo durante a implementação, como *MC_myResourceGroup_myAKSCluster_eastus*.
- Este recurso segundo grupo, tal como *MC_myResourceGroup_myAKSCluster_eastus*, contém todos os recursos de infraestrutura associados ao cluster. Esses recursos incluem a VMs de nó do Kubernetes, redes virtuais e armazenamento. Este grupo de recursos separado é criado para simplificar a limpeza de recursos.

Se criar recursos para utilização com o seu cluster do AKS, como contas de armazenamento ou endereços IP públicos reservados, colocá-los no grupo de recursos gerada automaticamente.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Posso modificar as etiquetas e outras propriedades de recursos do AKS no grupo de recursos MC_ *?

Modificar e eliminar as etiquetas criadas pelo Azure e outras propriedades de recursos no *MC_** grupo de recursos pode levar a resultados inesperados, tais como o dimensionamento e atualização de erros. É suportada para criar e modificar etiquetas personalizadas adicionais, tais como atribuir um centro de custo ou de unidade de negócios. Modificação de recursos sob o *MC_** no AKS cluster divide o objetivo de nível de serviço (SLO). Para obter mais informações, consulte [faz AKS oferecer um contrato de nível de serviço?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Os controladores de admissão do Kubernetes AKS oferece suporte? Controladores de admissão podem ser adicionados ou removidos?

AKS suporta as seguintes [controladores de admissão][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Não é atualmente possível modificar a lista de controladores de admissão no AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault está integrado com o AKS?

AKS não está atualmente nativamente integrado com o Azure Key Vault. No entanto, o [FlexVolume de Cofre de chave do Azure para o projeto de Kubernetes] [ keyvault-flexvolume] permite direcionar a integração de pods do Kubernetes a segredos do Cofre de chaves.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Pode executar contentores do Windows Server no AKS?

Para executar contentores do Windows Server, terá de executar nós com base no Windows Server. Neste momento, o Windows baseado em servidor nós não estão disponíveis no AKS. No entanto, pode usar o Virtual Kubelet agendar contentores do Windows no Azure Container Instances e geri-los como parte do cluster do AKS. Para obter mais informações, consulte [utilização Virtual Kubelet com o AKS][virtual-kubelet].

## <a name="does-aks-offer-a-service-level-agreement"></a>O AKS oferece um contrato de nível de serviço?

Num contrato ao nível de serviço, (SLA), o fornecedor concorda em reembolsar o cliente para o custo do serviço se o nível de serviço publicadas não for cumprido. Como AKS em si é gratuito, não há nenhum custo disponível para reembolsar e, portanto, não existe SLA formal. No entanto, o AKS procura manter a disponibilidade de, pelo menos, a 99,5% para o servidor de API do Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol

