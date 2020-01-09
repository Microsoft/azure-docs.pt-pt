---
title: Monitoramento de integridade no Service Fabric
description: Uma introdução ao modelo de monitoramento de integridade do Azure Service Fabric, que fornece monitoramento do cluster e seus aplicativos e serviços.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 473aa2b9a74193a857390cd3e29b2b559b6084d3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433888"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introdução à monitorização do estado de funcionamento do Service Fabric
O Azure Service Fabric introduz um modelo de integridade que fornece avaliação de integridade avançada, flexível e extensível e relatórios. O modelo permite o monitoramento quase em tempo real do estado do cluster e dos serviços em execução nele. Você pode facilmente obter informações de integridade e corrigir possíveis problemas antes que eles sejam colocados em cascata e causem interrupções maciças. No modelo típico, os serviços enviam relatórios com base em suas exibições locais e essas informações são agregadas para fornecer uma exibição geral no nível do cluster.

Service Fabric componentes usam esse modelo de integridade avançado para relatar seu estado atual. Você pode usar o mesmo mecanismo para relatar a integridade de seus aplicativos. Se você investir em relatórios de integridade de alta qualidade que capturam suas condições personalizadas, poderá detectar e corrigir problemas para o aplicativo em execução com muito mais facilidade.

> [!NOTE]
> Começamos o subsistema de integridade para atender a uma necessidade de atualizações monitoradas. Service Fabric fornece atualizações monitoradas de aplicativo e cluster que garantem disponibilidade total, sem tempo de inatividade e pouca intervenção do usuário. Para atingir essas metas, a atualização verifica a integridade com base nas políticas de atualização configuradas. Uma atualização pode continuar somente quando a integridade respeita os limites desejados. Caso contrário, a atualização será revertida automaticamente ou pausada para dar aos administradores a oportunidade de corrigir os problemas. Para saber mais sobre atualizações de aplicativos, consulte [Este artigo](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Repositório de integridade
O repositório de integridade mantém informações relacionadas à integridade sobre entidades no cluster para fácil recuperação e avaliação. Ele é implementado como um Service Fabric serviço de estado persistente para garantir alta disponibilidade e escalabilidade. O repositório de integridade faz parte do aplicativo **Fabric:/System** e está disponível quando o cluster está em execução.

## <a name="health-entities-and-hierarchy"></a>Entidades de integridade e hierarquia
As entidades de integridade são organizadas em uma hierarquia lógica que captura interações e dependências entre diferentes entidades. O repositório de integridade cria automaticamente entidades de integridade e hierarquia com base em relatórios recebidos de componentes de Service Fabric.

As entidades de integridade espelham as entidades de Service Fabric. (Por exemplo, a **entidade de aplicativo de integridade** corresponde a uma instância de aplicativo implantada no cluster, enquanto a entidade do nó de **integridade** corresponde a um nó de Cluster Service Fabric.) A hierarquia de integridade captura as interações das entidades do sistema e é a base para a avaliação de integridade avançada. Você pode aprender sobre os principais conceitos de Service Fabric em [Service Fabric visão geral técnica](service-fabric-technical-overview.md). Para obter mais informações sobre o aplicativo, consulte [modelo de aplicativo Service Fabric](service-fabric-application-model.md).

As entidades de integridade e a hierarquia permitem que o cluster e os aplicativos sejam efetivamente relatados, depurados e monitorados. O modelo de integridade fornece uma representação precisa e *granular* da integridade das muitas partes móveis no cluster.

![entidades de integridade.][1]
As entidades de integridade, organizadas em uma hierarquia com base em relações pai-filho.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de integridade são:

* **Cluster**. Representa a integridade de um Cluster Service Fabric. Os relatórios de integridade do cluster descrevem as condições que afetam o cluster inteiro. Essas condições afetam várias entidades no cluster ou no próprio cluster. Com base na condição, o reporter não pode restringir o problema a um ou mais filhos não íntegros. Os exemplos incluem o cérebro da divisão de cluster devido ao particionamento de rede ou problemas de comunicação.
* **Nó**. Representa a integridade de um nó de Service Fabric. Os relatórios de integridade do nó descrevem as condições que afetam a funcionalidade do nó. Normalmente, eles afetam todas as entidades implantadas em execução. Os exemplos incluem um nó sem espaço em disco (ou outras propriedades em todo o computador, como memória, conexões) e quando um nó está inoperante. A entidade de nó é identificada pelo nome do nó (cadeia de caracteres).
* **Aplicativo**. Representa a integridade de uma instância do aplicativo em execução no cluster. Os relatórios de integridade do aplicativo descrevem as condições que afetam a integridade geral do aplicativo. Eles não podem ser restringidos a filhos individuais (serviços ou aplicativos implantados). Os exemplos incluem a interação de ponta a ponta entre diferentes serviços no aplicativo. A entidade de aplicativo é identificada pelo nome do aplicativo (URI).
* **Serviço**. Representa a integridade de um serviço em execução no cluster. Os relatórios de integridade do serviço descrevem as condições que afetam a integridade geral do serviço. O reporter não pode restringir o problema a uma partição ou réplica não íntegra. Os exemplos incluem uma configuração de serviço (como porta ou compartilhamento de arquivo externo) que está causando problemas para todas as partições. A entidade de serviço é identificada pelo nome do serviço (URI).
* **Partição**. Representa a integridade de uma partição de serviço. Os relatórios de integridade de partição descrevem as condições que afetam o conjunto de réplicas inteiro. Os exemplos incluem quando o número de réplicas está abaixo da contagem de destino e quando uma partição está em perda de quorum. A entidade de partição é identificada pela ID de partição (GUID).
* **Réplica**. Representa a integridade de uma réplica de serviço com estado ou de uma instância de serviço sem estado. A réplica é a menor unidade que os Watchdogs e os componentes do sistema podem relatar para um aplicativo. Para serviços com estado, os exemplos incluem uma réplica primária que não pode replicar operações para secundários e replicação lenta. Além disso, uma instância sem estado pode relatar quando está ficando sem recursos ou tem problemas de conectividade. A entidade de réplica é identificada pela ID de partição (GUID) e pela ID de réplica ou instância (longa).
* **DeployedApplication**. Representa a integridade de um *aplicativo em execução em um nó*. Os relatórios de integridade do aplicativo implantados descrevem condições específicas ao aplicativo no nó que não podem ser reduzidas para pacotes de serviço implantados no mesmo nó. Os exemplos incluem erros quando o pacote de aplicativos não pode ser baixado nesse nó e emite problemas de configuração de entidades de segurança de aplicativo no nó. O aplicativo implantado é identificado pelo nome do aplicativo (URI) e pelo nome do nó (cadeia de caracteres).
* **DeployedServicePackage**. Representa a integridade de um pacote de serviço em execução em um nó no cluster. Ele descreve as condições específicas de um pacote de serviço que não afetam os outros pacotes de serviço no mesmo nó para o mesmo aplicativo. Os exemplos incluem um pacote de códigos no pacote de serviço que não pode ser iniciado e um pacote de configuração que não pode ser lido. O pacote de serviço implantado é identificado pelo nome do aplicativo (URI), nome do nó (cadeia de caracteres), nome do manifesto do serviço (cadeia de caracteres) e ID de ativação do pacote de serviço (cadeia de caracteres).

A granularidade do modelo de integridade torna mais fácil detectar e corrigir problemas. Por exemplo, se um serviço não estiver respondendo, é possível relatar que a instância do aplicativo não está íntegra. No entanto, o relatório nesse nível não é o ideal, pois o problema pode não estar afetando todos os serviços dentro desse aplicativo. O relatório deve ser aplicado ao serviço não íntegro ou a uma partição filho específica, se mais informações apontarem para essa partição. Os dados são automaticamente percorrendo pela hierarquia e uma partição não íntegra torna-se visível nos níveis de serviço e aplicativo. Essa agregação ajuda a identificar e resolver a causa raiz do problema mais rapidamente.

A hierarquia de integridade é composta por relações pai-filho. Um cluster é composto por nós e aplicativos. Os aplicativos têm serviços e aplicativos implantados. Os aplicativos implantados implantaram pacotes de serviço. Os serviços têm partições e cada partição tem uma ou mais réplicas. Há uma relação especial entre nós e entidades implantadas. Um nó não íntegro como relatado por seu componente de sistema de autoridade, o serviço de Gerenciador de Failover, afeta os aplicativos implantados, pacotes de serviço e réplicas implantados nele.

A hierarquia de integridade representa o estado mais recente do sistema com base nos relatórios de integridade mais recentes, que são quase informações em tempo real.
Os Watchdogs internos e externos podem relatar as mesmas entidades com base em lógica específica do aplicativo ou em condições monitoradas personalizadas. Os relatórios do usuário coexistem com os relatórios do sistema.

Planeje investir em como relatar e responder à integridade durante o design de um serviço de nuvem grande. Esse investimento antecipado torna o serviço mais fácil de depurar, monitorar e operar.

## <a name="health-states"></a>Estados de integridade
Service Fabric usa três Estados de integridade para descrever se uma entidade está íntegra ou não: OK, aviso e erro. Qualquer relatório enviado para o repositório de integridade deve especificar um desses Estados. O resultado da avaliação de integridade é um desses Estados.

Os [Estados de integridade](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) possíveis são:

* **OK**. A entidade está íntegra. Não há problemas conhecidos relatados sobre ele ou seus filhos (quando aplicável).
* **Aviso**. A entidade tem alguns problemas, mas ainda pode funcionar corretamente. Por exemplo, há atrasos, mas eles ainda não causam problemas funcionais. Em alguns casos, a condição de aviso pode ser corrigida sem intervenção externa. Nesses casos, os relatórios de integridade aumentam a conscientização e fornecem visibilidade do que está acontecendo. Em outros casos, a condição de aviso pode degradar um problema grave sem intervenção do usuário.
* **Erro**. A entidade não está íntegra. A ação deve ser tomada para corrigir o estado da entidade, pois ela não funciona corretamente.
* **Desconhecido**. A entidade não existe no repositório de integridade. Esse resultado pode ser obtido das consultas distribuídas que mesclam os resultados de vários componentes. Por exemplo, a consulta obter lista de nós vai para **failovermanager**, **Clustermanager**e **healthmanager**; a consulta obter lista de aplicativos vai para **Clustermanager** e **healthmanager**. Essas consultas mesclam os resultados de vários componentes do sistema. Se outro componente do sistema retornar uma entidade que não está presente no Health Store, o resultado mesclado terá um estado de integridade desconhecido. Uma entidade não está no repositório porque os relatórios de integridade ainda não foram processados ou a entidade foi limpa após a exclusão.

## <a name="health-policies"></a>Diretivas de integridade
O repositório de integridade aplica as políticas de integridade para determinar se uma entidade está íntegra com base em seus relatórios e seus filhos.

> [!NOTE]
> As políticas de integridade podem ser especificadas no manifesto do cluster (para avaliação de integridade do cluster e do nó) ou no manifesto do aplicativo (para avaliação do aplicativo e qualquer um de seus filhos). As solicitações de avaliação de integridade também podem passar por políticas de avaliação de integridade personalizadas, que são usadas somente para essa avaliação.
> 
> 

Por padrão, Service Fabric aplica regras estritas (tudo deve estar íntegro) para a relação hierárquica pai-filho. Se mesmo um dos filhos tiver um evento não íntegro, o pai será considerado não íntegro.

### <a name="cluster-health-policy"></a>Política de integridade do cluster
A [política de integridade do cluster](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) é usada para avaliar o estado de integridade do cluster e os Estados de integridade do nó. A política pode ser definida no manifesto do cluster. Se não estiver presente, a política padrão (zero falhas toleradas) será usada.
A política de integridade do cluster contém:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se os relatórios de integridade de aviso devem ser tratados como erros durante a avaliação de integridade. Predefinição: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Especifica a porcentagem máxima tolerada de aplicativos que podem não estar íntegros antes que o cluster seja considerado com erro.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Especifica o percentual máximo tolerado de nós que podem não estar íntegros antes que o cluster seja considerado com erro. Em clusters grandes, alguns nós estão sempre inativos ou fora para reparos, portanto, esse percentual deve ser configurado para tolerar isso.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). O mapa da política de integridade do tipo de aplicativo pode ser usado durante a avaliação de integridade do cluster para descrever tipos de aplicativos especiais. Por padrão, todos os aplicativos são colocados em um pool e avaliados com MaxPercentUnhealthyApplications. Se alguns tipos de aplicativos devem ser tratados de forma diferente, eles podem ser retirados do pool global. Em vez disso, eles são avaliados em relação aos percentuais associados ao nome do tipo de aplicativo no mapa. Por exemplo, em um cluster, há milhares de aplicativos de tipos diferentes e algumas instâncias de aplicativo de controle de um tipo de aplicativo especial. Os aplicativos de controle nunca devem estar em erro. Você pode especificar MaxPercentUnhealthyApplications globais para 20% para tolerar algumas falhas, mas para o tipo de aplicativo "ControlApplicationType", defina MaxPercentUnhealthyApplications como 0. Dessa forma, se alguns dos muitos aplicativos não estiverem íntegros, mas abaixo do percentual não íntegro global, o cluster será avaliado como aviso. Um estado de integridade de aviso não afeta a atualização do cluster ou outro monitoramento disparado pelo estado de integridade do erro. Mas mesmo um aplicativo de controle em erro tornaria o cluster não íntegro, que dispara a reversão ou pausa a atualização do cluster, dependendo da configuração da atualização.
  Para os tipos de aplicativo definidos no mapa, todas as instâncias de aplicativo são retiradas do pool global de aplicativos. Eles são avaliados com base no número total de aplicativos do tipo de aplicativo, usando o MaxPercentUnhealthyApplications específico do mapa. Todos os demais aplicativos permanecem no pool global e são avaliados com MaxPercentUnhealthyApplications.

O exemplo a seguir é um trecho de um manifesto de cluster. Para definir entradas no mapa de tipo de aplicativo, Prefixe o nome do parâmetro com "ApplicationTypeMaxPercentUnhealthyApplications-", seguido pelo nome do tipo de aplicativo.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Política de integridade do aplicativo
A [política de integridade do aplicativo](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) descreve como a avaliação de eventos e a agregação de Estados filho é feita para aplicativos e seus filhos. Ele pode ser definido no manifesto do aplicativo, **ApplicationManifest. xml**, no pacote de aplicativos. Se nenhuma política for especificada, o Service Fabric assumirá que a entidade não está íntegra se tiver um relatório de integridade ou um filho no estado de integridade de aviso ou erro.
As políticas configuráveis são:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se os relatórios de integridade de aviso devem ser tratados como erros durante a avaliação de integridade. Predefinição: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Especifica a porcentagem máxima tolerada de aplicativos implantados que podem não estar íntegros antes que o aplicativo seja considerado com erro. Essa porcentagem é calculada pela divisão do número de aplicativos implantados não íntegros durante o número de nós nos quais os aplicativos estão atualmente implantados no cluster. A computação arredonda até tolerar uma falha em pequenos números de nós. Porcentagem padrão: zero.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Especifica a política de integridade do tipo de serviço padrão, que substitui a política de integridade padrão para todos os tipos de serviço no aplicativo.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Fornece um mapa de políticas de integridade do serviço por tipo de serviço. Essas políticas substituem as políticas de integridade do tipo de serviço padrão para cada tipo de serviço especificado. Por exemplo, se um aplicativo tiver um tipo de serviço de gateway sem estado e um tipo de serviço de mecanismo com estado, você poderá configurar as políticas de integridade para sua avaliação de forma diferente. Ao especificar a política por tipo de serviço, você pode obter um controle mais granular da integridade do serviço.

### <a name="service-type-health-policy"></a>Política de integridade do tipo de serviço
A [política de integridade do tipo de serviço](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) especifica como avaliar e agregar os serviços e os filhos dos serviços. A política contém:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Especifica o percentual máximo tolerado de partições não íntegras antes que um serviço seja considerado não íntegro. Porcentagem padrão: zero.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Especifica o percentual máximo tolerado de réplicas não íntegras antes que uma partição seja considerada não íntegra. Porcentagem padrão: zero.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Especifica a porcentagem máxima tolerada de serviços não íntegros antes que o aplicativo seja considerado não íntegro. Porcentagem padrão: zero.

O exemplo a seguir é um trecho de um manifesto de aplicativo:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Avaliação de integridade
Os usuários e os serviços automatizados podem avaliar a integridade de qualquer entidade a qualquer momento. Para avaliar a integridade de uma entidade, o repositório de integridade agrega todos os relatórios de integridade na entidade e avalia todos os seus filhos (quando aplicável). O algoritmo de agregação de integridade usa políticas de integridade que especificam como avaliar relatórios de integridade e como agregar Estados de integridade filho (quando aplicável).

### <a name="health-report-aggregation"></a>Agregação do relatório de integridade
Uma entidade pode ter vários relatórios de integridade enviados por diferentes relatórios (componentes do sistema ou Watchdogs) em propriedades diferentes. A agregação usa as políticas de integridade associadas, especialmente o membro ConsiderWarningAsError da política de integridade de aplicativo ou de cluster. ConsiderWarningAsError especifica como avaliar os avisos.

O estado de integridade agregado é disparado pelos *piores* relatórios de integridade na entidade. Se houver pelo menos um relatório de integridade de erro, o estado de integridade agregado será um erro.

![Agregação de relatório de integridade com relatório de erros.][2]

Uma entidade de integridade que tem um ou mais relatórios de integridade de erro é avaliada como erro. O mesmo é verdadeiro para um relatório de integridade expirado, independentemente de seu estado de integridade.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se não houver nenhum relatório de erros e um ou mais avisos, o estado de integridade agregado será aviso ou erro, dependendo do sinalizador de política ConsiderWarningAsError.

![Agregação de relatório de integridade com relatório de aviso e ConsiderWarningAsError false.][3]

Agregação de relatório de integridade com relatório de aviso e ConsiderWarningAsError definido como false (padrão).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregação de integridade filho
O estado de integridade agregado de uma entidade reflete os Estados de integridade filho (quando aplicável). O algoritmo para agregar Estados de integridade filho usa as políticas de integridade aplicáveis com base no tipo de entidade.

![Agregação de integridade de entidades filho.][4]

Agregação filho com base nas políticas de integridade.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois que o repositório de integridade tiver avaliado todos os filhos, ele agregará seus Estados de integridade com base na porcentagem máxima configurada de filhos não íntegros. Esse percentual é obtido da política com base na entidade e no tipo filho.

* Se todos os filhos tiverem Estados OK, o estado de integridade agregado filho será OK.
* Se os filhos tiverem Estados OK e de aviso, o estado de integridade agregado filho será aviso.
* Se houver filhos com Estados de erro que não respeitam a porcentagem máxima permitida de filhos não íntegros, o estado de integridade do pai agregado será um erro.
* Se os filhos com Estados de erro respeitarem a porcentagem máxima permitida de filhos não íntegros, o estado de integridade do pai agregado será aviso.

## <a name="health-reporting"></a>Relatório de integridade
Os componentes do sistema, os aplicativos de malha do sistema e os Watchdogs internos/externos podem relatar Service Fabric entidades. Os relatórios fazem as determinações *locais* da integridade das entidades monitoradas, com base nas condições que estão monitorando. Eles não precisam examinar nenhum estado global ou dados agregados. O comportamento desejado é ter relatórios simples e não organismos complexos que precisam examinar muitas coisas para inferir quais informações enviar.

Para enviar dados de integridade para o repositório de integridade, um reporter precisa identificar a entidade afetada e criar um relatório de integridade. Para enviar o relatório, use a API [FabricClient. HealthClient. ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) , as APIs de integridade de relatório expostas nos objetos `Partition` ou `CodePackageActivationContext`, cmdlets do PowerShell ou REST.

### <a name="health-reports"></a>Relatórios de integridade
Os [relatórios de integridade](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) para cada uma das entidades no cluster contêm as seguintes informações:

* **SourceId**. Uma cadeia de caracteres que identifica exclusivamente o reporter do evento de integridade.
* **Identificador de entidade**. Identifica a entidade em que o relatório é aplicado. Ele difere com base no [tipo de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Em. Nenhum.
  * Nó. Nome do nó (cadeia de caracteres).
  * Aplicativo. Nome do aplicativo (URI). Representa o nome da instância do aplicativo implantada no cluster.
  * Serviço. Nome do serviço (URI). Representa o nome da instância de serviço implantada no cluster.
  * Particion. ID da partição (GUID). Representa o identificador exclusivo da partição.
  * Replica. A ID da réplica do serviço com estado ou a ID da instância de serviço sem estado (INT64).
  * DeployedApplication. Nome do aplicativo (URI) e nome do nó (cadeia de caracteres).
  * DeployedServicePackage. Nome do aplicativo (URI), nome do nó (cadeia de caracteres) e nome do manifesto do serviço (cadeia de caracteres).
* **Propriedade**. Uma *cadeia de caracteres* (não uma enumeração fixa) que permite ao reporter categorizar o evento de integridade para uma propriedade específica da entidade. Por exemplo, o reporter A pode relatar a integridade da propriedade "Storage" do Node01 e o reporter B pode relatar a integridade da propriedade "Connectivity" do Node01. No repositório de integridade, esses relatórios são tratados como eventos de integridade separados para a entidade Node01.
* **Descrição**. Uma cadeia de caracteres que permite a um relator fornecer informações detalhadas sobre o evento de integridade. **SourceID**, **Property**e **HealthState** devem descrever totalmente o relatório. A descrição adiciona informações legíveis ao relatório. O texto torna mais fácil para os administradores e usuários entenderem o relatório de integridade.
* **HealthState**. Uma [Enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de integridade do relatório. Os valores aceitos são OK, Warning e Error.
* **TimeToLive**. Um TimeSpan que indica por quanto tempo o relatório de integridade é válido. Junto com **RemoveWhenExpired**, ele permite que o repositório de integridade saiba como avaliar eventos expirados. Por padrão, o valor é infinito e o relatório é válido para sempre.
* **RemoveWhenExpired**. Um booliano. Se definido como true, o relatório de integridade expirado será removido automaticamente do repositório de integridade e o relatório não afetará a avaliação da integridade da entidade. Usado quando o relatório é válido apenas por um período de tempo especificado, e o reporter não precisa limpá-lo explicitamente. Ele também é usado para excluir relatórios do repositório de integridade (por exemplo, um Watchdog é alterado e para de enviar relatórios com a origem e a propriedade anteriores). Ele pode enviar um relatório com um breve TimeToLive junto com o RemoveWhenExpired para limpar qualquer estado anterior do repositório de integridade. Se o valor for definido como false, o relatório expirado será tratado como um erro na avaliação de integridade. O valor falso sinaliza para o repositório de integridade que a origem deve relatar periodicamente sobre essa propriedade. Caso contrário, deve haver algo errado com o Watchdog. A integridade do Watchdog é capturada considerando-se o evento como um erro.
* **SequenceNumber**. Um inteiro positivo que precisa estar cada vez maior, representa a ordem dos relatórios. Ele é usado pelo repositório de integridade para detectar relatórios obsoletos que são recebidos tardiamente devido a atrasos na rede ou outros problemas. Um relatório será rejeitado se o número de sequência for menor ou igual ao número aplicado mais recentemente para a mesma entidade, origem e propriedade. Se não for especificado, o número de sequência será gerado automaticamente. É necessário colocar o número de sequência somente ao relatar transições de estado. Nessa situação, a origem precisa lembrar quais relatórios foram enviados e manter as informações para recuperação no failover.

Essas quatro informações-SourceID, identificador de entidade, propriedade e HealthState – são necessárias para cada relatório de integridade. A cadeia de caracteres SourceID não pode iniciar com o prefixo "**System.** ", que é reservado para relatórios do sistema. Para a mesma entidade, há apenas um relatório para a mesma fonte e propriedade. Vários relatórios para a mesma origem e propriedade substituem uns aos outros, seja no lado do cliente de integridade (se estiverem em lotes) ou no lado do repositório de integridade. A substituição é baseada em números de sequência; os relatórios mais recentes (com números de sequência mais altos) substituem os relatórios mais antigos.

### <a name="health-events"></a>Eventos de integridade
Internamente, o repositório de integridade mantém os [eventos de integridade](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), que contêm todas as informações dos relatórios e os metadados adicionais. Os metadados incluem a hora em que o relatório foi fornecido ao cliente de integridade e a hora em que ele foi modificado no lado do servidor. Os eventos de integridade são retornados por [consultas de integridade](service-fabric-view-entities-aggregated-health.md#health-queries).

Os metadados adicionados contêm:

* **SourceUtcTimestamp**. A hora em que o relatório foi fornecido ao cliente de integridade (tempo Universal Coordenado).
* **LastModifiedUtcTimestamp**. A hora em que o relatório foi modificado pela última vez no lado do servidor (tempo Universal Coordenado).
* **IsExpired**. Um sinalizador para indicar se o relatório expirou quando a consulta foi executada pelo repositório de integridade. Um evento pode ser expirado somente se RemoveWhenExpired for false. Caso contrário, o evento não será retornado pela consulta e será removido do repositório.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. A última hora para transições de OK/aviso/erro. Esses campos fornecem o histórico das transições de estado de integridade para o evento.

Os campos de transição de estado podem ser usados para alertas mais inteligentes ou informações de eventos de integridade "históricas". Eles permitem cenários como:

* Alertar quando uma propriedade estiver em aviso/erro por mais de X minutos. A verificação da condição por um período de tempo evita alertas em condições temporárias. Por exemplo, um alerta se o estado de integridade tiver sido aviso por mais de cinco minutos pode ser convertido em (HealthState = = Warning e Now-LastWarningTransitionTime > 5 minutos).
* Alertar somente em condições que foram alteradas nos últimos X minutos. Se um relatório já estava em erro antes da hora especificada, ele pode ser ignorado porque já estava sinalizado anteriormente.
* Se uma propriedade estiver alternando entre aviso e erro, determine por quanto tempo ele não está íntegro (ou seja, não OK). Por exemplo, um alerta se a propriedade não tiver sido íntegra por mais de cinco minutos pode ser convertida em (HealthState! = OK e Now-LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Exemplo: relatar e avaliar a integridade do aplicativo
O exemplo a seguir envia um relatório de integridade por meio do PowerShell no Application **Fabric:/WordCount** da fonte **mywatchdog**. O relatório de integridade contém informações sobre a propriedade de integridade "disponibilidade" em um estado de integridade de erro, com TimeToLive infinito. Em seguida, ele consulta a integridade do aplicativo, que retorna erros de estado de integridade agregados e os eventos de integridade relatados na lista de eventos de integridade.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Uso do modelo de integridade
O modelo de integridade permite que os serviços de nuvem e a plataforma de Service Fabric subjacente sejam dimensionados, pois as determinações de monitoramento e integridade são distribuídas entre os diferentes monitores no cluster.
Outros sistemas têm um único serviço centralizado no nível de cluster que analisa todas as informações *potencialmente* úteis emitidas pelos serviços. Essa abordagem prejudica sua escalabilidade. Ele também não permite que eles coletem informações específicas para ajudar a identificar problemas e problemas potenciais o mais próximo possível da causa raiz.

O modelo de integridade é muito usado para monitoramento e diagnóstico, para avaliar a integridade do cluster e do aplicativo e para atualizações monitoradas. Outros serviços usam dados de integridade para executar reparos automáticos, criar histórico de integridade de cluster e emitir alertas em determinadas condições.

## <a name="next-steps"></a>Passos seguintes
[Exibir Service Fabric relatórios de integridade](service-fabric-view-entities-aggregated-health.md)

[Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adicionar relatórios de integridade de Service Fabric personalizados](service-fabric-report-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização do aplicativo Service Fabric](service-fabric-application-upgrade.md)

