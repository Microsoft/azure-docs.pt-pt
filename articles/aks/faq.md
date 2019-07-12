---
title: Perguntas mais frequentes sobre para o Azure Kubernetes Service (AKS)
description: Encontre respostas para algumas das perguntas comuns sobre o Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 495f182ed450d0fac69b31ea2996bacc60863fea
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672767"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas mais frequentes sobre o Azure Kubernetes Service (AKS)

Este artigo aborda o frequentes perguntas sobre o Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Que regiões do Azure fornecem atualmente AKS?

Para obter uma lista completa de regiões disponíveis, consulte [AKS regiões e disponibilidade][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS suporta o dimensionamento automático de nó?

Sim, a capacidade de dimensionar automaticamente nós de agente horizontalmente no AKS está atualmente disponível em pré-visualização. Ver [Dimensionar automaticamente um cluster para atender às necessidades de aplicações no AKS][aks-cluster-autoscaler] for instructions. AKS autoscaling is based on the [Kubernetes autoscaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Pode implementar AKS em minha rede virtual existente?

Sim, pode implementar um cluster do AKS numa rede virtual existente ao utilizar o [funcionalidade de rede avançada][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitar quem tem acesso ao servidor de API do Kubernetes?

Sim, pode limitar o acesso para o servidor de API do Kubernetes utilizando [API servidor autorizado intervalos de IP][api-server-authorized-ip-ranges], que está atualmente em pré-visualização.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Posso fazer o servidor de API do Kubernetes acessível apenas dentro da minha rede virtual?

Não neste momento, mas isso está previsto. Pode controlar o progresso no [repositório do GitHub do AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Pode ter diferentes tamanhos de VM num único cluster?

Sim, pode utilizar tamanhos de máquinas virtuais no cluster do AKS, criando [vários conjuntos de nós][multi-node-pools], que está atualmente em pré-visualização.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Atualizações de segurança são aplicadas a nós de agente do AKS?

Azure aplica automaticamente patches de segurança para os nós do Linux no seu cluster com base numa agenda noturna. No entanto, é responsável por assegurar que esses nós são reiniciados como de Linux necessários. Tem várias opções para reiniciar nós:

- Manualmente, por meio do portal do Azure ou a CLI do Azure.
- Ao atualizar o seu cluster do AKS. As atualizações de cluster [cordão e drenagem de nós][cordon-drain] automatically and then bring a new node online with the latest Ubuntu image and a new patch version or a minor Kubernetes version. For more information, see [Upgrade an AKS cluster][aks-upgrade].
- Usando [Kured](https://github.com/weaveworks/kured), um daemon de reinício de código-fonte aberto do Kubernetes. Kured é executado como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitoriza cada nó para a presença de um ficheiro que indica que é necessário um reinício. No cluster, os reinícios de sistema operacional são geridos pelo mesmo [cordão e drenagem processo][cordon-drain] como uma atualização do cluster.

Para obter mais informações sobre como utilizar kured, consulte [aplicar atualizações de segurança e de kernel para nós no AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Nós do Windows Server

Para nós do Windows Server (atualmente em pré-visualização no AKS), o Windows Update automaticamente executar e aplicar as atualizações mais recentes. Com base numa agenda regular em todo o ciclo de lançamento de atualização do Windows e o seu próprio processo de validação, deve efetuar uma atualização em agrupamentos de nó do Windows Server no cluster do AKS. Este processo de atualização cria nós que executam a imagem do Windows Server mais recente e patches, em seguida, remove os nós mais antigos. Para obter mais informações sobre este processo, consulte [atualizar um conjunto de nós no AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que dois grupos de recursos são criados com o AKS?

Cada implementação do AKS abrange dois grupos de recursos:

1. Criar o primeiro grupo de recursos. Este grupo contém apenas o recurso de serviço do Kubernetes. O fornecedor de recursos do AKS cria automaticamente o segundo grupo de recursos durante a implementação. É um exemplo do segundo grupo de recursos *MC_myResourceGroup_myAKSCluster_eastus*. Para obter informações sobre como especificar o nome deste segundo grupo de recursos, consulte a secção seguinte.
1. O segundo grupo de recursos, conhecido como o *grupo de recursos de nó*, contém todos os recursos de infraestrutura associados ao cluster. Esses recursos incluem a VMs de nó do Kubernetes, redes virtuais e armazenamento. Por predefinição, o grupo de recursos de nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. AKS elimina automaticamente o recurso de nó sempre que o cluster é eliminado, pelo que só deve ser utilizada para os recursos que partilham o ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Pode fornecer meu próprio nome para o grupo de recursos de nó do AKS?

Sim. Por predefinição, AKS designar o grupo de recursos do nó *MC_clustername_resourcegroupname_location*, mas também pode fornecer seu próprio nome.

Para especificar o seu próprio nome de grupo de recursos, instalar o [pré-visualização do aks][aks-preview-cli] versão de extensão da CLI do Azure *0.3.2* ou posterior. Quando cria um cluster do AKS com o [criar az aks][az-aks-create] comando, utilize o *– grupo de recursos de nó* parâmetro e especifique um nome para o grupo de recursos. Se [utilizar um modelo Azure Resource Manager][aks-rm-template] para implementar um cluster do AKS, pode definir o nome do grupo de recursos, utilizando o *nodeResourceGroup* propriedade.

* O grupo de recursos secundário é criado automaticamente pelo fornecedor de recursos do Azure na sua própria subscrição.
* Pode especificar um nome de grupo de recursos personalizado apenas quando estiver a criar o cluster.

À medida que trabalha com o grupo de recursos de nó, tenha em atenção que não é possível:

* Especifique um grupo de recursos existente para o grupo de recursos de nó.
* Especifique uma subscrição diferente para o grupo de recursos de nó.
* Altere o nome de grupo de recursos de nó depois do cluster ter sido criado.
* Especifique os nomes dos recursos geridos no grupo de recursos de nó.
* Modificar ou eliminar etiquetas de recursos geridos no grupo de recursos de nó. (Consulte informações adicionais na próxima seção.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar as etiquetas e outras propriedades de recursos do AKS no grupo de recursos de nó?

Se modifica ou eliminar etiquetas criadas pelo Azure e outras propriedades de recurso no grupo de recursos de nó, foi possível obter resultados inesperados, tais como o dimensionamento e atualização de erros. AKS permite-lhe criar e modificar etiquetas personalizadas. Pode desejar criar ou modificar etiquetas personalizadas, por exemplo, para atribuir um centro de custo ou de unidade de negócios. Ao modificar os recursos no grupo de recursos do nó do cluster do AKS, quebrar o objetivo de nível de serviço (SLO). Para obter mais informações, consulte [faz AKS oferecer um contrato de nível de serviço?](#does-aks-offer-a-service-level-agreement)

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

Atualmente, não é possível modificar a lista de controladores de admissão no AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault está integrado com o AKS?

AKS nativamente atualmente não é integrado com o Azure Key Vault. No entanto, o [FlexVolume de Cofre de chave do Azure para o projeto de Kubernetes][keyvault-flexvolume] permite direcionar a integração de pods do Kubernetes para segredos do Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Pode executar contentores do Windows Server no AKS?

Sim, os contentores do Windows Server estão disponíveis em pré-visualização. Para executar contentores do Windows Server no AKS, crie um conjunto de nós que executa o Windows Server como o SO convidado. Contentores do Windows Server podem utilizar apenas o Windows Server 2019. Para começar a utilizar, veja [criar um cluster do AKS com um conjunto de nós do Windows Server][aks-windows-cli].

Suporte de servidor de janela para o conjunto de nós inclui algumas limitações que fazem parte do Windows Server a montante no projeto do Kubernetes. Para obter mais informações sobre essas limitações, consulte [contentores do Windows Server nas limitações de AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>O AKS oferece um contrato de nível de serviço?

Num contrato de nível de serviço (SLA), o fornecedor concorda em reembolsar o cliente para o custo do serviço se o nível de serviço publicadas não for cumprido. Como o AKS é gratuito, sem custos estão disponível para reembolsar, para que o AKS não tem formal SLA. No entanto, o AKS procura manter a disponibilidade de, pelo menos, a 99,5% para o servidor de API do Kubernetes.

## <a name="why-cant-i-set-maxpods-below-30"></a>Por que não é possível definir os maxPods abaixo de 30?

No AKS, pode definir o `maxPods` valor quando criar o cluster ao utilizar os modelos da CLI do Azure e Azure Resource Manager. No entanto, Kubenet e Azure CNI exigem um *valor mínimo* (validado no momento da criação):

| Redes | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Como o AKS é um serviço gerido, podemos implementar e gerir suplementos e pods como parte do cluster. No passado, os utilizadores podem definir um `maxPods` valor inferior ao valor que os pods geridos necessário para executar (por exemplo, 30). AKS agora calcula o número mínimo de pods com esta fórmula: ((maxPods ou (maxPods * vm_count)) > mínimo de pods de suplemento gerenciado.

Os utilizadores não é possível substituir o mínimo `maxPods` validação.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Posso aplicar o desconto de reserva do Azure para meus nós de agente do AKS?

Nós de agente do AKS são faturados como máquinas virtuais do Azure standard, por isso, se tiver comprado [reservas Azure][reservation-discounts] para o tamanho da VM que está a utilizar no AKS, são automaticamente aplicados esses descontos.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948