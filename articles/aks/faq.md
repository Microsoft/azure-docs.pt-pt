---
title: Perguntas frequentes sobre o AKS (serviço kubernetes do Azure)
description: Encontre respostas para algumas das perguntas mais comuns sobre o AKS (serviço kubernetes do Azure).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/02/2019
ms.author: mlearned
ms.openlocfilehash: e68ce5a198165c4187cbad9e86ce61f67694a82d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961594"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre o AKS (serviço kubernetes do Azure)

Este artigo aborda perguntas frequentes sobre o AKS (serviço kubernetes do Azure).

## <a name="which-azure-regions-currently-provide-aks"></a>Quais regiões do Azure atualmente fornecem AKS?

Para obter uma lista completa das regiões disponíveis, consulte [regiões e disponibilidade do AKS][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>O AKS dá suporte ao dimensionamento automático de nós?

Sim, a capacidade de dimensionar automaticamente os nós de agente horizontalmente no AKS está disponível atualmente na versão prévia. Consulte [dimensionar automaticamente um cluster para atender às demandas de aplicativos no AKs][aks-cluster-autoscaler] para obter instruções. O dimensionamento automático do AKS é baseado no [dimensionador][auto-scaler]automático do kubernetes.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Posso implantar o AKS em minha rede virtual existente?

Sim, você pode implantar um cluster AKS em uma rede virtual existente usando o [recurso de rede avançado][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitar quem tem acesso ao servidor de API do kubernetes?

Sim, você pode limitar o acesso ao servidor de API kubernetes usando [intervalos de IP autorizados do servidor de API][api-server-authorized-ip-ranges].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Posso tornar o servidor de API kubernetes acessível somente dentro da minha rede virtual?

Não neste momento, mas isso está planejado. Você pode acompanhar o progresso no [repositório GitHub do AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Posso ter diferentes tamanhos de VM em um único cluster?

Sim, você pode usar diferentes tamanhos de máquina virtual em seu cluster AKS criando [vários pools de nós][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente do AKS?

O Azure aplica automaticamente patches de segurança aos nós do Linux em seu cluster em um agendamento noturno. No entanto, você é responsável por garantir que esses nós do Linux sejam reinicializados conforme necessário. Você tem várias opções para reinicializar nós:

- Manualmente, por meio do portal do Azure ou do CLI do Azure.
- Atualizando o cluster AKS. O cluster atualiza os [nós Cordon e dreno][cordon-drain] automaticamente e, em seguida, coloca um novo nó online com a imagem mais recente do Ubuntu e uma nova versão do patch ou uma versão secundária do kubernetes. Para obter mais informações, consulte [atualizar um cluster AKs][aks-upgrade].
- Usando o [Kured](https://github.com/weaveworks/kured), um daemon de reinicialização de código aberto para kubernetes. Kured é executado como um [daemonset](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. No cluster, as reinicializações do sistema operacional são gerenciadas pelo mesmo [processo de Cordon e drenagem][cordon-drain] como uma atualização de cluster.

Para obter mais informações sobre como usar o kured, consulte [aplicar segurança e atualizações de kernel a nós no AKs][node-updates-kured].

### <a name="windows-server-nodes"></a>Nós do Windows Server

Para nós do Windows Server (atualmente em visualização no AKS), Windows Update não executa automaticamente e aplica as atualizações mais recentes. Em um cronograma regular em relação ao ciclo de liberação Windows Update e seu próprio processo de validação, você deve executar uma atualização no cluster e nos pools de nó do Windows Server no cluster AKS. Esse processo de atualização cria nós que executam a imagem e os patches mais recentes do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre esse processo, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que dois grupos de recursos são criados com o AKS?

O AKS baseia-se em vários recursos de infraestrutura do Azure, incluindo conjuntos de dimensionamento de máquinas virtuais, redes virtuais e Managed disks. Isso permite que você aproveite muitos dos principais recursos da plataforma Azure no ambiente kubernetes gerenciado fornecido pelo AKS. Por exemplo, a maioria dos tipos de máquina virtual do Azure pode ser usada diretamente com AKS e as reservas do Azure podem ser usadas para receber descontos nesses recursos automaticamente.

Para habilitar essa arquitetura, cada implantação AKS abrange dois grupos de recursos:

1. Você cria o primeiro grupo de recursos. Esse grupo contém apenas o recurso de serviço kubernetes. O provedor de recursos AKS cria automaticamente o segundo grupo de recursos durante a implantação. Um exemplo do segundo grupo de recursos é *MC_myResourceGroup_myAKSCluster_eastus*. Para obter informações sobre como especificar o nome desse segundo grupo de recursos, consulte a próxima seção.
1. O segundo grupo de recursos, conhecido como *grupo de recursos de nó*, contém todos os recursos de infraestrutura associados ao cluster. Esses recursos incluem as VMs do nó kubernetes, a rede virtual e o armazenamento. Por padrão, o grupo de recursos de nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. O AKS exclui automaticamente o recurso de nó sempre que o cluster é excluído, portanto, ele só deve ser usado para recursos que compartilham o ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Posso fornecer meu próprio nome para o grupo de recursos do nó AKS?

Sim. Por padrão, AKS nomeará o grupo de recursos do nó *MC_resourcegroupname_clustername_location*, mas você também poderá fornecer seu próprio nome.

Para especificar seu próprio nome de grupo de recursos, instale o [AKs-preview][aks-preview-cli] CLI do Azure versão de extensão *0.3.2* ou posterior. Ao criar um cluster AKS usando o comando [AZ AKs Create][az-aks-create] , use o parâmetro *--node-Resource-Group* e especifique um nome para o grupo de recursos. Se você [usar um modelo de Azure Resource Manager][aks-rm-template] para implantar um cluster AKs, poderá definir o nome do grupo de recursos usando a propriedade *nodeResourceGroup* .

* O grupo de recursos secundário é criado automaticamente pelo provedor de recursos do Azure em sua própria assinatura.
* Você pode especificar um nome de grupo de recursos personalizado somente quando estiver criando o cluster.

Ao trabalhar com o grupo de recursos do nó, tenha em mente que não é possível:

* Especifique um grupo de recursos existente para o grupo de recursos do nó.
* Especifique uma assinatura diferente para o grupo de recursos do nó.
* Altere o nome do grupo de recursos do nó depois que o cluster tiver sido criado.
* Especifique nomes para os recursos gerenciados dentro do grupo de recursos do nó.
* Modifique ou exclua marcas de recursos gerenciados dentro do grupo de recursos do nó. (Consulte informações adicionais na próxima seção.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar marcas e outras propriedades dos recursos do AKS no grupo de recursos do nó?

Se você modificar ou excluir as marcas criadas pelo Azure e outras propriedades de recurso no grupo de recursos do nó, poderá obter resultados inesperados, como o dimensionamento e a atualização de erros. AKS permite que você crie e modifique marcas personalizadas. Talvez você queira criar ou modificar marcas personalizadas, por exemplo, para atribuir uma unidade de negócios ou um centro de custo. Ao modificar os recursos no grupo de recursos do nó no cluster AKS, você interrompe o objetivo de nível de serviço (SLO). Para obter mais informações, consulte [o AKs oferece um contrato de nível de serviço?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quais controladores de admissão kubernetes o AKS dá suporte? Os controladores de admissão podem ser adicionados ou removidos?

O AKS dá suporte aos seguintes [controladores de admissão][admission-controllers]:

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

No momento, não é possível modificar a lista de controladores de admissão no AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>O é Azure Key Vault integrado ao AKS?

Atualmente, o AKS não está integrado nativamente com Azure Key Vault. No entanto, o [projeto Azure Key Vault FlexVolume for kubernetes][keyvault-flexvolume] permite a integração direta do kubernetes pods com os segredos Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar contêineres do Windows Server em AKS?

Sim, os contêineres do Windows Server estão disponíveis em versão prévia. Para executar contêineres do Windows Server no AKS, você cria um pool de nós que executa o Windows Server como o sistema operacional convidado. Os contêineres do Windows Server podem usar apenas o Windows Server 2019. Para começar, consulte [criar um cluster AKs com um pool de nós do Windows Server][aks-windows-cli].

O suporte do Windows Server para pool de nós inclui algumas limitações que fazem parte do Windows Server upstream no projeto kubernetes. Para obter mais informações sobre essas limitações, consulte [contêineres do Windows Server em limitações do AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>O AKS oferece um contrato de nível de serviço?

Em um SLA (contrato de nível de serviço), o provedor concorda em reembolsar o cliente pelo custo do serviço se o nível de serviço publicado não for atendido. Como o AKS é gratuito, nenhum custo está disponível para reembolsar, portanto, o AKS não tem SLA formal. No entanto, o AKS busca manter a disponibilidade de pelo menos 99,5 por cento para o servidor de API do kubernetes.

É importante reconhecer a distinção entre a disponibilidade do serviço AKS, que se refere ao tempo de atividade do plano de controle kubernetes e à disponibilidade de sua carga de trabalho específica que está sendo executada em máquinas virtuais do Azure. Embora o plano de controle possa ficar indisponível se o plano de controle não estiver pronto, suas cargas de trabalho de cluster em execução nas VMs do Azure ainda poderão funcionar. Dadas as VMs do Azure são recursos pagos que são apoiados por um SLA financeiro. Leia [aqui para obter mais detalhes](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) sobre o SLA de VM do Azure e como aumentar essa disponibilidade com recursos como [zonas de disponibilidade][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Por que não posso definir maxPods abaixo de 30?

No AKS, você pode definir o valor de `maxPods` ao criar o cluster usando os modelos CLI do Azure e Azure Resource Manager. No entanto, Kubenet e CNI do Azure exigem um *valor mínimo* (validado no momento da criação):

| Redes | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Como o AKS é um serviço gerenciado, implantamos e gerenciamos Complementos e pods como parte do cluster. No passado, os usuários podiam definir um valor `maxPods` menor do que o valor que o pods gerenciado exigia executar (por exemplo, 30). O AKS agora calcula o número mínimo de pods usando esta fórmula: ((maxPods ou (maxPods * vm_count)) > mínimo de pods de complemento gerenciado.

Os usuários não podem substituir a validação de `maxPods` mínima.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Posso aplicar descontos de reserva do Azure aos meus nós de agente do AKS?

Os nós de agente AKS são cobrados como máquinas virtuais padrão do Azure. portanto, se você comprou as [reservas do Azure][reservation-discounts] para o tamanho da VM que você está usando em AKs, esses descontos serão aplicados automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Posso mover/migrar meu cluster entre locatários do Azure?

O comando `az aks update-credentials` pode ser usado para mover um cluster AKS entre locatários do Azure. Siga as instruções em [escolher para atualizar ou criar uma entidade de serviço](https://docs.microsoft.com/azure/aks/update-credentials) e, em seguida, [atualize o cluster AKs com novas credenciais](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Posso mover/migrar meu cluster entre assinaturas?

No momento, não há suporte para a movimentação de clusters entre assinaturas.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Posso mover meus clusters do AKS da assinatura atual do Azure para outra? 

Não há suporte para mover o cluster AKS e os recursos associados entre as assinaturas do Azure.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Por que minha exclusão de cluster está demorando tanto tempo? 

A maioria dos clusters é excluída mediante solicitação do usuário; em alguns casos, especialmente onde os clientes estão trazendo seu próprio grupo de recursos, ou fazer a exclusão de tarefas de RG cruzada pode levar mais tempo ou falhar. Se você tiver um problema com as exclusões, verifique se você não tem bloqueios no RG, se todos os recursos fora do RG estão desassociados do RG, etc.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se eu tiver Pod/implantações no estado ' NodeLost ' ou ' Unknown ', ainda posso atualizar o meu cluster?

Você pode, mas AKS não recomenda isso. As atualizações devem idealmente ser executadas quando o estado do cluster for conhecido e íntegro.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se eu tiver um cluster com um ou mais nós em um estado não íntegro ou for desligado, posso executar uma atualização?

Não, exclua/remova todos os nós em um estado de falha ou removidos do cluster antes de atualizar.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Executei uma exclusão de cluster, mas veja o erro `[Errno 11001] getaddrinfo failed` 

Normalmente, isso é causado por usuários que têm um ou mais NSGs (grupos de segurança de rede) que ainda estão em uso e associados ao cluster.  Remova-os e tente excluir novamente.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Eu executei uma atualização, mas agora meus pods estão em loops de falha, e as investigações de prontidão falham?

Confirme se sua entidade de serviço não expirou.  Consulte: [entidade de serviço AKs](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [credenciais de atualização do AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Meu cluster estava funcionando, mas repentinamente não pode provisionar balanceadores de carga, PVCs de montagem, etc.? 

Confirme se sua entidade de serviço não expirou.  Consulte: [entidade de serviço AKs](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [credenciais de atualização do AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Posso usar as APIs do conjunto de dimensionamento de máquinas virtuais para dimensionar manualmente?

Não, não há suporte para operações de escala usando as APIs do conjunto de dimensionamento de máquinas virtuais. Use as APIs AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Posso usar conjuntos de dimensionamento de máquinas virtuais para dimensionar manualmente para 0 nós?

Não, não há suporte para operações de escala usando as APIs do conjunto de dimensionamento de máquinas virtuais.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Posso parar ou desalocar todas as minhas VMs?

Embora o AKS tenha mecanismos de resiliência para resistir a essa configuração e recuperá-la, essa não é uma configuração recomendada.

## <a name="can-i-use-custom-vm-extensions"></a>Posso usar extensões de VM personalizadas?

Nenhum AKS é um serviço gerenciado e não há suporte para a manipulação dos recursos de IaaS. Para instalar componentes personalizados, etc. Aproveite as APIs e os mecanismos do kubernetes. Por exemplo, aproveite o DaemonSets para instalar os componentes necessários.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
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
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
