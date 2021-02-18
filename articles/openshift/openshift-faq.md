---
title: Perguntas frequentes para Azure Red Hat OpenShift
description: Aqui estão as respostas a perguntas comuns sobre o Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a3721083e48774963cd761178abdb552c93b15c7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634350"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift FAQ

Este artigo responde a perguntas frequentes (PERGUNTAS Frequentes) sobre o Microsoft Azure Red Hat OpenShift.

## <a name="installation-and-upgrade"></a>Instalação e atualização

### <a name="which-azure-regions-are-supported"></a>Quais as regiões de Azure que são apoiadas?

Para obter uma lista de regiões apoiadas para O Chapéu Vermelho Azure OpenShift 4.x, consulte [as regiões disponíveis.](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)

Para obter uma lista de regiões apoiadas para o Azure Red Hat OpenShift 3.11, consulte [os produtos disponíveis por região.](supported-resources.md#azure-regions)

### <a name="what-virtual-machine-sizes-can-i-use"></a>Que tamanhos de máquina virtual posso usar?

Para obter uma lista de tamanhos de máquinas virtuais suportados para Azure Red Hat OpenShift 4, consulte [recursos suportados para Azure Red Hat OpenShift 4](support-policies-v4.md).

Para obter uma lista de tamanhos de máquinas virtuais suportados para Azure Red Hat OpenShift 3.11, consulte [recursos suportados para O Chapéu Vermelho Azure OpenShift 3.11](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Qual é o número máximo de cápsulas num aglomerado Azure Red Hat OpenShift?  Qual é o número máximo de cápsulas por nó em Azure Red Hat OpenShift?

O número real de cápsulas suportadas depende da memória, CPU e requisitos de armazenamento de uma aplicação.

O Azure Red Hat OpenShift 4.x tem um limite de 250 pod-per-node e um limite de nó de 100 computação. Isto cobre o número máximo de cápsulas suportadas num cluster para 250 &times; 100 = 25.000.

O Azure Red Hat OpenShift 3.11 tem um limite de 50 pod-per-node e um limite de 20 nódulos computacional. Isto cobre o número máximo de cápsulas suportadas num cluster para 50 &times; 20 = 1.000.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Um cluster pode ter nóns computacional em várias regiões do Azure?

N.º Todos os nós de um aglomerado Azure Red Hat OpenShift devem ter origem na mesma região de Azure.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Um cluster pode ser implantado em várias zonas de disponibilidade?

Sim. Isto acontece automaticamente se o seu cluster for implantado numa região do Azure que suporte zonas de disponibilidade. Para mais informações, consulte [as zonas de disponibilidade.](../availability-zones/az-overview.md#availability-zones)

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Os nós dos aviões de controlo são abstraídos como estão com o Serviço Azure Kubernetes (AKS)?

N.º Todos os recursos, incluindo os nós principais do cluster, funcionam na subscrição do seu cliente. Estes tipos de recursos são colocados num grupo de recursos apenas de leitura.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>O cluster reside numa subscrição de cliente? 

A Aplicação Gerida Azure vive num Grupo de Recursos Bloqueado com a subscrição do cliente. Os clientes podem ver objetos nesse grupo de recursos, mas não modificá-los.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Existe algum elemento no Azure Red Hat OpenShift partilhado com outros clientes? Ou tudo é independente?

Cada cluster Azure Red Hat OpenShift é dedicado a um determinado cliente e vive dentro da subscrição do cliente. 

### <a name="are-infrastructure-nodes-available"></a>Os nós de infraestrutura estão disponíveis?

Nos clusters Azure Red Hat OpenShift 4.x, os nós de infraestrutura não estão disponíveis atualmente.

Nos clusters Azure Red Hat OpenShift 3.11, os nós de infraestrutura são incluídos por padrão.

## <a name="how-do-i-handle-cluster-upgrades"></a>Como lido com atualizações de cluster?

Para obter informações sobre atualizações, manutenção e versões suportadas, consulte o guia de [ciclo de vida](support-lifecycle.md)de suporte .

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Como é que o sistema operativo anfitrião e o software OpenShift serão atualizados?

Os sistemas operativos do anfitrião e o software OpenShift são atualizados à medida que o Azure Red Hat OpenShift consome versões de lançamento menores e patches a partir da plataforma de contentores OpenShift a montante.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Qual é o processo para reiniciar o nó atualizado?

Os nós são reiniciados como parte de uma atualização.

## <a name="cluster-operations"></a>Operações de cluster

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Posso usar o Prometeu para monitorizar as minhas aplicações?

Prometheus vem pré-instalado e configurado para aglomerados Azure Red Hat OpenShift 4.x. Leia mais sobre [a monitorização do cluster.](https://docs.openshift.com/container-platform/4.6/operators/operator_sdk/osdk-monitoring-prometheus.html)

Para os agrupamentos Azure Red Hat OpenShift 3.11, pode implantar Prometheus no seu espaço de nome e monitorizar aplicações no seu espaço de nome. Para obter mais informações, consulte [a instância Prometeu em Azure Red Hat OpenShift cluster](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Posso usar Prometeu para monitorizar métricas relacionadas com a saúde e capacidade do cluster?

Em Azure Red Hat OpenShift 4.x: Sim.

Em Azure Red Hat OpenShift 3.11: Não.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Os registos de VM subjacentes podem ser transmitidos para um sistema de análise de registo de clientes?

Os registos dos VM subjacentes são tratados pelo serviço gerido e não estão expostos aos clientes.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>Como pode um cliente ter acesso a métricas como CPU/memória ao nível do nó para tomar medidas à escala, depurar problemas, etc.? Não consigo correr o top kubectl num aglomerado Azure Red Hat OpenShift.

Para os clusters Azure Red Hat OpenShift 4.x, a consola web OpenShift contém todas as métricas ao nível do nó. Para obter mais informações, consulte a documentação do Chapéu Vermelho sobre [a visualização de informações sobre o cluster.](https://docs.openshift.com/container-platform/4.6/web_console/using-dashboard-to-get-cluster-information.html)

Para os clusters Azure Red Hat OpenShift 3.11, os clientes podem aceder às métricas CPU/Memória ao nível do nó utilizando o comando `oc adm top nodes` ou com o papel de cluster de `kubectl top nodes` administração do cliente. Os clientes também podem aceder às métricas CPU/Memória de `pods` com o comando ou `oc adm top pods` `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Se aumentarmos a implementação, como é que os domínios de falhas do Azure mapeiam a colocação do pod para garantir que todas as cápsulas para um serviço não são eliminadas por uma falha num único domínio de falha?

Existem, por defeito, cinco domínios de avaria ao utilizar conjuntos de escala de máquina virtual em Azure. Cada instância de máquina virtual num conjunto de escala será colocada num destes domínios de avaria. Isto garante que as aplicações implantadas nos nós computacional num cluster serão colocadas em domínios de avaria separados.

Para obter mais informações, consulte [escolher o número certo de domínios de avaria para conjunto de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Há alguma maneira de gerir a colocação do casulo?

Os clientes têm a capacidade de obter nós e ver etiquetas como o administrador do cliente. Isto fornecerá uma maneira de direcionar qualquer VM no conjunto de escala.

Deve ser utilizado um cuidado ao utilizar etiquetas específicas:

- O nome de hospedeiro não deve ser utilizado. O nome anfitrião é rodado frequentemente com atualizações e atualizações e é garantido que vai ser alterado.
- Se o cliente tiver um pedido de etiquetas específicas ou uma estratégia de implantação, isso poderia ser realizado, mas exigiria esforços de engenharia e não é apoiado hoje.

Para obter mais informações, consulte [a colocação do casulo de controlo.](https://docs.openshift.com/container-platform/4.6/nodes/scheduling/nodes-scheduler-about.html)

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>O registo de imagens está disponível externamente para que eu possa usar ferramentas como o Jenkins?

Para agrupamentos de 4.x, é necessário expor um registo seguro e configurar a autenticação. Para mais informações, consulte a seguinte documentação do Chapéu Vermelho:

- [Expondo um registo](https://docs.openshift.com/container-platform/4.6/registry/securing-exposing-registry.html)
- [Acesso ao registo](https://docs.openshift.com/container-platform/4.6/registry/accessing-the-registry.html)

Para 3.11 clusters, o registo de imagem docker está disponível. O registo do Docker está disponível a partir de `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . Também pode utilizar o Registo do Contentor Azure.

## <a name="networking"></a>Rede

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Posso colocar um cluster numa rede virtual existente?

Em clusters 4.x, você pode colocar um cluster em um VNet existente.

Em 3.11 clusters, não é possível implantar um cluster num VNet existente. Pode ligar um cluster Azure Red Hat OpenShift 3.11 a um VNet existente através do espreitamento.

### <a name="is-cross-namespace-networking-supported"></a>A rede de cross-namespace está suportada?

Os administradores de clientes e projetos individuais podem personalizar a rede de espaços de cross-name (incluindo negá-la) numa base por projeto usando `NetworkPolicy` objetos.

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Estou a tentar entrar numa rede virtual numa subscrição diferente, mas não consegui obter um erro do CIDR da VNet.

Na subscrição que tem a rede virtual, certifique-se de registar `Microsoft.ContainerService` o fornecedor com o seguinte comando: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Podemos especificar os intervalos ip para implantação no VNet privado, evitando confrontos com outros VNets corporativos uma vez espreitados?

Em clusters de 4.x, pode especificar os seus próprios intervalos de IP.

Em clusters 3.11, o Azure Red Hat OpenShift suporta o observamento VNet e permite ao cliente fornecer um VNet para par com e um VNet CIDR no qual a rede OpenShift funcionará.

O VNet criado por Azure Red Hat OpenShift será protegido e não aceitará alterações de configuração. O VNet que é espremiado é controlado pelo cliente e reside na sua subscrição.

### <a name="is-the-software-defined-network-module-configurable"></a>O módulo de rede definida por software é configurável?

A Rede Definida de Software é `openshift-ovs-networkpolicy` e não é configurável.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Que equilibrador de carga Azure é usado pela Azure Red Hat OpenShift?  É Standard ou Básico e é configurável?

O Azure Red Hat OpenShift utiliza o Balanceador de Carga Standard Azure, e não é configurável.

## <a name="permissions"></a>Permissões

### <a name="can-an-admin-manage-users-and-quotas"></a>Pode um administrador gerir utilizadores e quotas?

Sim. Um administrador Azure Red Hat OpenShift pode gerir utilizadores e quotas para além de aceder a todos os projetos criados pelo utilizador.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Posso restringir um cluster a apenas certos utilizadores de AD Azure?

Sim. Pode restringir quais os utilizadores AZure AD que podem iniciar súpido num cluster configurando a Aplicação AD Azure. Para mais detalhes, consulte [Como: Restringir a sua aplicação a um conjunto de utilizadores.](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)

### <a name="can-i-restrict-users-from-creating-projects"></a>Posso restringir os utilizadores à criação de projetos?

Sim. Inicie sessão no seu cluster como administrador e execute este comando:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Para obter mais informações, consulte a documentação openshift sobre a desativação do auto-fornecimento para a sua versão de cluster:

- [Desativação do auto-fornecimento em 4,6 clusters](https://docs.openshift.com/container-platform/4.6/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [Desativação do auto-fornecimento em 3,11 agrupamentos](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quais os direitos UNIX (em IaaS) disponíveis para os nós Masters/Infra/App?

Para clusters de 4.x, o acesso ao nó está disponível através da função de administração de cluster. Para mais informações, consulte [a visão geral do RBAC de Kubernetes.](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html)

Para 3.11 agrupamentos, é proibido o acesso ao nó.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Que direitos oCP temos? Administrador de agrupamento? Administrador de projetos?

Para clusters de 4.x, o papel de administrador de cluster está disponível. Para mais informações, consulte [a visão geral do RBAC de Kubernetes.](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html)

Para obter 3.11 clusters, consulte a [visão geral](https://docs.openshift.com/aro/admin_guide/index.html) da administração do cluster para obter mais detalhes.

### <a name="which-identity-providers-are-available"></a>Que fornecedores de identidade estão disponíveis?

Para agrupamentos de 4,x, configura o seu próprio fornecedor de identidade. Para obter mais informações, consulte a documentação do Chapéu Vermelho sobre [a configuração dos fornecedores de identidade.](https://docs.openshift.com/container-platform/4.6/authentication/identity_providers/configuring-ldap-identity-provider.html)

Para 3.11 clusters, você pode usar a integração AD AZure. 

## <a name="storage"></a>Armazenamento

### <a name="is-data-on-my-cluster-encrypted"></a>Os dados do meu cluster estão encriptados?

Por padrão, os dados são encriptados em repouso. A plataforma Azure Storage encripta automaticamente os seus dados antes de os persistir e desencripta os dados antes da recuperação. Para obter mais informações, consulte [a Encriptação do Serviço de Armazenamento Azure para obter dados em repouso](../storage/common/storage-service-encryption.md).

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Os dados são armazenados em etc encriptados no Azure Red Hat OpenShift?

Para os clusters Azure Red Hat OpenShift 4, os dados não são encriptados por padrão, mas tem a opção de ativar a encriptação. Para mais informações, consulte o guia sobre [encriptação etc.](https://docs.openshift.com/container-platform/4.6/security/encrypting-etcd.html)

Para 3.11 clusters, os dados não são encriptados no nível etc. A opção de ligar a encriptação não é atualmente suportada. O OpenShift suporta esta funcionalidade, mas são necessários esforços de engenharia para fazê-lo no roteiro. Os dados são encriptados ao nível do disco. Consulte os [dados de encriptação na camada de datastore](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) para obter mais informações.

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Podemos escolher qualquer solução de armazenamento persistente, como o OCS? 

Para clusters de 4.x, o Disco Azure (Premium_LRS) é configurado como a classe de armazenamento predefinido. Para fornecedores de armazenamento adicionais e para detalhes de configuração (incluindo O Arquivo Azure), consulte a documentação do Chapéu Vermelho sobre [o armazenamento persistente](https://docs.openshift.com/container-platform/4.6/storage/understanding-persistent-storage.html).

Para 3.11 agrupamentos, duas classes de armazenamento são fornecidas por padrão: uma para disco Azure (Premium_LRS) e outra para Ficheiro Azure.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>A ARO armazena algum dado de cliente fora da região do cluster?

N.º Todos os dados criados num cluster ARO são mantidos dentro da região do cluster.
