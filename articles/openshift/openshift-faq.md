---
title: Perguntas frequentes sobre o Azure Red Hat OpenShift | Microsoft Docs
description: Aqui estão as respostas para perguntas comuns sobre Microsoft Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d8707e2edccf144cbe58a530bcfe2c176e656915
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582409"
---
# <a name="azure-red-hat-openshift-faq"></a>Perguntas frequentes sobre o Azure Red Hat OpenShift

Este artigo aborda perguntas frequentes (FAQs) sobre Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Quais regiões do Azure têm suporte?

Consulte [recursos com suporte](supported-resources.md#azure-regions) para obter uma lista de regiões globais em que o Azure Red Hat OpenShift tem suporte.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Posso implantar um cluster em uma rede virtual existente?

Não. Mas você pode conectar um cluster do Azure Red Hat OpenShift a uma VNET existente por meio de emparelhamento. Consulte [conectar a rede virtual de um cluster a uma rede virtual existente](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) para obter detalhes.

## <a name="what-cluster-operations-are-available"></a>Quais operações de cluster estão disponíveis?

Você só pode escalar ou reduzir verticalmente o número de nós de computação. Nenhuma outra modificação é permitida ao recurso de `Microsoft.ContainerService/openShiftManagedClusters` após a criação. O número máximo de nós de computação é limitado a 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Quais tamanhos de máquina virtual posso usar?

Consulte [tamanhos de máquina virtual do Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) para obter uma lista de tamanhos de máquina virtual que você pode usar com um cluster do Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Os dados estão criptografados no meu cluster?

Por padrão, há criptografia em repouso. A plataforma de armazenamento do Azure criptografa automaticamente os dados antes de mantê-los e descriptografa os dados antes da recuperação. Consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) para obter detalhes.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Posso usar Prometheus/Grafana para monitorar meus aplicativos?

Sim, você pode implantar Prometheus em seu namespace e monitorar aplicativos em seu namespace.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Posso usar Prometheus/Grafana para monitorar as métricas relacionadas à integridade e à capacidade do cluster?

Não, não na hora atual.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>O registro do Docker está disponível externamente para que eu possa usar ferramentas como Jenkins?

O registro do Docker está disponível em `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` no entanto, uma garantia de durabilidade de armazenamento forte não é fornecida. Você também pode usar o [registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Há suporte para a rede de namespace cruzado?

Administradores de projeto individual e cliente podem personalizar a rede de namespace cruzado (incluindo sua negação) por projeto usando objetos `NetworkPolicy`.

## <a name="can-an-admin-manage-users-and-quotas"></a>Um administrador pode gerenciar usuários e cotas?

Sim. Um administrador do Azure Red Hat OpenShift pode gerenciar usuários e cotas, além de acessar todos os projetos criados pelo usuário.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Posso restringir um cluster a apenas determinados usuários do AD do Azure?

Sim. Você pode restringir quais usuários do Azure AD podem entrar em um cluster Configurando o aplicativo do Azure AD. Para obter detalhes, consulte [como: restringir seu aplicativo a um conjunto de usuários](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Um cluster pode ter nós de computação em várias regiões do Azure?

Não. Todos os nós em um cluster do Azure Red Hat OpenShift devem se originar da mesma região do Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Os nós mestre e de infraestrutura são abstratos como estão com o AKS (serviço kubernetes do Azure)?

Não. Todos os recursos, incluindo o mestre de cluster, são executados na sua assinatura de cliente. Esses tipos de recursos são colocados em um grupo de recursos somente leitura.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Há suporte para o Service Broker aberto para Azure (OSBA)?

Sim. Você pode usar o OSBA com o Azure Red Hat OpenShift. Consulte [abrir Service Broker para o Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) para obter mais informações.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Estou tentando emparelhar uma rede virtual em uma assinatura diferente, mas obter `Failed to get vnet CIDR` erro.

Na assinatura que tem a rede virtual, certifique-se de registrar o provedor de `Microsoft.ContainerService` com `az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>O que é o processo de manutenção da toa (Red Hat OpenShift) do Azure?

Há três tipos de manutenção para toa: atualizações, backup e restauração de dados do etcd e manutenção iniciada pelo provedor de nuvem.

+ Atualizações incluem atualizações de software e CVEs. A correção de CVE ocorre na inicialização executando `yum update` e fornece a mitigação imediata.  Em paralelo, uma nova compilação de imagem será criada para futuras criações de cluster.

+ O backup e o gerenciamento de dados do etcd são um processo automatizado que pode exigir tempo de inatividade do cluster dependendo da ação. Se o banco de dados etcd estiver sendo restaurado de um backup, haverá um tempo de inatividade. Fazemos o backup do etcd por hora e reguardamos as últimas 6 horas de backups.

+ A manutenção iniciada pelo provedor de nuvem inclui interrupções de rede, armazenamento e regionais. A manutenção depende do provedor de nuvem e depende das atualizações fornecidas pelo provedor.

## <a name="what-is-the-general-upgrade-process"></a>O que é o processo de atualização geral?

A execução de uma atualização deve ser um processo seguro para ser executado e não deve interromper os serviços de cluster. O SRE pode disparar o processo de atualização quando novas versões estão disponíveis ou CVEs estão pendentes.

As atualizações disponíveis são testadas em um ambiente de estágio e, em seguida, aplicadas a clusters de produção. Quando aplicado, um novo nó é temporariamente adicionado e os nós são atualizados de maneira rotativa para que o pods Mantenha as contagens de réplica. As práticas recomendadas a seguir ajudam a garantir um mínimo de sem tempo de inatividade.

Dependendo da severidade da atualização ou atualização pendente, o processo pode ser diferente, pois as atualizações podem ser aplicadas rapidamente para atenuar a exposição do serviço a um CVE. Uma nova imagem será criada de forma assíncrona, testada e distribuída como uma atualização de cluster. Além disso, não há nenhuma diferença entre emergência e manutenção planejada. A manutenção planejada não é preprogramada com o cliente.

As notificações podem ser enviadas por meio de ICM e email se a comunicação com o cliente for necessária.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>E quanto à emergência versus as janelas de manutenção planejadas?

Não fazemos distinção entre os dois tipos de manutenção. Nossas equipes estão disponíveis 24/7/365 e não usam janelas de manutenção tradicionais programadas "fora de horas".

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Como o sistema operacional host e o software OpenShift serão atualizados?

O sistema operacional do host e o software OpenShift são atualizados por meio de nosso processo geral de atualização e criação de imagem.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Qual é o processo para reinicializar o nó atualizado?

Isso deve ser tratado como parte de uma atualização.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Os dados são armazenados no etcd criptografados na toa?

Ele não é criptografado no nível de etcd. No momento, não há suporte para a opção de ativá-lo. O OpenShift dá suporte a esse recurso, mas os esforços de engenharia são necessários para fazê-lo no mapa de estrada. Os dados são criptografados no nível do disco. Para obter mais informações [, consulte Criptografando dados na camada de armazenamento de datastore](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Os logs de VMs subjacentes podem ser transmitidos para um sistema de análise de log do cliente?

Syslog, logs do Docker, diário e dmesg são tratados pelo serviço gerenciado e não são expostos aos clientes.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Como um cliente pode obter acesso a métricas como CPU/memória no nível do nó para tomar medidas para dimensionar, depurar problemas, etc. Não consigo executar `kubectl top` em um cluster toa.

`kubectl top` não está disponível no Red Hat OpenShift. Ele requer uma fonte de métricas de backup, Heapster (preterido) ou Metrics-Server (incubating ou Alpha), nenhum dos quais estão incluídos na pilha de monitoramento OpenShift.

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Qual é a configuração do Agendador de Pod padrão para toa?

A toa usa o agendador padrão fornecido no OpenShift. Há alguns mecanismos adicionais que não têm suporte na toa. Consulte a documentação do [Agendador padrão](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) e a [documentação do Agendador mestre](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) para obter mais detalhes.

No momento, não há suporte para o agendamento avançado/personalizado. Consulte a [documentação de agendamento](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) para obter mais detalhes.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Se expandirmos a implantação, como os domínios de falha do Azure serão mapeados no posicionamento pod para garantir que todos os pods de um serviço não sejam vazados por uma falha em um único domínio de falha?

Há, por padrão, cinco domínios de falha ao usar conjuntos de dimensionamento de máquinas virtuais no Azure. Cada instância de máquina virtual em um conjunto de dimensionamento será colocada em um desses domínios de falha. Isso garante que os aplicativos implantados nos nós de computação em um cluster sejam colocados em domínios de falha separados.

Consulte [escolher o número correto de domínios de falha para o conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) para obter mais detalhes.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Há uma maneira de gerenciar o posicionamento do pod?

Com a atualização iminente do administrador do cliente, os clientes terão a capacidade de obter nós e exibir rótulos.  Isso fornecerá uma maneira de direcionar qualquer VM no conjunto de dimensionamento.

Cuidado deve ser usado ao usar rótulos específicos:

- O nome do host não deve ser usado. O nome do host é girado frequentemente com atualizações e atualizações e tem a garantia de alteração.

- Se o cliente tiver uma solicitação de rótulos específicos ou uma estratégia de implantação, isso poderá ser realizado, mas exigiria esforços de engenharia e não terá suporte atualmente.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Qual é o número máximo de pods em um cluster de toa?  Qual é o número máximo de pods por nó na toa?

Consulte [docs OpenShift upstream](https://docs.openshift.com/container-platform/3.11/scaling_performance/cluster_limits.html#scaling-performance-current-cluster-limits) para obter mais detalhes. O Red Hat OpenShift 3,11 tem um limite de 250-Pod/nó, enquanto [a toa tem um limite de nó de 20 computadores](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), de modo que ele limita o número máximo de pods com suporte em um cluster de toa para 250 * 20 = 5000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Podemos especificar intervalos de IP para implantação na VNET privada, evitando conflitos com outros VNETs corporativos uma vez emparelhados?

O Azure Red Hat OpenShift dá suporte ao emparelhamento de VNET e permite que o cliente forneça uma VNET para emparelhar com e um CIDR de VNET no qual a rede OpenShift funcionará.

A VNET criada pela toa será protegida e não aceitará alterações de configuração. A VNET emparelhada é controlada pelo cliente e reside em sua assinatura.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>O cluster reside em uma assinatura de cliente? 

O aplicativo gerenciado do Azure reside em um grupo de recursos bloqueado com a assinatura do cliente. O cliente pode exibir objetos nesse RG, mas não modificar.

## <a name="is-the-sdn-module-configurable"></a>O módulo SDN é configurável?

SDN é openshift-OVS-NetworkPolicy e não é configurável.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quais direitos do UNIX (no IaaS) estão disponíveis para nós mestres/de infraestrutura/aplicativo?

Não aplicável a esta oferta. O acesso ao nó é proibido.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Quais direitos de OCP temos? Cluster-administrador? Projeto-administrador?

Para obter detalhes, consulte a [visão geral da administração do cluster](https://docs.openshift.com/aro/admin_guide/index.html)do Azure Red Hat OpenShift.

## <a name="which-kind-of-federation-with-ldap"></a>Qual tipo de Federação com LDAP?

Isso seria obtido por meio da integração do Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Há algum elemento na toa compartilhado com outros clientes? Ou é independente de tudo?

Cada cluster do Azure Red Hat OpenShift é dedicado a um determinado cliente e reside na assinatura do cliente. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Podemos escolher qualquer solução de armazenamento persistente, como o OCS? 

Duas classes de armazenamento estão disponíveis para seleção: disco do Azure e arquivo do Azure.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Como um cluster é atualizado (incluindo grandes e menores devido a vulnerabilidades)?

Veja o [que é o processo de atualização geral?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Qual balanceador de carga do Azure é usado por toa?  É padrão ou básico e é configurável?

A toa usa Azure Load Balancer padrão e não é configurável.

## <a name="can-aro-use-netapp-based-storage"></a>A toa pode usar o armazenamento baseado em NetApp?

No momento, as únicas opções de armazenamento com suporte são o disco do Azure e as classes de armazenamento de arquivos do Azure. 


