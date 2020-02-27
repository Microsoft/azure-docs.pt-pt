---
title: Perguntas frequentes para O Chapéu Vermelho Azure OpenShift
description: Aqui estão as respostas a perguntas comuns sobre o Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619482"
---
# <a name="azure-red-hat-openshift-faq"></a>FaQ openshift do chapéu vermelho azure

Este artigo aborda frequentemente perguntas (PERGUNTAS) sobre o Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Que regiões de Azure são apoiadas?

Consulte [os recursos suportados](supported-resources.md#azure-regions) para uma lista de regiões globais onde o Azure Red Hat OpenShift é apoiado.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Posso implantar um cluster numa rede virtual existente?

Não. Mas pode ligar um cluster Azure Red Hat OpenShift a um VNET existente através de um peering. Consulte [a rede virtual de um cluster a uma rede virtual existente](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) para obter detalhes.

## <a name="what-cluster-operations-are-available"></a>Que operações de cluster estão disponíveis?

Só se pode escalar para cima ou para baixo o número de nós de computação. Não são permitidas outras modificações ao recurso `Microsoft.ContainerService/openShiftManagedClusters` após a criação. O número máximo de nós de cálculo é limitado a 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Que tamanhos de máquina virtual posso usar?

Consulte o tamanho da [máquina virtual Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) para obter uma lista de tamanhos de máquinas virtuais que pode usar com um cluster OpenShift de chapéu vermelho azure.

## <a name="is-data-on-my-cluster-encrypted"></a>Os dados do meu cluster estão encriptados?

Por padrão, há encriptação em repouso. A plataforma de armazenamento Azure encripta automaticamente os seus dados antes de persistir e desencripta os dados antes da recuperação. Consulte a Encriptação do Serviço de [Armazenamento Azure para obter dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) para obter detalhes.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Posso usar a Prometheus/Grafana para monitorizar as minhas aplicações?

Sim, pode implantar a Prometheus no seu espaço de nome e monitorizar aplicações no seu espaço de nome.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Posso usar a Prometheus/Grafana para monitorizar métricas relacionadas com a saúde e capacidade do cluster?

Não, não no momento atual.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>O registo do Docker está disponível externamente para que eu possa usar ferramentas como o Jenkins?

O registo do Docker está disponível a partir de `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` No entanto, não é fornecida uma forte garantia de durabilidade de armazenamento. Também pode utilizar o Registo de [Contentores Azure](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>A rede de espaço com nomes cruzados é suportada?

Os administradores de projetos individuais e clientes podem personalizar a rede de espaço cross-name (incluindo negá-lo) numa base de projeto utilizando objetos `NetworkPolicy`.

## <a name="can-an-admin-manage-users-and-quotas"></a>Um administrador pode gerir utilizadores e quotas?

Sim. Um administrador Azure Red Hat OpenShift pode gerir utilizadores e quotas, além de aceder a todos os projetos criados pelo utilizador.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Posso restringir um cluster a apenas certos utilizadores de Anúncios Azure?

Sim. Pode restringir quais os utilizadores de Anúncios Azure que podem iniciar sessão num cluster configurando a Aplicação AD Azure. Para mais detalhes, consulte [Como: Restringir a sua app a um conjunto de utilizadores](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Posso restringir os utilizadores à criação de projetos?

Sim. Inicie sessão no seu cluster como administrador OpenShift da Red Hat Azure e execute este comando:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Para obter mais informações, consulte a documentação OpenShift sobre a desativação [do auto-provisionamento](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Um cluster pode ter nós de computação em várias regiões de Azure?

Não. Todos os nós de um aglomerado OpenShift do Chapéu Vermelho Azure devem ter origem na mesma região de Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Os nós mestres e de infraestruturas são abstraídos como estão com o Serviço Azure Kubernetes (AKS)?

Não. Todos os recursos, incluindo o mestre do cluster, são executados na subscrição do seu cliente. Este tipo de recursos são colocados num grupo de recursos apenas para leitura.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>O Open Service Broker para o Azure (OSBA) é suportado?

Sim. Pode utilizar o OSBA com o Azure Red Hat OpenShift. Consulte o Open Service Broker para obter mais informações sobre o [Azure.](https://github.com/Azure/open-service-broker-azure#openshift-project-template)

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Estou a tentar entrar numa rede virtual numa subscrição diferente, mas a ter `Failed to get vnet CIDR` erro.

Na subscrição que tem a rede virtual, certifique-se de registar `Microsoft.ContainerService` fornecedor com `az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>O que é o processo de manutenção do Azure Red Hat OpenShift (ARO) ?

Existem três tipos de manutenção para ARO: upgrades, cópia de segurança e restauro de dados de etcd, e manutenção iniciada pelo fornecedor de nuvem.

+ As atualizações incluem atualizações de software e CVEs. A reparação do CVE ocorre no arranque, executando `yum update` e prevê uma mitigação imediata.  Paralelamente, será criada uma nova construção de imagem para futuras criações de cluster.

+ A cópia de segurança e gestão de dados de etcd é um processo automatizado que pode exigir tempo de inatividade de cluster dependendo da ação. Se a base de dados etcd estiver a ser restaurada a partir de uma cópia de segurança, haverá tempo de inatividade. Recuamos etc e retemos as últimas 6 horas de reforços.

+ A manutenção iniciada pelo fornecedor de nuvem inclui falhas de rede, armazenamento e regionais. A manutenção depende do fornecedor de nuvem e baseia-se em atualizações fornecidas pelo fornecedor.

## <a name="what-is-the-general-upgrade-process"></a>Qual é o processo de atualização geral?

Executar uma atualização deve ser um processo seguro para ser executado e não deve perturbar os serviços de cluster. O SRE pode desencadear o processo de atualização quando novas versões estiverem disponíveis ou os CVEs estiverem pendentes.

As atualizações disponíveis são testadas num ambiente de estágio e depois aplicadas a clusters de produção. Quando aplicado, um novo nó é adicionado temporariamente e os nós são atualizados de forma rotativa para que as cápsulas mantenham as contagens de réplica. Seguir as melhores práticas ajuda a garantir o mínimo de tempo de inatividade.

Dependendo da gravidade da atualização ou atualização pendente, o processo poderá diferir na medida em que as atualizações podem ser aplicadas rapidamente para mitigar a exposição do serviço a um CVE. Uma nova imagem será construída assíncronamente, testada e lançada como uma atualização de cluster. Para além disso, não há diferença entre a emergência e a manutenção planeada. A manutenção planeada não está pré-programada com o cliente.

As notificações podem ser enviadas via ICM e e-mail se for necessária comunicação ao cliente.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>E as janelas de manutenção planeadas?

Não distinguimos entre os dois tipos de manutenção. As nossas equipas estão disponíveis 24 horas por dia, 7/365 e não utilizam janelas de manutenção programadas tradicionais "fora de horas".

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Como será atualizado o sistema operativo e o software OpenShift?

O sistema operativo anfitrião e o software OpenShift são atualizados através do nosso processo geral de atualização e construção de imagens.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Qual é o processo para reiniciar o nó atualizado?

Isto deve ser tratado como parte de uma atualização.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Os dados são armazenados em etcd encriptados na ARO?

Não é encriptado no nível de etc. A opção de ligá-lo não é suportada atualmente. A OpenShift suporta esta funcionalidade, mas são necessários esforços de engenharia para chegar ao mapa. Os dados estão encriptados ao nível do disco. Consulte [a Encriptação de Dados na Camada](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) de Datastore para obter mais informações.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Os registos de VMsubjacentes podem ser transmitidos para um sistema de análise de registo de clientes?

Syslog, docker logs, diário e dmesg são manuseados pelo serviço gerido e não estão expostos aos clientes.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Como pode um cliente ter acesso a métricas como CPU/memória ao nível do nó para tomar medidas à escala, problemas de depuração, etc. Não consigo correr `kubectl top` num aglomerado de ARO.

Os clientes podem aceder às métricas CPU/Memória ao nível do nó utilizando o comando `oc adm top nodes` ou `kubectl top nodes` com a função de clusterde clientes-administração.  Os clientes também podem aceder às métricas CPU/Memória de `pods` com o comando `oc adm top pods` ou `kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Qual é a configuração do programador de cápsulas padrão para ARO?

A ARO usa o programador padrão que envia no OpenShift. Existem alguns mecanismos adicionais que não são suportados na ARO. Consulte a [documentação do programador predefinido](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) e a [documentação do programador principal](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) para mais detalhes.

O agendamento avançado/personalizado não é suportado. Consulte a [documentação](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) de Agendamento para mais detalhes.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Se aumentarmos a implantação, como é que os domínios de falha do Azure mapeiam a colocação do pod para garantir que todas as cápsulas para um serviço não são eliminadas por uma falha num único domínio de falha?

Existem por padrão cinco domínios de falha ao utilizar conjuntos de escala de máquina virtual em Azure. Cada instância de máquina virtual num conjunto de escala será colocada num destes domínios de avaria. Isto garante que as aplicações implantadas nos nós de computação num cluster serão colocadas em domínios de falha separados.

Consulte a [Escolha do número certo de domínios de falha para](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) a escala de máquina virtual para mais detalhes.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Há alguma maneira de gerir a colocação da cápsula?

Os clientes têm a capacidade de obter nódosos e ver rótulos como o cliente-administrador.  Isto fornecerá uma maneira de direcionar qualquer VM no conjunto de escala.

Deve ser utilizado um cuidado quando se utilizam etiquetas específicas:

- O nome de anfitrião não deve ser utilizado. O nome de anfitrião é rodado frequentemente com atualizações e atualizações e é garantido que muda.

- Se o cliente tiver um pedido de etiquetas específicas ou uma estratégia de implementação, isso poderia ser realizado, mas exigiria esforços de engenharia e não é apoiado hoje.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Qual é o número máximo de cápsulas num aglomerado ARO?  Qual é o número máximo de cápsulas por nó em ARO?

 O Azure Red Hat OpenShift 3.11 tem um limite de 50 cápsulas por nó, com aro com um limite de nó [de 20 computas,](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)de modo que reduz o número máximo de cápsulas suportadas num cluster ARO a 50*20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Podemos especificar intervalos ip para implantação no VNET privado, evitando confrontos com outros VNETs corporativos uma vez espreitados?

O Azure Red Hat OpenShift suporta o peering vNET e permite ao cliente fornecer um VNET com o qual o VNET CIDR irá operar.

O VNET criado pela ARO será protegido e não aceitará alterações de configuração. O VNET que é espreitado é controlado pelo cliente e reside na sua subscrição.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>O cluster reside numa subscrição de cliente? 

A Aplicação Gerida Azure vive num Grupo de Recursos bloqueados com a subscrição do cliente. O cliente pode ver objetos nesse RG, mas não modificar.

## <a name="is-the-sdn-module-configurable"></a>O módulo SDN é configurável?

O SDN é uma política de rede openshift-ovs e não é configurável.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quais os direitos UNIX (em IaaS) disponíveis para os nós de Masters/Infra/App?

Não aplicável a esta oferta. O acesso ao nó é proibido.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Que direitos da OCP temos? Administrador de cluster? Administrador do projeto?

Para mais detalhes, consulte a visão geral da [administração](https://docs.openshift.com/aro/admin_guide/index.html)do cluster Azure Red Hat OpenShift .

## <a name="which-kind-of-federation-with-ldap"></a>Que tipo de federação com LDAP?

Isto seria conseguido através da integração da AD Azure. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Existe algum elemento na ARO partilhado com outros clientes? Ou tudo é independente?

Cada cluster Azure Red Hat OpenShift é dedicado a um determinado cliente e vive dentro da subscrição do cliente. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Podemos escolher alguma solução de armazenamento persistente, como o OCS? 

Estão disponíveis duas aulas de armazenamento para selecionar entre: Azure Disk e Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Como é que um cluster é atualizado (incluindo majores e menores devido a vulnerabilidades)?

Ver Qual é o processo de [atualização geral?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Que balancedor de carga Azure é usado pela ARO?  É Standard ou Básico e é configurável?

Aro usa O Equilíbrio de Carga Standard Azure, e não é configurável.

## <a name="can-aro-use-netapp-based-storage"></a>A ARO pode usar o armazenamento baseado em NetApp?

Neste momento, as únicas opções de armazenamento suportadas são as classes de armazenamento Azure Disk e Azure File. 


