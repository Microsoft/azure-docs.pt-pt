---
title: Monitorização da saúde em Tecido de Serviço
description: Uma introdução ao modelo de monitorização da saúde do Azure Service Fabric, que proporciona a monitorização do cluster e das suas aplicações e serviços.
ms.topic: conceptual
ms.date: 2/28/2018
ms.openlocfilehash: a1c545048739182e3baba3e3d94da1accca227d1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627420"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introdução à monitorização do estado de funcionamento do Service Fabric
A Azure Service Fabric introduz um modelo de saúde que proporciona uma avaliação e reporte de saúde rica, flexível e extensível. O modelo permite um acompanhamento quase em tempo real do estado do cluster e dos serviços que nele se insem. Você pode facilmente obter informações de saúde e corrigir potenciais problemas antes que eles em cascata e causar falhas massivas. No modelo típico, os serviços enviam relatórios com base nas suas opiniões locais, e essa informação é agregada para fornecer uma visão global ao nível do cluster.

Os componentes do Tecido de Serviço usam este rico modelo de saúde para reportar o seu estado atual. Pode utilizar o mesmo mecanismo para reportar a saúde das suas aplicações. Se investir em relatórios de saúde de alta qualidade que captam as suas condições personalizadas, pode detetar e corrigir problemas para a sua aplicação de execução muito mais facilmente.

> [!NOTE]
> Iniciámos o subsistema de saúde para responder à necessidade de atualizações monitorizadas. O Service Fabric fornece atualizações de aplicações e clusters monitorizadas que garantem a disponibilidade total, sem tempo de inatividade e sem qualquer intervenção do utilizador. Para atingir estes objetivos, a atualização verifica a saúde com base em políticas de upgrade configuradas. Uma atualização só pode prosseguir quando a saúde respeita os limiares pretendidos. Caso contrário, a atualização é automaticamente revirada ou interrompida para dar aos administradores a oportunidade de corrigir os problemas. Para saber mais sobre atualizações de aplicações, consulte [este artigo.](service-fabric-application-upgrade.md)
> 
> 

## <a name="health-store"></a>Loja de saúde
A loja de saúde mantém informações relacionadas com a saúde sobre entidades do cluster para fácil recuperação e avaliação. É implementado como um Tecido de Serviço persistido serviço estatal para garantir alta disponibilidade e escalabilidade. A loja de saúde faz parte da aplicação **do tecido:/Sistema,** e está disponível quando o cluster está em funcionamento.

## <a name="health-entities-and-hierarchy"></a>Entidades de saúde e hierarquia
As entidades de saúde organizam-se numa hierarquia lógica que capta interações e dependências entre diferentes entidades. A loja de saúde constrói automaticamente entidades de saúde e hierarquia com base em relatórios recebidos de componentes do Service Fabric.

As entidades de saúde espelham as entidades do Service Fabric. (Por exemplo, **a entidade de aplicação de saúde** corresponde a uma instância de aplicação implantada no cluster, enquanto a **entidade do nó de saúde** corresponde a um nó de cluster de tecido de serviço.) A hierarquia da saúde capta as interações das entidades do sistema, e é a base para uma avaliação avançada da saúde. Pode aprender sobre os principais conceitos de Tecido de Serviço na [visão técnica do Service Fabric.](service-fabric-technical-overview.md) Para mais informações sobre a aplicação, consulte [o modelo de aplicação Service Fabric.](service-fabric-application-model.md)

As entidades de saúde e a hierarquia permitem que o cluster e as aplicações sejam efetivamente reportados, depurados e monitorizados. O modelo de saúde proporciona uma representação *granular* precisa da saúde das muitas peças móveis do aglomerado.

![Entidades de saúde.][1]
As entidades de saúde, organizadas numa hierarquia baseada nas relações pai-filho.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de saúde são:

* **Cluster**. Representa a saúde de um cluster de Tecidos de Serviço. Relatórios de saúde do cluster descrevem condições que afetam todo o cluster. Estas condições afetam várias entidades do cluster ou do próprio cluster. Com base na condição, o repórter não pode reduzir o assunto a uma ou mais crianças insalubres. Exemplos incluem o cérebro do cluster dividindo-se devido a divisórias de rede ou problemas de comunicação.
* **Nó**. Representa a saúde de um nó de tecido de serviço. Os relatórios de saúde do nó descrevem as condições que afetam a funcionalidade do nó. Normalmente afetam todas as entidades implantadas que o executam. Exemplos incluem nó fora do espaço do disco (ou outras propriedades em toda a máquina, como memória, conexões) e quando um nó está para baixo. A entidade nódissódia é identificada pelo nome do nó (string).
* **Aplicação**. Representa a saúde de um caso de aplicação em execução no cluster. Os relatórios de saúde da aplicação descrevem condições que afetam a saúde geral da aplicação. Não podem ser restringidos a crianças individuais (serviços ou aplicações implantadas). Exemplos incluem a interação de ponta a ponta entre diferentes serviços na aplicação. A entidade de aplicação é identificada pelo nome de aplicação (URI).
* **Serviço**. Representa a saúde de um serviço em execução no cluster. Os relatórios de saúde do serviço descrevem condições que afetam a saúde geral do serviço. O repórter não pode limitar a questão a uma divisão ou réplica pouco saudável. Exemplos incluem uma configuração de serviço (como a parte de ficheiros porta ou externa) que está a causar problemas para todas as divisórias. A entidade de serviço é identificada pelo nome de serviço (URI).
* **Partição.** Representa a saúde de uma divisória de serviço. Os relatórios de saúde da partição descrevem as condições que afetam todo o conjunto de réplicas. Exemplos incluem quando o número de réplicas está abaixo da contagem do alvo e quando uma partição está em perda de quórum. A entidade de partição é identificada pelo ID de partição (GUID).
* **Réplica.** Representa a saúde de uma réplica de serviço estatal ou de uma instância de serviço apátrida. A réplica é a unidade mais pequena que os cães de guarda e os componentes do sistema podem reportar para uma aplicação. Para serviços estatais, exemplos incluem uma réplica primária que não pode replicar operações para os secundários e replicação lenta. Além disso, um caso apátrida pode reportar quando está a ficar sem recursos ou tem problemas de conectividade. A entidade de réplica é identificada pelo ID de partição (GUID) e pela réplica ou iD de instância (longo).
* **Implementação Aplicação de Aplicação**. Representa a saúde de uma *aplicação em execução num nó*. Os relatórios de saúde de aplicações implantados descrevem condições específicas da aplicação no nó que não podem ser reduzidas aos pacotes de serviço implantados no mesmo nó. Exemplos incluem erros quando o pacote de aplicações não pode ser descarregado nesse nó e problemas que configuram os principais de segurança da aplicação no nó. A aplicação implementada é identificada pelo nome de aplicação (URI) e nome do nó (cadeia).
* **Serviço de Embalagem implantado**. Representa a saúde de um pacote de serviço que funciona num nó no aglomerado. Descreve condições específicas de um pacote de serviços que não afetam os outros pacotes de serviços no mesmo nó para a mesma aplicação. Exemplos incluem um pacote de código no pacote de serviço que não pode ser iniciado e um pacote de configuração que não pode ser lido. O pacote de serviço implantado é identificado pelo nome de aplicação (URI), nome do nó (string), nome manifesto de serviço (cadeia) e ID de ativação do pacote de serviço (cadeia).

A granularidade do modelo de saúde facilita a deteção e correção de problemas. Por exemplo, se um serviço não estiver a responder, é possível informar que a instância de aplicação não é saudável. No entanto, a comunicação a este nível não é o ideal, uma vez que a questão pode não estar a afetar todos os serviços dentro dessa aplicação. O relatório deve ser aplicado ao serviço pouco saudável ou a uma partição específica de crianças, se mais informações apontam para essa partição. Os dados surgem automaticamente através da hierarquia, e uma divisória pouco saudável é tornada visível nos níveis de serviço e aplicação. Esta agregação ajuda a identificar e resolver a causa principal do problema mais rapidamente.

A hierarquia da saúde é composta por relações entre pais e filhos. Um cluster é composto por nós e aplicações. As aplicações têm serviços e aplicações implementadas. As aplicações implementadas implementaram pacotes de serviço. Os serviços têm divisórias, e cada divisória tem uma ou mais réplicas. Existe uma relação especial entre nós e entidades implantadas. Um nó pouco saudável, tal como relatado pelo seu componente do sistema de autoridade, o serviço Failover Manager, afeta as aplicações implementadas, pacotes de serviço e réplicas implementadas no mesmo.

A hierarquia da saúde representa o estado mais recente do sistema com base nos últimos relatórios de saúde, que são informações quase em tempo real.
Os cães de guarda internos e externos podem reportar sobre as mesmas entidades com base na lógica específica da aplicação ou em condições monitorizadas personalizadas. Os relatórios dos utilizadores coexistem com os relatórios do sistema.

Planeia investir em como reportar e responder à saúde durante o desenho de um grande serviço de nuvem. Este investimento adiantado facilita o depurar, monitorizar e operar.

## <a name="health-states"></a>Estados de saúde
A Service Fabric utiliza três estados de saúde para descrever se uma entidade é saudável ou não: OK, aviso e erro. Qualquer relatório enviado à loja de saúde deve especificar um destes estados. O resultado da avaliação da saúde é um desses estados.

Os possíveis [estados de saúde](/dotnet/api/system.fabric.health.healthstate) são:

* **Ok, tudo bem.** A entidade é saudável. Não existem questões conhecidas sobre o mesmo ou sobre os seus filhos (quando aplicável).
* **Atenção.** A entidade tem alguns problemas, mas ainda pode funcionar corretamente. Por exemplo, há atrasos, mas ainda não causam problemas funcionais. Em alguns casos, a condição de aviso pode fixar-se sem intervenção externa. Nestes casos, os relatórios de saúde sensibilizam e dão visibilidade ao que se passa. Noutros casos, a condição de aviso pode degradar-se num problema grave sem a intervenção do utilizador.
* **Erro**. A entidade não é saudável. Devem ser tomadas medidas para corrigir o estado da entidade, porque não pode funcionar corretamente.
* **Desconhecido**. A entidade não existe na loja de saúde. Este resultado pode ser obtido a partir das consultas distribuídas que se fundem resulta de vários componentes. Por exemplo, a consulta da lista de nó de obter vai para **FailoverManager,** **ClusterManager** e **HealthManager;** obter consulta de lista de candidaturas vai para **ClusterManager** e **HealthManager**. Estas consultas fundem resultados de vários componentes do sistema. Se outro componente do sistema devolver uma entidade que não está presente na loja de saúde, o resultado resultante da fusão tem um estado de saúde desconhecido. Uma entidade não está na loja porque os relatórios de saúde ainda não foram processados ou a entidade foi limpa após a eliminação.

## <a name="health-policies"></a>Políticas de saúde
A loja de saúde aplica políticas de saúde para determinar se uma entidade é saudável com base nos seus relatórios e nos seus filhos.

> [!NOTE]
> As políticas de saúde podem ser especificadas no manifesto do cluster (para avaliação de saúde em agrupamento e nó) ou no manifesto de candidatura (para avaliação de candidaturas e qualquer uma das suas crianças). Os pedidos de avaliação da saúde também podem passar em políticas de avaliação de saúde personalizadas, que são usadas apenas para essa avaliação.
> 
> 

Por defeito, o Service Fabric aplica regras estritas (tudo deve ser saudável) para a relação hierárquica pai-filho. Se mesmo uma das crianças tem um evento pouco saudável, o progenitor é considerado insalubre.

### <a name="cluster-health-policy"></a>Política de saúde do cluster
A [política de saúde do cluster](/dotnet/api/system.fabric.health.clusterhealthpolicy) é usada para avaliar o estado de saúde do cluster e os estados de saúde do nó. A política pode ser definida no manifesto do cluster. Se não estiver presente, utiliza-se a política por defeito (zero falhas toleradas).

A política de saúde do cluster contém:

* [Considere AWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se deve tratar os relatórios de saúde de advertência como erros durante a avaliação de saúde. Predefinição: false.
* [MaxPercentUnhealthyApplicações](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Especifica a percentagem máxima tolerada de aplicações que podem não ser saudáveis antes do cluster ser considerado errado.
* [MaxPercentUnhealthyNodes](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Especifica a percentagem máxima tolerada de nós que podem não ser saudáveis antes do cluster ser considerado errado. Em grandes aglomerados, alguns nós estão sempre em baixo ou fora para reparações, por isso esta percentagem deve ser configurada para tolerar isso.
* [AplicaçãoTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). O mapa da política de saúde do tipo de aplicação pode ser usado durante a avaliação da saúde do cluster para descrever tipos especiais de aplicação. Por padrão, todas as aplicações são colocadas numa piscina e avaliadas com MaxPercentUnhealthyApplications. Se alguns tipos de aplicação devem ser tratados de forma diferente, podem ser retirados da piscina global. Em vez disso, são avaliados em comparação com as percentagens associadas ao seu nome de tipo de aplicação no mapa. Por exemplo, num cluster existem milhares de aplicações de diferentes tipos, e algumas instâncias de aplicação de controlo de um tipo especial de aplicação. As aplicações de controlo nunca devem estar erradas. Pode especificar maxpercentUnhealthyApplicações globais a 20% para tolerar algumas falhas, mas para o tipo de aplicação "ControlApplicationType" definiu as Aplicações MaxPercentUnhealthy para 0. Desta forma, se algumas das muitas aplicações não forem saudáveis, mas abaixo da percentagem global de insalubres, o cluster seria avaliado para Aviso. Um estado de saúde de aviso não afeta a atualização do cluster ou outra monitorização desencadeada pelo estado de saúde error. Mas mesmo uma aplicação de controlo em erro tornaria o cluster insalubre, o que desencadeia o recuo ou pausa na atualização do cluster, dependendo da configuração de upgrade.
  Para os tipos de aplicação definidos no mapa, todas as instâncias de aplicação são retiradas do conjunto global de aplicações. São avaliados com base no número total de aplicações do tipo de aplicação, utilizando as aplicações maxpercentunhealthy específicas do mapa. Todas as restantes aplicações permanecem no pool global e são avaliadas com MaxPercentUnhealthyApplications.

  O exemplo a seguir é um excerto de um manifesto de agrupamento. Para definir as entradas no mapa do tipo de aplicação, prefixe o nome do parâmetro com "ApplicationTypeMaxPercentUnhealthyApplications-", seguido do nome do tipo de aplicação.

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

* [NodeTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.nodetypehealthpolicymap). O mapa da política de saúde do tipo nó pode ser usado durante a avaliação da saúde do cluster para descrever tipos especiais de nó. Os tipos de nó são avaliados em comparação com as percentagens associadas ao nome do nó no mapa. A definição deste valor não tem qualquer efeito no conjunto global de nós utilizados para `MaxPercentUnhealthyNodes` . Por exemplo, um cluster tem centenas de nós de diferentes tipos e alguns tipos de nós que acolhem trabalhos importantes. Nenhum nós deste tipo deve estar para baixo. Você pode especificar global `MaxPercentUnhealthyNodes` a 20% para tolerar algumas falhas para todos os nós, mas para o tipo de `SpecialNodeType` nó, definir o `MaxPercentUnhealthyNodes` para 0. Desta forma, se alguns dos muitos nós não forem saudáveis, mas abaixo da percentagem global de insalubres, o cluster seria avaliado como estando no estado de alerta de saúde. Um estado de saúde de aviso não afeta a atualização do cluster ou outra monitorização desencadeada por um estado de saúde de Erro. Mas mesmo um nó de tipo num estado de `SpecialNodeType` saúde de Erro tornaria o cluster insalubre e desencadearia o revés ou interromperia a atualização do cluster, dependendo da configuração de upgrade. Inversamente, fixar o global `MaxPercentUnhealthyNodes` para 0 e fixar os `SpecialNodeType` nós máximos por cento não saudáveis para 100 com um nó de tipo num estado de erro ainda colocaria o cluster num estado de `SpecialNodeType` erro porque a restrição global é mais rigorosa neste caso. 

  O exemplo a seguir é um excerto de um manifesto de agrupamento. Para definir entradas no mapa do tipo do nó, prefixe o nome do parâmetro com "NodeTypeMaxPercentUnhealthyNodes-", seguido do nome do tipo nó.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="NodeTypeMaxPercentUnhealthyNodes-SpecialNodeType" Value="0" />
    </Section>
  </FabricSettings>
  ```

### <a name="application-health-policy"></a>Política de saúde de aplicação
A [política de saúde](/dotnet/api/system.fabric.health.applicationhealthpolicy) da aplicação descreve como a avaliação dos eventos e da agregação dos estados-crianças é feita para aplicações e seus filhos. Pode ser definido no manifesto de candidatura, **ApplicationManifest.xml,** no pacote de candidaturas. Se não forem especificadas políticas, a Service Fabric assume que a entidade não é saudável se tiver um relatório de saúde ou uma criança no estado de saúde de aviso ou erro.
As políticas configuráveis são:

* [Considere AWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se deve tratar os relatórios de saúde de advertência como erros durante a avaliação de saúde. Predefinição: false.
* [MaxPercentUnhealthyDeployedApplicações](/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Especifica a percentagem máxima tolerada de aplicações implementadas que podem não ser saudáveis antes de a aplicação ser considerada por engano. Esta percentagem é calculada dividindo o número de aplicações não saudáveis implementadas sobre o número de nós em que as aplicações são atualmente implantadas no cluster. A computação arredonda para tolerar uma falha em um pequeno número de nós. Percentagem de predefinição: zero.
* [DefaultServiceTypeHealthPolicy](/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Especifica a política de saúde do tipo de serviço predefinido, que substitui a política de saúde predefinido para todos os tipos de serviços na aplicação.
* [ServiceTypeHealthPolicyMap](/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Fornece um mapa das políticas de saúde de serviço por tipo de serviço. Estas políticas substituem as políticas de saúde do tipo de serviço predefinido para cada tipo de serviço especificado. Por exemplo, se uma aplicação tiver um tipo de serviço de gateway apátrida e um tipo de serviço de motor imponente, pode configurar as políticas de saúde para a sua avaliação de forma diferente. Quando especificar a política por tipo de serviço, pode ganhar mais controlo granular da saúde do serviço.

### <a name="service-type-health-policy"></a>Política de saúde tipo de serviço
A [política de saúde do tipo de serviço](/dotnet/api/system.fabric.health.servicetypehealthpolicy) especifica como avaliar e agregar os serviços e as crianças dos serviços. A política contém:

* [MaxPercentUnhealthyPartitionsPerService](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Especifica a percentagem máxima tolerada de divisórias não saudáveis antes de um serviço ser considerado insalubre. Percentagem de predefinição: zero.
* [MaxPercentUnhealthyReplicasPerPartition](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Especifica a percentagem máxima tolerada de réplicas não saudáveis antes de uma partição ser considerada insalubre. Percentagem de predefinição: zero.
* [MaxPercentUnhealthyServices](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Especifica a percentagem máxima tolerada de serviços não saudáveis antes de o pedido ser considerado insalubre. Percentagem de predefinição: zero.

Segue-se um excerto de um manifesto de aplicação:

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

## <a name="health-evaluation"></a>Avaliação de saúde
Os utilizadores e serviços automatizados podem avaliar a saúde de qualquer entidade a qualquer momento. Para avaliar a saúde de uma entidade, a loja de saúde agrega todos os relatórios de saúde sobre a entidade e avalia todos os seus filhos (quando aplicável). O algoritmo de agregação de saúde utiliza políticas de saúde que especificam como avaliar relatórios de saúde e como agregar estados de saúde infantil (quando aplicável).

### <a name="health-report-aggregation"></a>Agregação do relatório de saúde
Uma entidade pode ter vários relatórios de saúde enviados por diferentes repórteres (componentes do sistema ou cães de guarda) em diferentes propriedades. A agregação utiliza as políticas de saúde associadas, em particular o membro do ConsiderWarningAsError da política de aplicação ou saúde do cluster. ConsidereWarningAsError especifica como avaliar avisos.

O estado de saúde agregado é desencadeado pelos *piores* relatórios de saúde sobre a entidade. Se houver pelo menos um relatório de saúde de erro, o estado de saúde agregado é um erro.

![Relatório de saúde agregação com relatório de erro.][2]

Uma entidade de saúde que tenha um ou mais relatórios de saúde de erro é avaliada como Erro. O mesmo se aplica a um relatório de saúde caducado, independentemente do seu estado de saúde.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se não houver relatórios de erro e uma ou mais advertências, o estado de saúde agregado é um aviso ou erro, dependendo da bandeira política do ConsiderWarningAsError.

![Agregação de relatórios de saúde com relatório de aviso e ConsidereWarningAsError falso.][3]

Agregação de relatórios de saúde com relatório de aviso e ConsidereWarningAsError definido para falso (padrão).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregação da saúde infantil
O estado de saúde agregado de uma entidade reflete os estados de saúde infantil (quando aplicável). O algoritmo para agregar estados de saúde infantil utiliza as políticas de saúde aplicáveis com base no tipo de entidade.

![Agregação de saúde de entidades infantis.][4]

Agregação infantil baseada em políticas de saúde.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois de a loja de saúde ter avaliado todas as crianças, agrega os seus estados de saúde com base na percentagem máxima configurada de crianças não saudáveis. Esta percentagem é retirada da política com base na entidade e no tipo infantil.

* Se todas as crianças tiverem estados ok, o estado de saúde agregado para crianças está ok.
* Se as crianças tiverem estados de ok e aviso, o estado de saúde agregado para crianças é aviso.
* Se há crianças com estados de erro que não respeitam a percentagem máxima permitida de crianças não saudáveis, o estado de saúde dos pais agregado é um erro.
* Se as crianças com estados de erro respeitarem a percentagem máxima permitida de crianças não saudáveis, o estado de saúde dos pais agregados está avisado.

## <a name="health-reporting"></a>Relatórios de saúde
Componentes do sistema, aplicações de Tecidos de Sistema e cães de guarda internos/externos podem reportar contra entidades do Service Fabric. Os repórteres fazem determinações *locais* sobre a saúde das entidades monitorizadas, com base nas condições que estão a monitorizar. Não precisam de olhar para nenhum estado global ou dados agregados. O comportamento desejado é ter repórteres simples, e não organismos complexos que precisam olhar para muitas coisas para inferir que informação enviar.

Para enviar dados de saúde para a loja de saúde, um repórter precisa identificar a entidade afetada e criar um relatório de saúde. Para enviar o relatório, utilize o [FabricClient.HealthClient.ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, reporte APIs de saúde expostos nos `Partition` `CodePackageActivationContext` cmdlets ou objetos, cmdlets PowerShell ou REST.

### <a name="health-reports"></a>Relatórios de saúde
Os [relatórios de saúde](/dotnet/api/system.fabric.health.healthreport) de cada uma das entidades do agrupamento contêm as seguintes informações:

* **SourceId**. Uma corda que identifica exclusivamente o repórter do evento de saúde.
* **Identificador de entidades.** Identifica a entidade onde o relatório é aplicado. Difere com base no [tipo de entidade:](service-fabric-health-introduction.md#health-entities-and-hierarchy)
  
  * O agrupamento. Nenhum.
  * O nó. Nome do nó (string).
  * A candidatura. Nome da aplicação (URI). Representa o nome da instância de aplicação implantada no cluster.
  * O serviço. Nome de serviço (URI). Representa o nome da instância de serviço implantada no cluster.
  * A partilha. ID de partição (GUID). Representa o identificador único da partição.
  * Réplica. O ID de réplica de serviço estatal ou o ID de instância de serviço apátrida (INT64).
  * Implementação De aplicação. Nome da aplicação (URI) e nome do nó (cadeia).
  * Serviço de Serviço implantado. Nome da aplicação (URI), nome do nó (string) e nome manifesto de serviço (cadeia).
* **Propriedade**. Uma *corda* (não uma enumeração fixa) que permite ao repórter categorizar o evento de saúde para uma propriedade específica da entidade. Por exemplo, o repórter A pode reportar a saúde da propriedade Node01 "Armazenamento" e o repórter B pode reportar a saúde da propriedade Node01 "Conectividade". Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para a entidade Node01.
* **Descrição**. Uma corda que permite a um repórter fornecer informações detalhadas sobre o evento de saúde. **SourceId**, **Property** e **HealthState** devem descrever totalmente o relatório. A descrição adiciona informações legíveis pelo homem sobre o relatório. O texto facilita a compreensão do relatório de saúde por parte dos administradores e dos utilizadores.
* **Estado da Saúde**. Uma [enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de saúde do relatório. Os valores aceites são OK, Aviso e Erro.
* **TimeToLive**. Um período de tempo que indica quanto tempo o relatório de saúde é válido. Juntamente com **removeWhenExpired,** permite à loja de saúde saber como avaliar eventos expirados. Por padrão, o valor é infinito, e o relatório é válido para sempre.
* **RemoverWhenExpired**. Um booleano. Se for definido como verdadeiro, o relatório de saúde expirado é automaticamente removido da loja de saúde, e o relatório não tem impacto na avaliação de saúde da entidade. Usado quando o relatório é válido apenas por um período de tempo especificado, e o repórter não precisa explicitamente limpá-lo. Também é usado para apagar relatórios da loja de saúde (por exemplo, um cão de guarda é alterado e para de enviar relatórios com fonte e propriedade anteriores). Pode enviar um relatório com um breve TimeToLive juntamente com removeWhenExpired para limpar qualquer estado anterior da loja de saúde. Se o valor for definido como falso, o relatório caducado é tratado como um erro na avaliação de saúde. O valor falso indica à loja de saúde que a fonte deve reportar periodicamente sobre esta propriedade. Se não, deve haver algo de errado com o cão de guarda. A saúde do cão de guarda é capturada considerando o evento como um erro.
* **Sequência Número**. Um número inteiro positivo que precisa de ser cada vez maior, representa a ordem dos relatórios. É usado pela loja de saúde para detetar relatórios antigos que são recebidos tardiamente devido a atrasos na rede ou outros problemas. Um relatório é rejeitado se o número da sequência for inferior ou igual ao número mais recentemente aplicado para a mesma entidade, fonte e propriedade. Se não for especificado, o número de sequência é gerado automaticamente. É necessário colocar o número da sequência apenas quando se reporta sobre as transições estatais. Nesta situação, a fonte precisa de se lembrar quais os relatórios que enviou e manter as informações para recuperação em caso de falha.

Estas quatro peças de informação-- SourceId, identificador de entidades, Propriedade e Estado da Saúde -- são necessárias para cada relatório de saúde. A cadeia SourceId não está autorizada a começar com o prefixo "**System.**", que é reservado para relatórios do sistema. Para a mesma entidade, existe apenas um relatório para a mesma fonte e propriedade. Vários relatórios para a mesma fonte e propriedade sobrepõem-se mutuamente, seja no lado do cliente de saúde (se forem em lotados) ou no lado da loja de saúde. A substituição baseia-se em números de sequência; relatórios mais recentes (com números de sequência mais elevados) substituem relatórios mais antigos.

### <a name="health-events"></a>Eventos de saúde
Internamente, a loja de saúde mantém eventos de [saúde](/dotnet/api/system.fabric.health.healthevent), que contêm toda a informação dos relatórios, e metadados adicionais. Os metadados incluem o tempo que o relatório foi dado ao cliente de saúde e o tempo que foi modificado no lado do servidor. Os eventos de saúde são devolvidos por [consultas de saúde.](service-fabric-view-entities-aggregated-health.md#health-queries)

Os metadados adicionados contêm:

* **FonteUtcTimestamp**. O tempo que o relatório foi dado ao cliente de saúde (Tempo Universal Coordenado).
* **ÚltimaâMpia De ÚltimaM.** A última vez que o relatório foi modificado no lado do servidor (Tempo Universal Coordenado).
* **IsExpired**. Uma bandeira para indicar se o relatório expirou quando a consulta foi executada pela loja de saúde. Um evento só pode ser expirado se o RemoveWhenExpired for falso. Caso contrário, o evento não é devolvido por consulta e é removido da loja.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. A última vez para transições OK/aviso/erro. Estes campos dão a história das transições do estado de saúde para o evento.

Os campos de transição do Estado podem ser usados para alertas mais inteligentes ou informações "históricas" de eventos de saúde. Permitem cenários como:

* Alerte quando uma propriedade estiver em alerta/erro por mais de X minutos. A verificação da condição durante um período de tempo evita alertas sobre condições temporárias. Por exemplo, um alerta se o estado de saúde estiver avisado há mais de cinco minutos pode ser traduzido para (HealthState == Warning and Now - LastWarningTransitionTime > 5 minutos).
* Alerta apenas sobre as condições que mudaram nos últimos X minutos. Se um relatório já estava errado antes do tempo especificado, pode ser ignorado porque já foi sinalizado anteriormente.
* Se uma propriedade estiver a mexer entre aviso e erro, determine quanto tempo esteve pouco saudável (isto é, não OK). Por exemplo, um alerta se a propriedade não estiver saudável há mais de cinco minutos pode ser traduzido para (HealthState != Ok and Now - LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Exemplo: Relatório e avaliação da saúde das aplicações
O exemplo a seguir envia um relatório de saúde através do PowerShell sobre o tecido da **aplicação:/WordCount** da fonte **MyWatchdog**. O relatório de saúde contém informações sobre a "disponibilidade" da propriedade sanitária num estado de saúde de erro, com o infinito TimeToLive. Depois questiona a saúde da aplicação, que devolve erros agregados do Estado de saúde e os relatos de eventos de saúde na lista de eventos de saúde.

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

## <a name="health-model-usage"></a>Uso de modelo de saúde
O modelo de saúde permite que os serviços em nuvem e a plataforma subjacente do Service Fabric sejam dimensionados, uma vez que as determinações de monitorização e saúde são distribuídas entre os diferentes monitores dentro do cluster.
Outros sistemas têm um serviço único e centralizado ao nível do cluster que analisa todas as informações *potencialmente* úteis emitidas pelos serviços. Esta abordagem dificulta a sua escalabilidade. Também não lhes permite recolher informações específicas para ajudar a identificar problemas e potenciais problemas o mais próximo possível da causa raiz.

O modelo de saúde é fortemente utilizado para monitorização e diagnóstico, para avaliar a saúde do cluster e da aplicação, e para atualizações monitorizadas. Outros serviços utilizam dados de saúde para realizar reparações automáticas, construir histórico de saúde do cluster e emitir alertas em determinadas condições.

## <a name="next-steps"></a>Passos seguintes
[Ver relatórios de saúde do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Utilize relatórios de saúde do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como reportar e verificar a saúde do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adicionar relatórios de saúde personalizados do Service Fabric](service-fabric-report-health.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação do Tecido de Serviço](service-fabric-application-upgrade.md)
