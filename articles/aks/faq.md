---
title: Perguntas frequentes para o Serviço Azure Kubernetes (AKS)
description: Encontre respostas para algumas das perguntas comuns sobre o Serviço Azure Kubernetes (AKS).
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: 136f79df43bcc1730f187980df8726d693390faa
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300931"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas mais frequentes acerca do Azure Kubernetes Service (AKS)

Este artigo aborda questões frequentes sobre o Serviço Azure Kubernetes (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Quais as regiões de Azure que atualmente fornecem AKS?

Para obter uma lista completa das regiões disponíveis, consulte [as regiões AKS e a disponibilidade.][aks-regions]

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Posso espalhar um aglomerado AKS por regiões?

Não. Os aglomerados AKS são recursos regionais e não podem abranger regiões. Consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres][bcdr-bestpractices] para obter orientações sobre como criar uma arquitetura que inclua várias regiões.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Posso espalhar um cluster AKS através de zonas de disponibilidade?

Yes. Você pode implementar um cluster AKS em uma ou mais [zonas de disponibilidade][availability-zones] em [regiões que as suportam.][az-regions]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitar quem tem acesso ao servidor API da Kubernetes?

Yes. Existem duas opções para limitar o acesso ao servidor API:

- Utilize [gamas IP autorizadas do servidor API][api-server-authorized-ip-ranges] se pretender manter um ponto final público para o servidor API, mas restringir o acesso a um conjunto de gamas IP fidedignas.
- Utilize [um cluster privado][private-clusters] se pretender limitar o servidor API para estar acessível *apenas* a partir da sua rede virtual.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Posso ter diferentes tamanhos de VM num único aglomerado?

Sim, você pode usar diferentes tamanhos de máquina virtual no seu cluster AKS criando [várias piscinas de nós.][multi-node-pools]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós de agente AKS?

O Azure aplica automaticamente patches de segurança aos nós Linux no seu cluster num horário noturno. No entanto, é responsável por garantir que esses nós Linux são reiniciados conforme necessário. Tem várias opções para reiniciar os nóns:

- Manualmente, através do portal Azure ou do Azure CLI.
- Atualizando o seu cluster AKS. O cluster atualiza os [nós de cordon e drenagem][cordon-drain] automaticamente e, em seguida, traz um novo nó on-line com a mais recente imagem Ubuntu e uma nova versão patch ou uma versão menor de Kubernetes. Para obter mais informações, consulte [atualizar um cluster AKS][aks-upgrade].
- Ao usar [Kured,](https://github.com/weaveworks/kured)um daemon de reinicialização de código aberto para Kubernetes. Kured funciona como Um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitoriza cada nó para a presença de um ficheiro que indica que é necessário um reboot. Em todo o cluster, os reboots de SO são geridos pelo mesmo [processo de cordão e drenagem][cordon-drain] como um upgrade de cluster.

Para obter mais informações sobre a utilização de kured, consulte [Aplicar atualizações de segurança e kernel aos nós em AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Os acenos do Servidor do Windows

Para os nós do Windows Server, o Windows Update não é executado automaticamente e aplica as atualizações mais recentes. Numa programação regular em torno do ciclo de lançamento do Windows Update e do seu próprio processo de validação, deverá realizar uma atualização no cluster e no conjunto de nós do Windows Server no seu cluster AKS. Este processo de atualização cria nós que executam a mais recente imagem e patches do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre este processo, consulte [a atualização de um conjunto de nós em AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com AKS?

A AKS baseia-se numa série de recursos de infraestrutura Azure, incluindo conjuntos de escala de máquinas virtuais, redes virtuais e discos geridos. Isto permite-lhe alavancar muitas das capacidades centrais da plataforma Azure dentro do ambiente gerido de Kubernetes fornecido pela AKS. Por exemplo, a maioria dos tipos de máquinas virtuais Azure podem ser usados diretamente com reservas AKS e Azure podem ser usados para receber descontos nesses recursos automaticamente.

Para permitir esta arquitetura, cada implantação AKS abrange dois grupos de recursos:

1. Cria-se o primeiro grupo de recursos. Este grupo contém apenas o recurso de serviço Kubernetes. O fornecedor de recursos AKS cria automaticamente o segundo grupo de recursos durante a implementação. Um exemplo do segundo grupo de recursos é *MC_myResourceGroup_myAKSCluster_eastus*. Para obter informações sobre como especificar o nome deste segundo grupo de recursos, consulte a secção seguinte.
1. O segundo grupo de recursos, conhecido como grupo de *recursos de nó,* contém todos os recursos de infraestrutura associados ao cluster. Estes recursos incluem os VMs de nó de Kubernetes, networking virtual e armazenamento. Por predefinição, o grupo de recursos de nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. A AKS elimina automaticamente o recurso de nó sempre que o cluster é eliminado, pelo que deve ser utilizado apenas para recursos que partilhem o ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Posso dar o meu próprio nome para o grupo de recursos de nó AKS?

Yes. Por padrão, a AKS nomeará o grupo de recursos de nó *MC_resourcegroupname_clustername_location*, mas também pode fornecer o seu próprio nome.

Para especificar o nome do seu próprio grupo de recursos, instale a versão de extensão Azure CLI [de pré-visualização aks-preview][aks-preview-cli] *0.3.2* ou posterior. Quando criar um cluster AKS utilizando os [az aks criar][az-aks-create] comando, use o parâmetro *grupo de recursos-node* e especifique um nome para o grupo de recursos. Se [utilizar um modelo de Gestor de Recursos Azure][aks-rm-template] para implantar um cluster AKS, pode definir o nome do grupo de recursos utilizando a propriedade *nodeResourceGroup.*

* O grupo de recursos secundários é automaticamente criado pelo fornecedor de recursos Azure na sua própria subscrição.
* Só pode especificar um nome de grupo de recursos personalizados quando estiver a criar o cluster.

Ao trabalhar com o grupo de recursos de nó, lembre-se que não pode:

* Especifique um grupo de recursos existente para o grupo de recursos de nó.
* Especifique uma subscrição diferente para o grupo de recursos de nó.
* Mude o nome do grupo de recursos do nó após a criação do cluster.
* Especificar os nomes dos recursos geridos dentro do grupo de recursos de nó.
* Modifique ou elimine as tags criadas pelo Azure de recursos geridos dentro do grupo de recursos do nó. (Ver informações adicionais na secção seguinte.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar etiquetas e outras propriedades dos recursos AKS no grupo de recursos de nó?

Se modificar ou eliminar tags criadas pelo Azure e outras propriedades de recursos no grupo de recursos de nó, poderá obter resultados inesperados, tais como erros de escala e de atualização. A AKS permite-lhe criar e modificar tags personalizadas criadas pelos utilizadores finais. É possível que queira criar ou modificar tags personalizadas, por exemplo, para atribuir uma unidade de negócio ou um centro de custos. Isto pode ser conseguido através da criação de Políticas Azure com âmbito no grupo de recursos geridos.

No entanto, modificar quaisquer **tags criadas pelo Azure** em recursos no âmbito do grupo de recursos de nó no cluster AKS é uma ação não apoiada que quebra o objetivo de nível de serviço (SLO). Para mais informações, consulte [a AKS oferecer um acordo de nível de serviço?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Que controladores de admissão kubernetes suportam a AKS? Os controladores de admissão podem ser adicionados ou removidos?

A AKS suporta os seguintes [controladores de admissão:][admission-controllers]

- *Espaço nomeLifecycle*
- *LimiteRanger*
- *Contagem de serviços*
- *Classe defaultstorage*
- *Padrão DeleraçãoSegundos*
- *MutatingAdmissionWebhook*
- *Validação DoWebhook daAdmission*
- *ResourceQuota*

Atualmente, não é possível modificar a lista de controladores de admissão em AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Posso usar webhooks de controlador de admissão na AKS?

Sim, pode usar webhooks do controlador de admissão na AKS. Recomenda-se que exclua espaços de nomes AKS internos marcados com a **etiqueta do plano de controlo.** Por exemplo, adicionando o abaixo à configuração webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Os webhooks do controlador de admissão podem impactar o sistema kube e os espaços internos de nomeS AKS?

Para proteger a estabilidade do sistema e evitar que os controladores de admissão personalizados impactem nos serviços internos no sistema kube, o Namespace AKS dispõe de um Executor de **Admissões**, que exclui automaticamente o sistema kube e os espaços de nome internos AKS. Este serviço garante que os controladores de admissão personalizados não afetam os serviços em execução no sistema kube.

Se tiver um caso crítico de uso para ter algo implantado no sistema kube (não recomendado) que você precisa ser coberto pelo seu webhook de admissão personalizado, você pode adicionar o rótulo ou anotação abaixo para que o Executor de Admissões o ignore.

Etiqueta: ```"admissions.enforcer/disabled": "true"``` ou Anotação:```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault está integrado com a AKS?

AKS não está integrada de forma nativa com o Azure Key Vault. No entanto, o [fornecedor Azure Key Vault para a CSI Secrets Store][csi-driver] permite a integração direta de cápsulas Kubernetes para segredos key vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar os contentores do Windows Server na AKS?

Sim, os contentores do Windows Server estão disponíveis em AKS. Para executar os contentores do Windows Server em AKS, cria uma piscina de nó que executa o Windows Server como o SISTEMA convidado. Os contentores do Windows Server só podem utilizar o Windows Server 2019. Para começar, consulte [Criar um cluster AKS com um conjunto de nós do Windows Server][aks-windows-cli].

O suporte do Windows Server para o conjunto de nós inclui algumas limitações que fazem parte do projeto Do Windows Server a montante em Kubernetes. Para obter mais informações sobre estas limitações, consulte [os recipientes do Windows Server em limitações AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>A AKS oferece um acordo de nível de serviço?

A AKS fornece garantias SLA como um complemento opcional em funcionalidade com [Uptime SLA][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Posso aplicar descontos de reserva Azure aos meus nós de agente AKS?

Os nós de agente AKS são faturados como máquinas virtuais Azure padrão, por isso, se adquiriu [reservas Azure][reservation-discounts] para o tamanho VM que está a usar em AKS, esses descontos são automaticamente aplicados.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Posso mover/migrar o meu aglomerado entre inquilinos do Azure?

O `az aks update-credentials` comando pode ser usado para mover um aglomerado AKS entre inquilinos de Azure. Siga as instruções em [Escolha atualizar ou criar um principal de serviço](https://docs.microsoft.com/azure/aks/update-credentials) e, em seguida, atualizar o cluster [aks com novas credenciais](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Posso mover/migrar o meu cluster entre assinaturas?

Atualmente, o movimento de clusters entre subscrições não é suportado.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Posso mover os meus clusters AKS da atual assinatura do Azure para outro? 

Mover o seu cluster AKS e os seus recursos associados entre subscrições Azure não é suportado.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Porque é que o meu grupo está a demorar tanto tempo? 

A maioria dos clusters são eliminados a pedido do utilizador; em alguns casos, especialmente quando os clientes estão trazendo o seu próprio Grupo de Recursos, ou fazendo a supressão de tarefas cross-RG pode levar tempo adicional ou falhar. Se tiver algum problema com as eliminações, verifique duas vezes se não tem fechaduras no RG, que quaisquer recursos fora do RG estão dissociados do RG, etc.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se eu tiver pod/implantações no estado 'NodeLost' ou 'Unknown' ainda posso atualizar o meu cluster?

Pode, mas a AKS não recomenda isto. As atualizações devem ser realizadas idealmente quando o estado do cluster é conhecido e saudável.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se eu tiver um aglomerado com um ou mais nós em estado insalubre ou desligado, posso fazer uma atualização?

Não, por favor, elimine/remova quaisquer nós num estado falhado ou de outra forma removido do cluster antes da atualização.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Corri um aglomerado apagar, mas ver o erro`[Errno 11001] getaddrinfo failed` 

Mais comummente, isto é causado por utilizadores que têm um ou mais Grupos de Segurança de Rede (NSGs) ainda em uso e associados ao cluster.  Por favor, retire-os e tente apagar novamente.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Fiz um upgrade, mas agora as minhas cápsulas estão em circuitos de colisão, e as sondas de prontidão falham?

Por favor, confirme que o seu diretor de serviço não expirou.  Consulte: Credenciais de atualização [de serviço AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>O meu aglomerado estava a funcionar, mas de repente não pode abastecer LoadBalancers, montar PVCs, etc.? 

Por favor, confirme que o seu diretor de serviço não expirou.  Consulte: Credenciais de atualização [de serviço AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Posso utilizar as APIs de escala de máquina virtual para escalar manualmente?

Não, as operações de escala utilizando as APIs de escala de máquina virtual não são suportadas. Utilize as APIs AKS `az aks scale` ().

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Posso usar conjuntos de balança de máquinas virtuais para escalar manualmente até 0 nós?

Não, as operações de escala utilizando as APIs de escala de máquina virtual não são suportadas.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Posso parar ou desatribuer todos os meus VMs?

Embora a AKS tenha mecanismos de resiliência para resistir a tal config e recuperar dele, esta não é uma configuração recomendada.

## <a name="can-i-use-custom-vm-extensions"></a>Posso usar extensões VM personalizadas?

No AKS é um serviço gerido, e a manipulação dos recursos da IaaS não é suportada. Para instalar componentes personalizados, etc. por favor, aproveite as APIs e mecanismos de Kubernetes. Por exemplo, aproveitar os DaemonSets para instalar os componentes necessários.

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
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/