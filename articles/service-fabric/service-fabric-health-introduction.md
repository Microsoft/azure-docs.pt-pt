---
title: Monitorização de estado de funcionamento no Service Fabric | Documentos da Microsoft
description: Uma introdução para o modelo, que fornece monitoramento de cluster e seus aplicativos e serviços de monitoramento de integridade de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d0ef9f34d6b657a063e50b0f144197c41905e809
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60949191"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introdução à monitorização do estado de funcionamento do Service Fabric
O Azure Service Fabric introduz um modelo de estado de funcionamento que fornece relatórios e de avaliação do Estado de funcionamento avançado, flexível e extensível. O modelo permite que quase em tempo real de monitorização do Estado do cluster e os serviços em execução no mesmo. Pode facilmente obter informações de estado de funcionamento e corrigir possíveis problemas antes de cascata e provocar interrupções em massa. No modelo típico, serviços enviam relatórios com base em suas exibições locais e que informações são agregadas para fornecer um geral cluster a nível de exibição.

Componentes do Service Fabric usam esse modelo de estado de funcionamento avançado para reportar o estado atual. Pode utilizar o mesmo mecanismo para Estado de funcionamento do relatório de seus aplicativos. Se investe no relatório de estado de funcionamento de alta qualidade, que captura suas condições personalizadas, pode detetar e corrigir problemas para a sua aplicação em execução muito mais facilidade.

> [!NOTE]
> Começamos o subsistema de estado de funcionamento para resolver uma necessidade de atualizações monitorizadas. O Service Fabric fornece atualizações monitorizadas do aplicativo e o cluster que garantir a disponibilidade total, sem períodos de indisponibilidade e mínima sem intervenção do utilizador. Para atingir esses objetivos, a atualização verifica o estado de funcionamento com base em políticas de atualização configuradas. Uma atualização possa prosseguir apenas quando o estado de funcionamento respeita os limiares pretendidos. Caso contrário, a atualização é automaticamente revertida ou colocada em pausa para dar aos administradores uma oportunidade de se corrigir os problemas. Para saber mais sobre as atualizações de aplicações, veja [este artigo](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Arquivo de estado de funcionamento
O arquivo de estado de funcionamento mantém informações relacionadas com o estado de funcionamento sobre entidades do cluster para fácil obtenção e avaliação. Ele é implementado como um recurso de infraestrutura de serviço persistidas serviço com estado, para garantir a elevada disponibilidade e escalabilidade. O arquivo de estado de funcionamento é parte do **fabric: / System** aplicativo e ele está disponível quando o cluster está ativo e em execução.

## <a name="health-entities-and-hierarchy"></a>Entidades de estado de funcionamento e a hierarquia
As entidades de estado de funcionamento estão organizadas numa hierarquia de lógica que captura as interações e dependências entre as diferentes entidades. O arquivo de estado de funcionamento cria automaticamente as entidades de estado de funcionamento e a hierarquia com base em relatórios recebidos de componentes do Service Fabric.

As entidades de estado de funcionamento espelham as entidades do Service Fabric. (Por exemplo, **entidade de aplicativo de estado de funcionamento** corresponde a uma instância de aplicação implementado no cluster, embora **entidade de nó de estado de funcionamento** corresponde a um nó de cluster do Service Fabric.) A hierarquia de estado de funcionamento captura as interações das entidades de sistema e é a base para a avaliação de estado de funcionamento avançadas. Pode aprender sobre os principais conceitos do Service Fabric no [visão geral técnica do Service Fabric](service-fabric-technical-overview.md). Para saber mais sobre a aplicação, consulte [modelo de aplicação do Service Fabric](service-fabric-application-model.md).

As entidades de estado de funcionamento e a hierarquia de permitir que o cluster e as aplicações sejam efetivamente comunicado, depurado e monitorizados. O modelo de estado de funcionamento fornece uma lista exata *granular* representação do Estado de funcionamento das muitas peças móveis no cluster.

![Entidades de estado de funcionamento.][1]
As entidades de estado de funcionamento, organizados numa hierarquia com base nas relações pai-filho.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de estado de funcionamento são:

* **Cluster**. Representa o estado de funcionamento de um cluster do Service Fabric. Relatórios de estado de funcionamento do cluster descrevem as condições que afetam todo o cluster. Estas condições afetam várias entidades no cluster ou o próprio cluster. Com base na condição, o gerador de relatórios não é possível restringir o problema para baixo para uma ou mais filhos mau estado de funcionamento. Os exemplos incluem o cérebro do cluster divisão devido a problemas de criação de partições ou a comunicação de rede.
* **Nó**. Representa o estado de funcionamento de um nó do Service Fabric. Relatórios de estado de funcionamento do nó descrevem as condições que afetam a funcionalidade de nó. Normalmente, eles afetam todas as entidades implementadas em execução no mesmo. Os exemplos incluem o nó sem espaço em disco (ou outras propriedades de todo o computador, como memória, ligações) e quando um nó estiver em baixo. A entidade de nó é identificada pelo nome do nó (cadeia).
* **Aplicação**. Representa o estado de funcionamento de uma instância de aplicação em execução no cluster. Relatórios de estado de funcionamento da aplicação descrevem as condições que afetam o estado de funcionamento geral do aplicativo. Não pode ser reduzidas para filho individual (serviços ou aplicações implementadas). Os exemplos incluem a interação de ponto a ponto entre diferentes serviços no aplicativo. A entidade de aplicação é identificada pelo nome da aplicação (URI).
* **Serviço**. Representa o estado de funcionamento de um serviço em execução no cluster. Relatórios de estado de funcionamento do serviço de descrevem as condições que afetam o estado de funcionamento geral do serviço. O gerador de relatórios não é possível restringir o problema para uma partição de mau estado de funcionamento ou a réplica. Os exemplos incluem uma configuração de serviço (por exemplo, a porta ou partilha de ficheiros externos) que está a causar problemas para todas as partições. A entidade de serviço é identificada pelo nome do serviço (URI).
* **Partição**. Representa o estado de funcionamento de uma partição de serviço. Relatórios de estado de funcionamento de partição descrevem as condições que afetam o conjunto de réplicas de todo. Os exemplos incluem quando o número de réplicas é inferior a contagem de destino e quando uma partição é de perda de quórum. A entidade de partição é identificada por partição ID (GUID).
* **Réplica**. Representa o estado de funcionamento de uma réplica de serviço com estado ou uma instância de serviço sem estado. A réplica é a menor unidade watchdogs e componentes do sistema podem reportar para uma aplicação. Para serviços com estado, os exemplos incluem uma réplica primária, que não é possível replicar operações com a replicação lenta e de bases de dados secundárias. Além disso, uma instância sem monitoração de estado pode comunicar quando este está a ficar sem recursos ou problemas de conectividade. A entidade de réplica é identificada, a ID (GUID) da partição e o ID de réplica ou de instância (longa).
* **DeployedApplication**. Representa o estado de funcionamento de um *aplicação em execução num nó*. Relatórios de estado de funcionamento da aplicação implementada descrevem condições específicas para a aplicação no nó que não pode ser reduzido para pacotes de serviço implementados no mesmo nó. Os exemplos incluem erros quando não é possível transferir o pacote de aplicação nesse nó e problemas de configuração de entidades de segurança de aplicação no nó. A aplicação implementada é identificada pelo nome da aplicação (URI) e o nome de nó (cadeia).
* **DeployedServicePackage**. Representa o estado de funcionamento de um pacote de serviço em execução num nó do cluster. Ele descreve a condições específicas para um pacote de serviço que não afetam os outros pacotes de serviço no mesmo nó para a mesma aplicação. Os exemplos incluem um pacote do código no pacote de serviço que não é possível iniciar e um pacote de configuração que não é possível ler. O pacote de serviço implementado é identificado pelo nome da aplicação (URI), o nome de nó (cadeia), nome do manifesto do serviço (cadeia) e o ID de ativação de pacote de serviço (cadeia).

A granularidade do modelo de estado de funcionamento torna mais fácil detetar e corrigir problemas. Por exemplo, se um serviço de mensagens em fila não está a responder, é viável para comunicar que a instância da aplicação está danificada. Relatórios em que nível não é ideal, no entanto, uma vez que o problema não possa estar afetando todos os serviços dentro desse aplicativo. O relatório deve ser aplicado para o serviço em mau estado de funcionamento ou para uma partição filho específico, se mais informações apontar para essa partição. Os dados automaticamente superfícies através da hierarquia e uma partição em mau estado de funcionamento ficam visíveis em níveis de serviços e de aplicações. Esta agregação ajuda a identificar e resolver a causa de raiz do problema mais rapidamente.

A hierarquia de estado de funcionamento é composta por relações pai-filho. Um cluster é composto por nós e aplicativos. Os aplicativos tem os serviços e aplicações implementadas. Aplicações implementadas tem implementado os pacotes de serviço. Os serviços têm partições, e cada partição tem uma ou mais réplicas. Há um relacionamento especial entre nós e entidades implementadas. Um nó em mau estado de funcionamento conforme comunicado pelo seu componente de sistema de autoridade, o serviço de Gestor de ativação pós-falha, afeta a aplicações implementadas, os pacotes de serviço e réplicas implementadas no mesmo.

A hierarquia de estado de funcionamento representa o estado mais recente do sistema com base em relatórios de estado de funcionamento mais recente, que é informações quase em tempo real.
As mesmas entidades com base na lógica específica de aplicativo ou condições monitorizadas personalizadas podem reportar watchdogs internos e externos. Relatórios de utilizador coexistam com os relatórios do sistema.

Planeje para investir em como informar e responder a Estado de funcionamento durante o design de um serviço cloud de grandes dimensões. Esse investimento inicial torna mais fácil depurar, monitorar e operar o serviço.

## <a name="health-states"></a>Estados de funcionamento
O Service Fabric utiliza três Estados de funcionamento para descrever se uma entidade é bom estado de funcionamento ou não: OK, aviso e erro. Qualquer relatório enviado para o arquivo de estado de funcionamento tem de especificar um desses Estados. O resultado da avaliação de estado de funcionamento é um desses Estados.

Os possíveis [Estados de funcionamento](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) são:

* **OK**. A entidade está em bom estada. Não existem não existem problemas conhecidos relatados no mesmo ou seus filhos (quando aplicável).
* **Aviso**. A entidade tem alguns problemas, mas que ainda possa funcionar corretamente. Por exemplo, existem atrasos, mas eles não causam problemas funcionais ainda. Em alguns casos, a condição de aviso pode corrigir problema em si sem a intervenção do externa. Nestes casos, os relatórios de estado de funcionamento consciencialização e fornecem visibilidade sobre o que está acontecendo. Em outros casos, a condição de aviso pode degradar-se um problema grave sem intervenção do utilizador.
* **Erro**. A entidade está danificada. Deverão ser executadas ações para corrigir o estado da entidade, porque ele não funcionará corretamente.
* **Desconhecido**. A entidade não existe no arquivo de estado de funcionamento. Este resultado pode ser obtido das consultas distribuídas que unir os resultados de vários componentes. Por exemplo, a consulta de lista de nó de get vai para **FailoverManager**, **ClusterManager**, e **HealthManager**; colocar a aplicação vai de consulta da lista para  **ClusterManager** e **HealthManager**. Estas consultas intercalar os resultados dos vários componentes de sistema. Se outro componente do sistema retorna uma entidade que não está presente no arquivo de estado de funcionamento, o resultado intercalado má neznámou estado de funcionamento. Uma entidade não está no arquivo porque ainda não foram processados relatórios de estado de funcionamento ou a entidade foram limpos após a eliminação.

## <a name="health-policies"></a>Políticas de estado de funcionamento
O arquivo de estado de funcionamento aplica políticas de estado de funcionamento para determinar se uma entidade é bom estado de funcionamento com base nos seus relatórios e seus filhos.

> [!NOTE]
> Políticas de estado de funcionamento podem ser especificadas no manifesto do cluster (para avaliação de estado de funcionamento do cluster e nó) ou no manifesto do aplicativo (para avaliação de aplicativo e qualquer um dos respetivos subordinados). Pedidos de avaliação do Estado de funcionamento podem também passar nas políticas de avaliação do Estado de funcionamento personalizadas, que são utilizadas apenas para essa avaliação.
> 
> 

Por predefinição, o Service Fabric aplica regras rígidas (tudo o que tem de ser bom estado de funcionamento) para a relação hierárquica de principal-subordinado. Se ainda um dos filhos tem um evento mau estado de funcionamento, o pai é considerado incorreto.

### <a name="cluster-health-policy"></a>Política de estado de funcionamento do cluster
O [política de estado de funcionamento do cluster](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) é utilizado para avaliar o estado de funcionamento do cluster e Estados de funcionamento do nó. A política pode ser definida no manifesto do cluster. Se não estiver presente, é utilizada a política predefinida (zero falhas toleradas).
Contém a política de estado de funcionamento do cluster:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se tratar de estado de funcionamento de aviso relatórios como erros durante a avaliação de estado de funcionamento. Predefinição: FALSO.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Especifica a percentagem máxima de tolerado de aplicativos que podem ser mau estado de funcionamento antes do cluster será considerado como erro.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Especifica a percentagem de tolerado máxima de nós que podem ser mau estado de funcionamento antes do cluster será considerado como erro. Em grandes clusters, alguns nós são sempre verticalmente ou horizontalmente para reparos, para que esta percentagem deve ser configurada para tolerar que.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). O mapa de política de estado de funcionamento de tipo de aplicativo pode ser utilizado durante a avaliação de estado de funcionamento do cluster para descrever os tipos de aplicativos especiais. Por predefinição, todos os aplicativos colocar num conjunto e avaliados com MaxPercentUnhealthyApplications. Se alguns tipos de aplicações devem ser tratados de forma diferente, ele é levado para fora do conjunto global. Em vez disso, eles são avaliados contra as porcentagens associadas com os respetivos nomes de tipo de aplicativo no mapa. Por exemplo, num cluster existem milhares de aplicativos de diferentes tipos e algumas instâncias de aplicativo de controle de um tipo especial de aplicação. Os aplicativos de controle nunca devem estar num erro. Pode especificar MaxPercentUnhealthyApplications global para 20% a tolerar algumas falhas, mas para o tipo de aplicação "ControlApplicationType" definir o MaxPercentUnhealthyApplications como 0. Dessa forma, se alguns dos muitos aplicativos são mau estado de funcionamento, mas abaixo da percentagem de mau estado de funcionamento global, o cluster seria avaliado para aviso. Um Estado de funcionamento de aviso não afeta a atualização do cluster ou outros monitorização acionadas por Estado de funcionamento de erro. Mas até mesmo um controle de aplicativo no erro tornaria cluster mau estado de funcionamento, o que aciona a reversão ou interrompe a atualização do cluster, dependendo da configuração de atualização.
  Para os tipos de aplicativos definidos no mapa, todas as instâncias da aplicação são retiradas do conjunto global de aplicativos. Eles são avaliados com base no número total de aplicativos do tipo de aplicação, utilizando o MaxPercentUnhealthyApplications específico do mapa. Todo o resto dos aplicativos permanecem no conjunto global e são avaliadas com MaxPercentUnhealthyApplications.

O exemplo seguinte é um trecho de um manifesto do cluster. Para definir as entradas no mapa de tipo de aplicação, preceder o nome de parâmetro com "ApplicationTypeMaxPercentUnhealthyApplications-", seguido do nome de tipo de aplicação.

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

### <a name="application-health-policy"></a>Política de estado de funcionamento de aplicações
O [política de estado de funcionamento de aplicações](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) descreve como a avaliação de agregação de eventos e Estados filho é feita para aplicativos e seus filhos. Ele pode ser definido no manifesto do aplicativo, **Applicationmanifest**, no pacote de aplicação. Se forem especificadas não existem políticas, Service Fabric parte do princípio de que a entidade é mau estado de funcionamento se tiver um relatório de estado de funcionamento ou um subordinado, o estado de funcionamento de aviso ou erro.
As políticas configuráveis são:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se tratar de estado de funcionamento de aviso relatórios como erros durante a avaliação de estado de funcionamento. Predefinição: FALSO.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Especifica a percentagem de tolerado máxima de aplicações que podem ser mau estado de funcionamento antes do aplicativo é considerado como erro. Esta percentagem é calculada dividindo o número de aplicações implementadas mau estado de funcionamento sobre o número de nós que os aplicativos são implantados no momento no cluster. A computação Arredonda por excesso para tolerar uma falha num pequeno número de nós. Padrão de percentagem: zero.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Especifica a serviço tipo estado de funcionamento política predefinida, que substitui a política de estado de funcionamento padrão para todos os tipos de serviço no aplicativo.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Fornece um mapa de políticas de estado de funcionamento do serviço por tipo de serviço. Estas políticas substituem as políticas de estado de funcionamento do tipo de serviço padrão para cada tipo de serviço especificado. Por exemplo, se um aplicativo tem um tipo de serviço do gateway sem monitoração de estado e um tipo de serviço do motor com monitoração de estado, pode configurar as políticas de estado de funcionamento para a respetiva avaliação de forma diferente. Quando especificar política de por tipo de serviço, pode obter um controle mais granular do Estado de funcionamento do serviço.

### <a name="service-type-health-policy"></a>Política de estado de funcionamento do tipo de serviço
O [política de estado de funcionamento do tipo de serviço](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) Especifica como avaliar e agregar os serviços e os filhos de serviços. A política contém:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Especifica a percentagem de tolerado máxima de partições de mau estado de funcionamento antes de um serviço é considerado em mau estado de funcionamento. Padrão de percentagem: zero.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Especifica a percentagem máxima de tolerado das réplicas de mau estado de funcionamento antes de uma partição é considerada em mau estado de funcionamento. Padrão de percentagem: zero.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Especifica a percentagem máxima de tolerado dos serviços de mau estado de funcionamento antes do aplicativo é considerado em mau estado de funcionamento. Padrão de percentagem: zero.

O exemplo seguinte é um trecho de um manifesto de aplicativo:

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

## <a name="health-evaluation"></a>Avaliação do Estado de funcionamento
Utilizadores e serviços automatizados podem avaliar o estado de funcionamento de qualquer entidade em qualquer altura. Para avaliar o estado de funcionamento de uma entidade, os agregados de arquivo de estado de funcionamento estado de funcionamento de todos os relatórios na entidade e avalia todos os respetivos subordinados (quando aplicável). O algoritmo de agregação do Estado de funcionamento utiliza políticas de estado de funcionamento que especificam avaliar os relatórios de estado de funcionamento e a agregar Estados de funcionamento do filho (quando aplicável).

### <a name="health-report-aggregation"></a>Agregação de relatório de estado de funcionamento
Uma entidade pode ter vários relatórios de estado de funcionamento enviados pelo Informadores diferentes (componentes do sistema ou watchdogs) nas propriedades diferentes. A agregação utiliza as políticas de estado de funcionamento associado, em particular o membro ConsiderWarningAsError da política de estado de funcionamento da aplicação ou o cluster. ConsiderWarningAsError Especifica como avaliar avisos.

O estado de funcionamento agregado é acionado pela *pior* os relatórios de estado na entidade. Se houver um relatório de estado de funcionamento de pelo menos um erro, o estado de funcionamento agregado é um erro.

![Agregação de relatório de estado de funcionamento com o relatório de erros.][2]

Uma entidade de estado de funcionamento que tem um ou mais relatórios de estado de funcionamento de erro é avaliada como erro. O mesmo é verdadeiro para um relatório de estado de funcionamento expirados, independentemente do seu estado de funcionamento.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se existirem não existem relatórios de erros e um ou mais avisos, o estado de funcionamento agregado é aviso ou erro, consoante o sinalizador de política de ConsiderWarningAsError.

![Agregação de relatório de estado de funcionamento com o relatório de aviso e ConsiderWarningAsError false.][3]

Agregação de relatório de estado de funcionamento com o relatório de aviso e ConsiderWarningAsError definido como false (predefinição).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregação do Estado de funcionamento de subordinados
O estado de funcionamento agregado de uma entidade reflete os Estados de funcionamento do filho (quando aplicável). O algoritmo para agregar Estados de funcionamento do filho utiliza as políticas de estado de funcionamento aplicáveis com base no tipo de entidade.

![Agregação do Estado de funcionamento de entidades de subordinados.][4]

Agregação de subordinados com base nas políticas de estado de funcionamento.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois do arquivo de estado de funcionamento ter avaliado todos os filhos, agrega os Estados de funcionamento com base na percentagem máxima configurada de subordinados mau estado de funcionamento. Essa porcentagem é retirada da política com base no tipo de entidade e subordinados.

* Se todos os filhos tem Estados OK, o estado de funcionamento de filhos agregados está OK.
* Se os menores tenham OK e Estados de aviso, o estado de funcionamento de filhos agregados indica um aviso.
* Se existirem filhos com os Estados de erro que não respeitam o máximo permitido de percentagem dos filhos mau estado de funcionamento, o estado de funcionamento agregado principal é um erro.
* Se os filhos com os Estados de erro respeitam a percentagem de permitido máxima de subordinados mau estado de funcionamento, o estado de funcionamento do principal de agregados indica um aviso.

## <a name="health-reporting"></a>Relatórios de estado de funcionamento
Componentes do sistema, os aplicativos de recursos de infraestrutura do sistema e watchdogs interna/externa podem relatar em relação a entidades do Service Fabric. Tornam as jornalistas *local* determinações do Estado de funcionamento das entidades monitorizados, com base nas condições que estão a monitorizar. Não precisam de observar qualquer Estado global ou os dados agregados. O comportamento desejado é ter Informadores simples e complexos não organisms que precisam verificar muitas coisas para inferir as informações que enviar.

Para enviar dados de estado de funcionamento para o arquivo de estado de funcionamento, um gerador de relatórios tem de identificar a entidade afetada e criar um relatório de estado de funcionamento. Para enviar o relatório, utilize o [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, o estado de funcionamento do relatório APIs expostas no `Partition` ou `CodePackageActivationContext` objetos, cmdlets do PowerShell ou REST.

### <a name="health-reports"></a>Relatórios de estado de funcionamento
O [relatórios de estado de funcionamento](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) para cada uma dessas entidades no cluster de conter as seguintes informações:

* **SourceId**. Uma cadeia que identifica exclusivamente o gerador de relatórios do evento de estado de funcionamento.
* **Identificador da entidade**. Identifica a entidade em que o relatório é aplicado. É diferente consoante o [tipo de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Cluster. Nenhum.
  * Nó. Nome do nó (cadeia).
  * aplicação. Nome da aplicação (URI). Representa o nome da instância da aplicação implementada no cluster.
  * Serviço. Nome do serviço (URI). Representa o nome da instância do serviço implementado no cluster.
  * Partição. ID de partição (GUID). Representa o identificador exclusivo da partição.
  * Réplica. O ID de réplica de serviço com estado ou o ID de instância de serviço sem estado (INT64).
  * DeployedApplication. Nome da aplicação (URI) e o nome de nó (cadeia).
  * DeployedServicePackage. Nome da aplicação (URI), o nome de nó (cadeia) e o serviço de manifesto nome (cadeia).
* **Propriedade**. R *cadeia de caracteres* (não uma enumeração fixa) que permite que o gerador de relatórios para categorizar o evento de estado de funcionamento para uma propriedade específica da entidade. Por exemplo, o gerador de relatórios A pode reportar o estado de funcionamento a Node01 propriedade de "Armazenamento" e o gerador de relatórios de B podem reportar o estado de funcionamento a Node01 propriedade de "Conectividade". No arquivo de estado de funcionamento, esses relatórios são tratados como eventos de estado de funcionamento separado para a entidade de Node01.
* **Descrição**. Uma cadeia de caracteres que permite que um gerador de relatórios fornecer informações detalhadas sobre o evento de estado de funcionamento. **SourceId**, **propriedade**, e **HealthState** deve descrever completamente o relatório. A descrição adiciona legível por humanos informações sobre o relatório. O texto torna mais fácil para os administradores e utilizadores a compreender o relatório de estado de funcionamento.
* **HealthState**. Uma [enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de funcionamento do relatório. Os valores aceites são OK, aviso e erro.
* **TimeToLive**. Um intervalo de tempo que indica o tempo que o relatório de estado de funcionamento do é válido. Juntamente com **RemoveWhenExpired**, ele permite que o arquivo de estado de funcionamento que sabe como avaliar eventos expirados. Por predefinição, o valor é infinito e o relatório é válido para sempre.
* **RemoveWhenExpired**. Um booleano. Se definido como true, o relatório de estado de funcionamento expirada é removido automaticamente do arquivo de estado de funcionamento e o relatório não tem impacto na avaliação de estado de funcionamento da entidade. Utilizado quando o relatório é válido durante um determinado período de tempo apenas e não tem o gerador de relatórios de explicitamente limpá-la. Também é utilizado para eliminar relatórios a partir do arquivo de estado de funcionamento (por exemplo, um watchdog é alterado e interrompe o envio de relatórios com propriedade e a origem anterior). Ele pode enviar um relatório com uma breve TimeToLive juntamente com RemoveWhenExpired para limpar qualquer estado anterior do arquivo de estado de funcionamento. Se o valor é definido como false, o relatório expirado é tratado como um erro na avaliação de estado de funcionamento. Os sinais de valor falso para o arquivo de estado de funcionamento que a origem deve reportar periodicamente tuto vlastnost. Se não, em seguida, tem de existir um problema com o watchdog. Estado de funcionamento do watchdog é capturado pelo Considerando o evento como um erro.
* **SequenceNumber**. Um número inteiro positivo que tem de ser cada vez, ele representa a ordem dos relatórios. É utilizado pelo arquivo de estado de funcionamento para detetar obsoletos relatórios que são recebidos tarde devido a atrasos de rede ou outros problemas. Um relatório será rejeitado se o número de sequência é que menor ou igual a mais recentemente aplicadas número para a mesma entidade, origem e a propriedade. Se não for especificado, o número de sequência é gerado automaticamente. É necessário colocar o número de sequência apenas ao gerar relatórios sobre as transições de estado. Nesta situação, a origem precisa Lembre-se de que ele enviado de relatórios e manter as informações de recuperação na ativação pós-falha.

Essas quatro partes de informações – SourceId, identificador da entidade, propriedade e HealthState – são necessários para cada relatório de estado de funcionamento. A cadeia de caracteres SourceId não tem permissão para iniciar com o prefixo "**System.** ", que está reservado para relatórios do sistema. Para a mesma entidade, existe apenas um relatório para a mesma origem e a propriedade. Vários relatórios para a mesma origem e a propriedade substituam entre si, no lado do cliente de estado de funcionamento (se eles são limitativas) ou no estado de funcionamento armazenar lado. A substituição se baseia em números de sequência; relatórios mais recente (com números de sequência mais elevados) substituem relatórios mais antigos.

### <a name="health-events"></a>Eventos de estado de funcionamento
Internamente, o arquivo de estado de funcionamento mantém [eventos de estado de funcionamento](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), que contêm todas as informações dos relatórios e os metadados adicionais. Os metadados incluem o tempo que o relatório foi atribuído ao cliente de estado de funcionamento e o tempo que foi modificada no lado do servidor. Os eventos de estado de funcionamento são devolvidos pelo [consultas de estado de funcionamento](service-fabric-view-entities-aggregated-health.md#health-queries).

Contém os metadados adicionados:

* **SourceUtcTimestamp**. O tempo que o relatório foi indicado para o cliente do Estado de funcionamento (hora de Universal Coordenada).
* **LastModifiedUtcTimestamp**. A hora em que o relatório foi modificado pela última vez no lado do servidor (hora de Universal Coordenada).
* **IsExpired**. Um sinalizador para indicar se o relatório foi a expirou quando a consulta foi executada pelo arquivo de estado de funcionamento. Um evento pode ter expirado apenas se RemoveWhenExpired é false. Caso contrário, o evento não é devolvido pela consulta e é removido da loja.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. A última hora de transições de OK/aviso/erro. Estes campos dar o histórico do Estado de funcionamento, as transições de estado para o evento.

Os campos de transição de estado podem ser utilizados para obter informações de eventos de estado de funcionamento "históricos" ou alertas mais inteligentes. Eles permitem cenários, tais como:

* Alerta quando uma propriedade tiver sido aviso/erro para mais de X minutos. A verificação da condição durante um período de tempo evita alertas em condições temporárias. Por exemplo, um alerta se o estado de funcionamento tem sido aviso para a mais de cinco minutos pode ser convertido numa (HealthState = = aviso e LastWarningTransitionTime agora - > 5 minutos).
* Alerta apenas em condições que foram alterados nos últimos X minutos. Se um relatório já estava em erro antes do período de tempo especificado, pode ser ignorado porque já foi sinalizado anteriormente.
* Se uma propriedade é alternar entre o aviso e erro, determine o período de tempo tem sido mau estado de funcionamento (OK ou seja, não). Por exemplo, um alerta se a propriedade ainda não estiver em bom estado durante mais de cinco minutos pode ser convertido numa (HealthState! = Ok e agora - LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Exemplo: Comunicar e avaliar o estado de funcionamento da aplicação
O exemplo seguinte envia um relatório de estado de funcionamento através do PowerShell no aplicativo **fabric: / WordCount** da origem **MyWatchdog**. O relatório de estado de funcionamento contém informações sobre a propriedade de estado de funcionamento "availability" num Estado de funcionamento de erro, com TimeToLive infinito. Em seguida, ele consulta o estado de funcionamento do aplicativo, que retorna agregados erros de estado de funcionamento e os eventos de estado de funcionamento reportados na lista de eventos de estado de funcionamento.

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

## <a name="health-model-usage"></a>Utilização do modelo de estado de funcionamento
O modelo de estado de funcionamento permite que serviços cloud e a plataforma subjacente do Service Fabric para dimensionar, porque determinações do tipo de monitorização e estado de funcionamento são distribuídas entre os monitores diferentes dentro do cluster.
Outros sistemas de tem um único serviço centralizado no nível do cluster que analisa todos os *potencialmente* emitidas pelos serviços de informações úteis. Essa abordagem impede a escalabilidade deles. Ela também não permite-lhes para recolher informações específicas para ajudar a identificar problemas e os potenciais problemas como próximo a causa possível.

O modelo de estado de funcionamento é utilizado intensamente para monitorização e diagnóstico, por avaliar o estado de funcionamento do cluster e a aplicação e para atualizações monitorizadas. Outros serviços utilizam dados de estado de funcionamento para realizar reparações automáticas, histórico de estado de funcionamento do cluster de criar e emitir alertas em determinadas condições.

## <a name="next-steps"></a>Passos Seguintes
[Ver relatórios de estado de funcionamento do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Utilizar relatórios de estado de funcionamento do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como comunicar e verificar o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adicionar relatórios de estado de funcionamento personalizados do Service Fabric](service-fabric-report-health.md)

[Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)

