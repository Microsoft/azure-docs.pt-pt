---
title: Perguntas frequentes para o Serviço Azure Kubernetes (AKS)
description: Encontre respostas a algumas das perguntas comuns sobre o Serviço Azure Kubernetes (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: f91fe1c63430a0eac23cf9cbc184babb6dd5f7a4
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106089"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas mais frequentes acerca do Azure Kubernetes Service (AKS)

Este artigo aborda questões frequentes sobre o Serviço Azure Kubernetes (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Quais as regiões azure que atualmente fornecem AKS?

Para obter uma lista completa das regiões disponíveis, consulte [as regiões aks e a disponibilidade.][aks-regions]

## <a name="does-aks-support-node-autoscaling"></a>A AKS suporta a autoscalcificação do nó?

Sim, a capacidade de escalar automaticamente os nós de agente horizontalmente no AKS está atualmente disponível na pré-visualização. Consulte automaticamente um cluster para atender às exigências de [aplicação em AKS][aks-cluster-autoscaler] para obter instruções. A autoscalcificação AKS baseia-se no [autoscaler Kubernetes][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Posso colocar AKS na minha rede virtual existente?

Sim, pode implantar um cluster AKS numa rede virtual existente utilizando a funcionalidade avançada de [networking.][aks-advanced-networking]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitar quem tem acesso ao servidor Kubernetes API?

Sim, pode limitar o acesso ao servidor Kubernetes API utilizando [intervalos IP autorizados][api-server-authorized-ip-ranges]do servidor API .

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Posso tornar o servidor Kubernetes API acessível apenas dentro da minha rede virtual?

Não neste momento, mas isto está planeado. Você pode acompanhar o progresso no [repo AKS GitHub][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Posso ter tamanhos vm diferentes num único aglomerado?

Sim, você pode usar diferentes tamanhos de máquina virtual no seu cluster AKS criando [várias piscinas][multi-node-pools]de nós.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente AKS?

O Azure aplica automaticamente patches de segurança aos nós linux do seu cluster num horário noturno. No entanto, é responsável por garantir que os nós linux sejam reiniciados conforme necessário. Tem várias opções para reiniciar os nódosos:

- Manualmente, através do portal Azure ou do Azure CLI.
- Melhorando o seu cluster AKS. O cluster atualiza os nós de [cordão e drenagem][cordon-drain] automaticamente e, em seguida, traz um novo nó on-line com a mais recente imagem Ubuntu e uma nova versão de patch ou uma versão kubernetes menor. Para mais informações, consulte [Atualizar um cluster AKS][aks-upgrade].
- Ao usar [Kured,](https://github.com/weaveworks/kured)um daemon de reinicialização de código aberto para Kubernetes. Kured funciona como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitoriza cada nó para a presença de um ficheiro que indica que é necessário reiniciar. Através do cluster, as reinicializações de SO são geridas pelo mesmo processo de [cordão e drenagem][cordon-drain] que uma atualização de cluster.

Para obter mais informações sobre o uso de kured, consulte Aplicar atualizações de [segurança e kernel em nós em AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Nós do Servidor do Windows

No caso dos nós do Windows Server (atualmente em pré-visualização no AKS), o Windows Update não é executado automaticamente e aplica as atualizações mais recentes. Numa programação regular em torno do ciclo de lançamento do Windows Update e do seu próprio processo de validação, deverá efetuar uma atualização no cluster e no conjunto de nós do Windows Server no seu cluster AKS. Este processo de upgrade cria nós que executam a mais recente imagem e patches do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre este processo, consulte [Atualização de um conjunto][nodepool-upgrade]de nós em AKS .

## <a name="why-are-two-resource-groups-created-with-aks"></a>Porque é que dois grupos de recursos são criados com AKS?

A AKS baseia-se em vários recursos de infraestrutura Azure, incluindo conjuntos de escala de máquinas virtuais, redes virtuais e discos geridos. Isto permite-lhe alavancar muitas das capacidades centrais da plataforma Azure dentro do ambiente gerido por Kubernetes fornecido pela AKS. Por exemplo, a maioria dos tipos de máquinas virtuais Azure podem ser usados diretamente com AKS e Reservas Azure podem ser usados para receber descontos nesses recursos automaticamente.

Para permitir esta arquitetura, cada implantação AKS abrange dois grupos de recursos:

1. Cria-se o primeiro grupo de recursos. Este grupo contém apenas o recurso de serviço Kubernetes. O fornecedor de recursos AKS cria automaticamente o segundo grupo de recursos durante a implantação. Um exemplo do segundo grupo de recursos é *MC_myResourceGroup_myAKSCluster_eastus.* Para obter informações sobre como especificar o nome deste segundo grupo de recursos, consulte a secção seguinte.
1. O segundo grupo de recursos, conhecido como o grupo de recursos do *nó,* contém todos os recursos de infraestrutura associados ao cluster. Estes recursos incluem os VMs do nó Kubernetes, networking virtual e armazenamento. Por padrão, o grupo de recursos do nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. O AKS elimina automaticamente o recurso do nó sempre que o cluster é eliminado, pelo que só deve ser utilizado para recursos que partilhem o ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Posso fornecer o meu próprio nome para o grupo de recursos do nó AKS?

Sim. Por padrão, a AKS nomeará o grupo de recursos do nó *MC_resourcegroupname_clustername_location,* mas também pode fornecer o seu próprio nome.

Para especificar o nome do seu próprio grupo de recursos, instale a versão de extensão Azure CLI de [pré-visualização][aks-preview-cli] de aks *0.3.2* ou posterior. Quando criar um cluster AKS utilizando as [aks az criar][az-aks-create] comando, use o parâmetro *do grupo de recursos-nó* e especifique um nome para o grupo de recursos. Se [utilizar um modelo][aks-rm-template] de Gestor de Recursos Azure para implementar um cluster AKS, pode definir o nome do grupo de recursos utilizando a propriedade do *NodeResourceGroup.*

* O grupo de recursos secundários é automaticamente criado pelo fornecedor de recursos Azure na sua própria subscrição.
* Só pode especificar um nome de grupo de recursos personalizados quando estiver a criar o cluster.

Enquanto trabalha com o grupo de recursos do nó, lembre-se que não pode:

* Especifique um grupo de recursos existente para o grupo de recursos do nó.
* Especifique uma subscrição diferente para o grupo de recursos do nó.
* Mude o nome do grupo de recursos do nó após a criação do cluster.
* Especifique os nomes dos recursos geridos dentro do grupo de recursos do nó.
* Modificar ou eliminar etiquetas de recursos geridos dentro do grupo de recursos do nó. (Consulte informações adicionais na secção seguinte.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar etiquetas e outras propriedades dos recursos AKS no grupo de recursos do nó?

Se modificar ou eliminar tags criadas pelo Azure e outras propriedades de recursos no grupo de recursos do nó, poderá obter resultados inesperados, tais como erros de escala e upgrade. O AKS permite-lhe criar e modificar etiquetas personalizadas. Pode querer criar ou modificar etiquetas personalizadas, por exemplo, para atribuir uma unidade de negócio ou centro de custos. Ao modificar os recursos sob o grupo de recursos do nó no cluster AKS, você quebra o objetivo de nível de serviço (SLO). Para mais informações, consulte a [AKS oferece um acordo de nível de serviço?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Que controladores de admissão kubernetes suportam aks? Os controladores de admissão podem ser adicionados ou removidos?

A AKS suporta os [seguintes controladores de admissão:][admission-controllers]

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiçoConta*
- *Classe de Armazenamento Padrão*
- *PredefiniçãoDeS*
- *MutatingAdmissionWebhook*
- *Validação AdmissionWebhook*
- *Quota de Recursos*

Atualmente, não pode modificar a lista de controladores de admissão no AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Posso usar webhooks controladores de admissão no AKS?

Sim, pode usar webhooks controladores de admissão no AKS. Recomenda-se que exclua espaços de nome aks internos marcados com a etiqueta de **plano de controlo.** Por exemplo, adicionando o abaixo à configuração do webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Os webhooks do controlador de admissão podem ter impacto no sistema kube e nos espaços de nome saldação internos aks?

Para proteger a estabilidade do sistema e evitar que os controladores de admissão personalizados afetem os serviços internos no sistema kube, o espaço de nome AKS tem um Executor de **Admissões,** que exclui automaticamente o sistema de kube e os espaços de nome interno AKS. Este serviço garante que os controladores de admissão personalizados não afetam os serviços em execução no sistema kube.

Se tiver um caso de utilização crítica para ter algo implantado no sistema kube (não recomendado) que necessita de ser coberto pelo seu webhook de admissão personalizada, pode adicionar a etiqueta ou anotação abaixo para que o Executor de Admissões o ignore.

Etiqueta: ```"admissions.enforcer/disabled": "true"``` ou Anotação:```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Cofre chave Azure está integrado com aks?

A AKS não está atualmente integrada com o Cofre chave Azure. No entanto, o [projeto Azure Key Vault FlexVolume para Kubernetes][keyvault-flexvolume] permite a integração direta de cápsulas Kubernetes para segredos key vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar os contentores do Windows Server no AKS?

Sim, os recipientes do Windows Server estão disponíveis na pré-visualização. Para executar os recipientes do Windows Server no AKS, cria-se uma piscina de nós que executa o Windows Server como o OS de hóspedes. Os recipientes do Windows Server só podem utilizar o Windows Server 2019. Para começar, consulte [Criar um cluster AKS com uma piscina][aks-windows-cli]de nó do Windows Server .

O suporte do Windows Server para piscina de nó inclui algumas limitações que fazem parte do Windows Server a montante no projeto Kubernetes. Para obter mais informações sobre estas limitações, consulte [os recipientes do Windows Server nas limitações aks][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>A AKS oferece um acordo de nível de serviço?

Num acordo de nível de serviço (SLA), o prestador aceita reembolsar o cliente pelo custo do serviço se o nível de serviço publicado não for cumprido. Uma vez que a AKS é gratuita, não há custos disponíveis para reembolsar, pelo que a AKS não tem SLA formal. No entanto, a AKS procura manter a disponibilidade de pelo menos 99,5% para o servidor Kubernetes API.

É importante reconhecer a distinção entre a disponibilidade de serviço AKS que se refere ao tempo de uptime do plano de controlo Kubernetes e a disponibilidade da sua carga de trabalho específica que está a funcionar em Máquinas Virtuais Azure. Embora o plano de controlo possa não estar disponível se o plano de controlo não estiver pronto, as cargas de trabalho do seu cluster em funcionamento em VMs Azure ainda podem funcionar. Dado que os VMs Azure são recursos pagos, são apoiados por um SLA financeiro. Leia [aqui para mais detalhes](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) sobre o Azure VM SLA e como aumentar essa disponibilidade com [funcionalidades][availability-zones]como Zonas de Disponibilidade .

## <a name="why-cant-i-set-maxpods-below-30"></a>Por que não posso definir maxPods abaixo dos 30?

No AKS, pode `maxPods` definir o valor quando criar o cluster utilizando os modelos Azure CLI e Azure Resource Manager. No entanto, tanto a Kubenet como a Azure CNI requerem um *valor mínimo* (validado no momento da criação):

| Redes | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Rio Kubenet | 30 | 110 |

Como o AKS é um serviço gerido, implementamos e gerimos add-ons e cápsulas como parte do cluster. No passado, os utilizadores `maxPods` poderiam definir um valor inferior ao valor que as cápsulas geridas necessárias para executar (por exemplo, 30). O AKS calcula agora o número mínimo de cápsulas utilizando esta fórmula: (maxPods ou (maxPods * vm_count)) > cápsulas de adição geridas no mínimo.

Os utilizadores não podem `maxPods` anular a validação mínima.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Posso aplicar descontos de reserva Azure aos meus nós de agente AKS?

Os nós de agente AKS são faturados como máquinas virtuais Standard Azure, por isso, se adquiriu [reservas Azure][reservation-discounts] para o tamanho VM que está a usar no AKS, esses descontos são automaticamente aplicados.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Posso mover/migrar o meu aglomerado entre inquilinos do Azure?

O `az aks update-credentials` comando pode ser usado para mover um aglomerado AKS entre inquilinos azure. Siga as instruções em [Escolha atualizar ou criar um diretor](https://docs.microsoft.com/azure/aks/update-credentials) de serviço e, em seguida, [atualize o cluster aks com novas credenciais](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Posso mover/migrar o meu cluster entre assinaturas?

Atualmente, o movimento de clusters entre subscrições não é suportado.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Posso mover os meus clusters AKS da subscrição azul atual para outra? 

A movimentação do seu cluster AKS e os seus recursos associados entre as subscrições do Azure não são suportados.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Porque é que o meu agrupamento está a apagar tanto tempo? 

A maioria dos clusters são eliminados a pedido do utilizador; em alguns casos, especialmente quando os clientes estão trazendo o seu próprio Grupo de Recursos, ou fazendo a eliminação de tarefas cross-RG pode levar tempo adicional ou falhar. Se tiver algum problema com exclusões, verifique duas vezes se não tem fechaduras no RG, que quaisquer recursos fora do RG estão dissociados do RG, etc.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se eu tiver pod/implantações no estado 'NodeLost' ou 'Unknown' ainda posso atualizar o meu cluster?

Pode, mas a AKS não recomenda isto. As atualizações devem ser realizadas idealmente quando o estado do cluster é conhecido e saudável.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se eu tiver um aglomerado com um ou mais nós em estado não saudável ou desligado, posso fazer uma atualização?

Não, por favor, elimine/remova quaisquer nós em estado falhado ou removido do cluster antes da atualização.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Corri um aglomerado apagar, mas ver o erro`[Errno 11001] getaddrinfo failed` 

Mais comummente, isto é causado por utilizadores que têm um ou mais Grupos de Segurança de Rede (NSGs) ainda em uso e associados ao cluster.  Por favor, remova-os e tente apagar novamente.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Fiz uma atualização, mas agora as minhas cápsulas estão em loops de colisão, e as sondas de prontidão falham?

Por favor, confirme que o seu diretor de serviço não expirou.  Consulte: [Diretor de serviço AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e credenciais de [atualização AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>O meu cluster estava a funcionar, mas de repente não pode fornecer LoadBalancers, mount PVCs, etc.? 

Por favor, confirme que o seu diretor de serviço não expirou.  Consulte: [Diretor de serviço AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e credenciais de [atualização AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Posso usar a escala virtual de apis para escalar manualmente?

Não, as operações de escala utilizando as APIs de escala de máquina virtual não são suportadas. Utilize as APIs`az aks scale`AKS ().

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Posso usar conjuntos de escala de máquinas virtuais para escalar manualmente até 0 nós?

Não, as operações de escala utilizando as APIs de escala de máquina virtual não são suportadas.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Posso parar ou desalocar todos os meus VMs?

Embora a AKS tenha mecanismos de resiliência para resistir a tal config e recuperar dele, esta não é uma configuração recomendada.

## <a name="can-i-use-custom-vm-extensions"></a>Posso usar extensões VM personalizadas?

Nenhuma AKS é um serviço gerido, e a manipulação dos recursos iaaS não é apoiada. Para instalar componentes personalizados, etc. por favor, aproveite as APIs e mecanismos kubernetes. Por exemplo, aproveite os DaemonSets para instalar os componentes necessários.

<!-- LINKS - internal -->

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
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
