---
title: Monitorização da saúde em Tecido de Serviço
description: Uma introdução ao modelo de monitorização da saúde azure Service Fabric, que fornece monitorização do cluster e das suas aplicações e serviços.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 473aa2b9a74193a857390cd3e29b2b559b6084d3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282422"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introdução à monitorização do estado de funcionamento do Service Fabric
A Azure Service Fabric introduz um modelo de saúde que proporciona uma avaliação e reporte de saúde ricas, flexíveis e extensíveis. O modelo permite a monitorização quase em tempo real do estado do cluster e dos serviços que nele estão em funcionamento. Você pode facilmente obter informações de saúde e corrigir problemas potenciais antes que eles em cascata e causar interrupções massivas. No modelo típico, os serviços enviam relatórios com base nas suas opiniões locais, e essa informação é agregada para fornecer uma visão global do nível do cluster.

Os componentes do Tecido de Serviço usam este modelo de saúde rico para reportar o seu estado atual. Pode usar o mesmo mecanismo para reportar a saúde das suas aplicações. Se investir em relatórios de saúde de alta qualidade que captam as suas condições personalizadas, pode detetar e corrigir problemas para a sua aplicação de execução muito mais facilmente.

> [!NOTE]
> Iniciámos o subsistema de saúde para resolver a necessidade de atualizações monitorizadas. O Service Fabric fornece atualizações de aplicações monitorizadas e cluster que garantem total disponibilidade, sem tempo de inatividade e mínima sem intervenção do utilizador. Para atingir estes objetivos, a atualização verifica a saúde com base em políticas de upgrade configuradas. Uma atualização só pode prosseguir quando a saúde respeitar os limiares desejados. Caso contrário, a atualização é automaticamente relançada ou interrompida para dar aos administradores a oportunidade de corrigir os problemas. Para saber mais sobre atualizações de aplicações, consulte [este artigo.](service-fabric-application-upgrade.md)
> 
> 

## <a name="health-store"></a>Loja de saúde
A loja de saúde mantém informações relacionadas com a saúde sobre entidades do cluster para fácil recuperação e avaliação. É implementado como um Tecido de Serviço persistente serviço estatal para garantir alta disponibilidade e escalabilidade. A loja de saúde faz parte da aplicação **de tecido:/Sistema,** e está disponível quando o cluster está em funcionamento.

## <a name="health-entities-and-hierarchy"></a>Entidades de saúde e hierarquia
As entidades de saúde estão organizadas numa hierarquia lógica que capta interações e dependências entre diferentes entidades. A loja de saúde constrói automaticamente entidades de saúde e hierarquia com base em relatórios recebidos de componentes do Service Fabric.

As entidades de saúde espelham as entidades do Tecido de Serviço. (Por exemplo, a entidade de aplicação de **saúde** corresponde a uma instância de aplicação implantada no cluster, enquanto a entidade do nó de **saúde** corresponde a um nó de cluster de tecido de serviço.) A hierarquia da saúde capta as interações das entidades do sistema, e é a base para uma avaliação avançada da saúde. Você pode aprender sobre os conceitos chave Service Fabric na visão geral técnica do [Tecido de Serviço](service-fabric-technical-overview.md). Para mais informações sobre a aplicação, consulte o modelo de [aplicação Service Fabric](service-fabric-application-model.md).

As entidades de saúde e a hierarquia permitem que o cluster e as aplicações sejam efetivamente reportadas, depuradas e monitorizadas. O modelo de saúde proporciona uma representação precisa e *granular* da saúde das muitas peças móveis do cluster.

entidades de saúde ![.][1]
As entidades de saúde, organizadas numa hierarquia baseada nas relações pai-filho.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de saúde são:

* **Aglomerado.** Representa a saúde de um cluster de Tecido de Serviço. Relatórios de saúde do cluster descrevem condições que afetam todo o cluster. Estas condições afetam várias entidades do cluster ou do próprio cluster. Com base na condição, o repórter não pode reduzir o assunto a uma ou mais crianças insalubres. Exemplos incluem o cérebro do cluster dividindo-se devido a problemas de divisão de rede ou comunicação.
* **Nó.** Representa a saúde de um nó de tecido de serviço. Relatórios de saúde do nó descrevem condições que afetam a funcionalidade do nó. Normalmente afetam todas as entidades implantadas que nela executam. Exemplos incluem nó fora do espaço do disco (ou outras propriedades em toda a máquina, tais como memória, conexões) e quando um nó está em baixo. A entidade nóé identificada pelo nome do nó (corda).
* **Aplicação**. Representa a saúde de uma instância de candidatura que funciona no cluster. Os relatórios de saúde da aplicação descrevem condições que afetam a saúde geral da aplicação. Não podem ser reduzidas a crianças individuais (serviços ou aplicações implementadas). Exemplos incluem a interação de ponta a ponta entre diferentes serviços na aplicação. A entidade de aplicação é identificada pelo nome da aplicação (URI).
* **Serviço.** Representa a saúde de um serviço em execução no aglomerado. Os relatórios de saúde do serviço descrevem condições que afetam a saúde geral do serviço. O repórter não pode reduzir o assunto a uma divisão ou réplica pouco saudável. Exemplos incluem uma configuração de serviço (como a porta ou a partilha de ficheiros externos) que está a causar problemas a todas as divisórias. A entidade de serviço é identificada pelo nome de serviço (URI).
* **Partição**. Representa a saúde de uma partição de serviço. Os relatórios de saúde da partição descrevem condições que afetam todo o conjunto de réplicas. Exemplos incluem quando o número de réplicas está abaixo da contagem do alvo e quando uma partição está em perda de quórum. A entidade de partição é identificada pelo ID de partição (GUID).
* **Réplica.** Representa a saúde de uma réplica de serviço apátrida ou de uma instância de serviço apátrida. A réplica é a unidade mais pequena que os cães de guarda e os componentes do sistema podem reportar para uma aplicação. Para serviços estatais, exemplos incluem uma réplica primária que não pode replicar operações a secundários e replicação lenta. Além disso, um caso apátrida pode relatar quando está a ficar sem recursos ou tem problemas de conectividade. A entidade réplica é identificada pelo ID da partição (GUID) e pela réplica ou id da instância (longo).
* **Aplicação implantada**. Representa a saúde de uma *aplicação em funcionamento num nó.* Relatórios de saúde de aplicação implantados descrevem condições específicas para a aplicação no nó que não pode ser reduzida a pacotes de serviço implantados no mesmo nó. Exemplos incluem erros quando o pacote de aplicações não pode ser descarregado nesse nó e questões que configuram os diretores de segurança da aplicação no nó. A aplicação implementada é identificada pelo nome da aplicação (URI) e pelo nome do nó (cadeia).
* **DeployservicePackage**. Representa a saúde de um pacote de serviço que funciona num nó no cluster. Descreve condições específicas de um pacote de serviços que não afetam os outros pacotes de serviços no mesmo nó para a mesma aplicação. Exemplos incluem um pacote de código no pacote de serviço que não pode ser iniciado e um pacote de configuração que não pode ser lido. O pacote de serviço implantado é identificado pelo nome da aplicação (URI), nome do nó (string), nome do manifesto de serviço (string) e ID de ativação do pacote de serviço (string).

A granularidade do modelo de saúde facilita a deteção e correção de problemas. Por exemplo, se um serviço não está a responder, é possível informar que a instância de candidatura não é saudável. No entanto, a comunicação a este nível não é o ideal, porque a questão pode não estar a afetar todos os serviços dentro dessa aplicação. O relatório deve ser aplicado ao serviço não saudável ou a uma partição específica de crianças, se mais informações apontarem para essa partilha. Os dados surgem automaticamente através da hierarquia, e uma partição pouco saudável é visível aos níveis de serviço e aplicação. Esta agregação ajuda a identificar e resolver a causa principal do problema mais rapidamente.

A hierarquia da saúde é composta por relações pai-filho. Um aglomerado é composto por nós e aplicações. As aplicações têm serviços e aplicações implementadas. Aplicações implementadas implementaram pacotes de serviços. Os serviços têm divisórias, e cada partição tem uma ou mais réplicas. Existe uma relação especial entre nós e entidades implantadas. Um nó doentio, tal como relatado pela sua componente do sistema de autoridade, o serviço Failover Manager, afeta as aplicações implementadas, pacotes de serviços e réplicas implantadas no mesmo.

A hierarquia da saúde representa o mais recente estado do sistema com base nos últimos relatórios de saúde, que são informações quase em tempo real.
Os cães de guarda internos e externos podem reportar sobre as mesmas entidades com base em lógica específica da aplicação ou condições monitorizadas personalizadas. Os relatórios dos utilizadores coexistem com os relatórios do sistema.

Planeie investir na forma de reportar e responder à saúde durante o desenho de um grande serviço na nuvem. Este investimento frontal facilita o depuração, monitorização e operação do serviço.

## <a name="health-states"></a>Estados de saúde
Service Fabric usa três estados de saúde para descrever se uma entidade é saudável ou não: OK, aviso e erro. Qualquer relatório enviado à loja de saúde deve especificar um destes estados. O resultado da avaliação da saúde é um destes estados.

Os possíveis [estados de saúde](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) são:

* **Ok, não é?** A entidade é saudável. Não existem questões conhecidas sobre ele ou sobre os seus filhos (quando aplicável).
* **Aviso.** A entidade tem alguns problemas, mas ainda pode funcionar corretamente. Por exemplo, há atrasos, mas ainda não causam problemas funcionais. Em alguns casos, a condição de alerta pode fixar-se sem intervenção externa. Nestes casos, os relatórios de saúde sensibilizam e proporcionam visibilidade ao que se passa. Noutros casos, a condição de alerta pode degradar-se num grave problema sem a intervenção do utilizador.
* **Erro**. A entidade não é saudável. Devem ser tomadas medidas para corrigir o estado da entidade, porque não pode funcionar corretamente.
* **Desconhecido.** A entidade não existe na loja de saúde. Este resultado pode ser obtido a partir das consultas distribuídas que se fundem resulta de vários componentes. Por exemplo, a consulta da lista de obter vai para **FailoverManager,** **ClusterManager**, e **HealthManager;** obter consulta de lista de aplicações vai para **ClusterManager** e **HealthManager**. Estas consultas fundem-se resulta de múltiplos componentes do sistema. Se outra componente do sistema devolver uma entidade que não está presente na loja de saúde, o resultado fundido tem um estado de saúde desconhecido. Uma entidade não está armazenada porque os relatórios de saúde ainda não foram processados ou a entidade foi limpa após a eliminação.

## <a name="health-policies"></a>Políticas de saúde
A loja de saúde aplica políticas de saúde para determinar se uma entidade é saudável com base nos seus relatórios e nos seus filhos.

> [!NOTE]
> As políticas de saúde podem ser especificadas no manifesto de agrupamento (para avaliação de cluster e saúde nó) ou no manifesto de candidatura (para avaliação de candidaturas e qualquer um dos seus filhos). Os pedidos de avaliação da saúde também podem passar em políticas personalizadas de avaliação da saúde, que são usadas apenas para essa avaliação.
> 
> 

Por padrão, o Tecido de Serviço aplica regras rígidas (tudo deve ser saudável) para a relação hierárquica pai-filho. Se mesmo uma das crianças tem um evento pouco saudável, o progenitor é considerado insalubre.

### <a name="cluster-health-policy"></a>Política de saúde de cluster
A política de [saúde](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) do cluster é usada para avaliar o estado de saúde do cluster e os estados de saúde do nó. A política pode ser definida no manifesto de cluster. Se não estiver presente, a política de incumprimento (zero falhas toleradas) é utilizada.
A política de saúde do cluster contém:

* [Considere o Erro de Advertências](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se deve tratar os relatórios de saúde como erros durante a avaliação da saúde. Padrão: falso.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Especifica a percentagem máxima tolerada de aplicações que podem ser insalubres antes de o cluster ser considerado por engano.
* [MaxPercentUnhealthyNodes.](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes) Especifica a percentagem máxima tolerada de nós que pode ser insalubre antes que o cluster seja considerado errado. Em grandes aglomerados, alguns nós estão sempre para baixo ou para reparações, por isso esta percentagem deve ser configurada para tolerar isso.
* [AplicatypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). O mapa de política de saúde do tipo de aplicação pode ser usado durante a avaliação de saúde do cluster para descrever tipos de aplicação especiais. Por padrão, todas as aplicações são colocadas numa piscina e avaliadas com Aplicações MaxPercentUnhealthy. Se alguns tipos de aplicações devem ser tratados de forma diferente, podem ser retirados do pool global. Em vez disso, são avaliados contra as percentagens associadas ao seu nome de tipo de aplicação no mapa. Por exemplo, num cluster existem milhares de aplicações de diferentes tipos, e algumas instâncias de aplicação de controlo de um tipo especial de aplicação. As aplicações de controlo nunca devem estar erradas. Pode especificar as Aplicações MaxPercentUnhealthy global a 20% para tolerar algumas falhas, mas para o tipo de aplicação "ControlApplicationType" definiu as Aplicações MaxPercentUnhealthy a 0. Desta forma, se algumas das muitas aplicações não forem saudáveis, mas abaixo da percentagem global de insalubres, o cluster seria avaliado ao Aviso. Um estado de saúde de alerta não afeta a atualização do cluster ou outra monitorização desencadeada pelo estado de saúde error. Mas mesmo uma aplicação de controlo por erro tornaria o cluster insalubre, o que dispara para trás ou pausa a atualização do cluster, dependendo da configuração de upgrade.
  Para os tipos de aplicação definidos no mapa, todas as instâncias de aplicação são retiradas do conjunto global de aplicações. São avaliados com base no número total de aplicações do tipo de aplicação, utilizando as aplicações maxpercentunhealthy específicas do mapa. Todas as restantes aplicações permanecem no pool global e são avaliadas com Aplicações MaxPercentUnhealthy.

O exemplo que se segue é um excerto de um manifesto de cluster. Para definir as entradas no mapa do tipo de aplicação, prefixe o nome do parâmetro com "ApplicationTypeMaxPercentUnhealthyApplications-", seguido do nome do tipo de aplicação.

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

### <a name="application-health-policy"></a>Política de saúde de aplicação
A política de saúde da [aplicação](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) descreve como a avaliação dos eventos e da agregação dos estados infantis é feita para aplicações e seus filhos. Pode ser definido no manifesto de aplicação, **ApplicationManifest.xml,** no pacote de aplicação. Se não forem especificadas políticas, a Service Fabric assume que a entidade não é saudável se tiver um relatório de saúde ou uma criança no estado de saúde de advertência ou erro.
As políticas configuráveis são:

* [Considere o Erro de Advertências](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se deve tratar os relatórios de saúde como erros durante a avaliação da saúde. Padrão: falso.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Especifica a percentagem máxima tolerada de aplicações implementadas que podem ser insalubres antes de a aplicação ser considerada errada. Esta percentagem é calculada dividindo o número de aplicações implantadas pouco saudáveis em relação ao número de nós em que as aplicações são atualmente implantadas no cluster. A computação reúne-se para tolerar uma falha em pequenos números de nódosos. Percentagem de incumprimento: zero.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Especifica a política de saúde do tipo de serviço padrão, que substitui a política de saúde padrão para todos os tipos de serviço na aplicação.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Fornece um mapa das políticas de saúde de serviço por tipo de serviço. Estas políticas substituem as políticas de saúde do tipo de serviço padrão para cada tipo de serviço especificado. Por exemplo, se uma aplicação tiver um tipo de serviço de gateway apátrida e um tipo de serviço de motor imponente, pode configurar as políticas de saúde para a sua avaliação de forma diferente. Quando especifica a política por tipo de serviço, pode obter um controlo mais granular da saúde do serviço.

### <a name="service-type-health-policy"></a>Política de saúde tipo serviço
A política de [saúde do tipo de serviço](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) especifica como avaliar e agregar os serviços e as crianças dos serviços. A política contém:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Especifica a percentagem máxima tolerada de divisórias não saudáveis antes de um serviço ser considerado insalubre. Percentagem de incumprimento: zero.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Especifica a percentagem máxima tolerada de réplicas não saudáveis antes de uma partição ser considerada insalubre. Percentagem de incumprimento: zero.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Especifica a percentagem máxima tolerada de serviços não saudáveis antes de o pedido ser considerado insalubre. Percentagem de incumprimento: zero.

O exemplo seguinte é um excerto de um manifesto de candidatura:

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

## <a name="health-evaluation"></a>Avaliação da saúde
Os utilizadores e serviços automatizados podem avaliar a saúde de qualquer entidade a qualquer momento. Para avaliar a saúde de uma entidade, a loja de saúde agrega todos os relatórios de saúde da entidade e avalia todos os seus filhos (quando aplicável). O algoritmo de agregação da saúde usa políticas de saúde que especificam como avaliar relatórios de saúde e como agregar estados de saúde infantil (quando aplicável).

### <a name="health-report-aggregation"></a>Agregação do relatório de saúde
Uma entidade pode ter vários relatórios de saúde enviados por diferentes repórteres (componentes do sistema ou cães de guarda) em diferentes propriedades. A agregação utiliza as políticas de saúde associadas, em particular o membro da Aplicação ou da Política de Saúde do Agrupamento de Empresas. Considere O Erro de Aviso especifica como avaliar os avisos.

O estado de saúde agregado é desencadeado pelos *piores* relatórios de saúde da entidade. Se houver pelo menos um relatório de saúde de erro, o estado de saúde agregado é um erro.

![Agregação do relatório de saúde com relatório de erro.][2]

Uma entidade de saúde que tem um ou mais relatórios de saúde de erro é avaliado como Erro. O mesmo se aplica a um relatório de saúde caducado, independentemente do seu estado de saúde.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se não houver relatórios de erro e um ou mais avisos, o estado de saúde agregado é aviso ou erro, dependendo da bandeira da política ConsiderWarningAsError.

![Agregação do relatório de saúde com relatório de alerta e ConsidereOErro falso.][3]

Agregação do relatório de saúde com relatório de aviso e ConsidereWarningAsError definido para falso (padrão).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregação da saúde infantil
O estado de saúde agregado de uma entidade reflete os estados de saúde infantil (quando aplicável). O algoritmo para agregação de estados de saúde infantil utiliza as políticas de saúde aplicáveis com base no tipo de entidade.

![Agregação de saúde de entidades infantis.][4]

Agregação infantil baseada em políticas de saúde.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois de a loja de saúde ter avaliado todas as crianças, agrega os seus estados de saúde com base na percentagem máxima configurada de crianças não saudáveis. Esta percentagem é retirada da política com base na entidade e no tipo infantil.

* Se todas as crianças tiverem estados OK, o estado de saúde agregado da criança está bem.
* Se as crianças tiverem estado sok e alerta, o estado de saúde agregado da criança está alerta.
* Se há crianças com erros que não respeitam a percentagem máxima permitida de crianças não saudáveis, o estado de saúde dos pais agregados é um erro.
* Se as crianças com erros respeitarem a percentagem máxima permitida de crianças não saudáveis, o estado de saúde dos pais agregados é avisado.

## <a name="health-reporting"></a>Relatórios de saúde
Componentes do sistema, aplicações de Tecido system e cães de guarda internos/externos podem reportar contra entidades de Tecido de Serviço. Os jornalistas fazem determinações *locais* da saúde das entidades monitorizadas, com base nas condições que estão a monitorizar. Não precisam de olhar para nenhum estado global ou dados agregados. O comportamento desejado é ter repórteres simples, e não organismos complexos que precisam olhar para muitas coisas para inferir que informação enviar.

Para enviar dados de saúde para a loja de saúde, um repórter precisa identificar a entidade afetada e criar um relatório de saúde. Para enviar o relatório, utilize a [API FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, reporte APIs de saúde expostos na `Partition` ou `CodePackageActivationContext` objetos, cmdlets PowerShell ou REST.

### <a name="health-reports"></a>Relatórios de saúde
Os relatórios de [saúde](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) de cada uma das entidades do cluster contêm as seguintes informações:

* **Fonteid.** Uma corda que identifica exclusivamente o repórter do evento de saúde.
* **Identificador de entidades.** Identifica a entidade onde o relatório é aplicado. Difere com base no [tipo de entidade:](service-fabric-health-introduction.md#health-entities-and-hierarchy)
  
  * Aglomerado. Nenhum.
  * Node, node. Nome do nó (corda).
  * A inscrição. Nome da candidatura (URI). Representa o nome da instância de aplicação implantada no cluster.
  * O serviço. Nome de serviço (URI). Representa o nome da instância de serviço implantada no cluster.
  * Partição. ID de partição (GUID). Representa o identificador único da partição.
  * Uma réplica. A réplica de serviço audato ID ou a instância de serviço apátrida ID (INT64).
  * Aplicação implantada. Nome de aplicação (URI) e nome do nó (corda).
  * DeployedServicePackage. Nome da aplicação (URI), nome do nó (corda) e nome manifesto de serviço (corda).
* **Propriedade.** Uma *corda* (não uma enumeração fixa) que permite ao repórter categorizar o evento de saúde para uma propriedade específica da entidade. Por exemplo, o repórter A pode reportar a saúde da propriedade node01 "Armazenamento" e o repórter B pode reportar a saúde da propriedade node01 "Conectividade". Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para a entidade Nóde01.
* **Descrição**. Uma corda que permite a um repórter fornecer informações detalhadas sobre o evento de saúde. **SourceId**, **Propriedade**e **Estado de Saúde** devem descrever totalmente o relatório. A descrição acrescenta informações legíveis pelo homem sobre o relatório. O texto facilita a compreensão do relatório de saúde por parte dos administradores e utilizadores.
* **Estado de Saúde.** Uma [enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de saúde do relatório. Os valores aceites são OK, Aviso e Erro.
* **TimetoLive.** Uma hora que indica quanto tempo o relatório de saúde é válido. Juntamente com **removeWhenExpired,** permite à loja de saúde saber avaliar os eventos expirados. Por padrão, o valor é infinito, e o relatório é válido para sempre.
* **Remover Quando expirado**. Um booleano. Se for verdade, o relatório de saúde expirado é automaticamente removido da loja de saúde, e o relatório não impacta a avaliação de saúde da entidade. Usado quando o relatório é válido apenas por um período de tempo especificado, e o repórter não precisa explicitamente limpá-lo. Também é usado para apagar relatórios da loja de saúde (por exemplo, um cão de guarda é alterado e deixa de enviar relatórios com fonte e propriedade anteriores). Pode enviar um relatório com um breve TimeToLive juntamente com removeWhenExpired para limpar qualquer estado anterior da loja de saúde. Se o valor for definido como falso, o relatório expirado é tratado como um erro na avaliação de saúde. O valor falso indica para a loja de saúde que a fonte deve reportar periodicamente sobre esta propriedade. Se não, deve haver algo de errado com o cão de guarda. A saúde do cão de guarda é capturada considerando o evento como um erro.
* **SequênciaNúmero**. Um inteiro positivo que precisa de ser cada vez maior, representa a ordem dos relatórios. É utilizado pela loja de saúde para detetar relatórios antigos que são recebidos tardiamente devido a atrasos na rede ou outros problemas. Um relatório é rejeitado se o número de sequência for inferior ou igual ao número mais recentemente aplicado para a mesma entidade, fonte e propriedade. Se não for especificado, o número da sequência é gerado automaticamente. É necessário colocar o número da sequência apenas quando se reporta sobre as transições estatais. Nesta situação, a fonte precisa de se lembrar quais os relatórios que enviou e manter a informação para recuperação sobre o fracasso.

Estas quatro peças de informação--SourceId, identificador de entidades, propriedade e Estado de Saúde - são necessárias para cada relatório de saúde. A cadeia SourceId não está autorizada a começar com o prefixo "**System .** ", que é reservado para relatórios do sistema. Para a mesma entidade, há apenas um relatório para a mesma fonte e imóvel. Vários relatórios para a mesma fonte e propriedade sobrepõem-se uns aos outros, quer do lado do cliente de saúde (se forem loteados) quer do lado da loja de saúde. A substituição baseia-se em números de sequência; os relatórios mais recentes (com números de sequência mais elevados) substituem relatórios mais antigos.

### <a name="health-events"></a>Eventos de saúde
Internamente, a loja de saúde mantém eventos de [saúde](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), que contêm toda a informação dos relatórios, e metadados adicionais. Os metadados incluem o tempo em que o relatório foi dado ao cliente de saúde e o tempo que foi modificado no lado do servidor. Os eventos de saúde são devolvidos por consultas de [saúde.](service-fabric-view-entities-aggregated-health.md#health-queries)

Os metadados adicionados contêm:

* **SourceUtcTimestamp**. O momento em que o relatório foi dado ao cliente de saúde (Tempo Universal Coordenado).
* **LastModificadoUtcTimestamp**. O momento em que o relatório foi modificado pela última vez no lado do servidor (Tempo Universal Coordenado).
* **IsExpired**. Uma bandeira para indicar se o relatório expirou quando a consulta foi executada pela loja de saúde. Um evento só pode ser expirado se removeWhenExpired for falso. Caso contrário, o evento não é devolvido por consulta e é removido da loja.
* **Lastoktransitionat**, **LastWarningTransitionat,** **LastErrorTransitionat**. A última vez para transições OK/aviso/erro. Estes campos dão a história das transições do estado de saúde para o evento.

Os campos de transição do Estado podem ser usados para alertas mais inteligentes ou informações "históricas" de eventos de saúde. Permitem cenários como:

* Alerta quando uma propriedade está em alerta/erro por mais de X minutos. Verificar a condição por um período de tempo evita alertas em condições temporárias. Por exemplo, um alerta se o estado de saúde tiver sido avisado por mais de cinco minutos pode ser traduzido para (HealthState == Warning and Now - LastWarningTransitionTime > 5 minutos).
* Alerta apenas sobre as condições que mudaram nos últimos x minutos. Se um relatório já estava errado antes do tempo especificado, pode ser ignorado porque já tinha sido sinalizado anteriormente.
* Se uma propriedade estiver a alternar entre o aviso e o erro, determine quanto tempo não foi saudável (isto é, não OK). Por exemplo, um alerta se a propriedade não estiver saudável há mais de cinco minutos pode ser traduzido para (HealthState != Ok e Now - LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Exemplo: Reportar e avaliar a saúde das aplicações
O exemplo seguinte envia um relatório de saúde através da PowerShell no tecido da **aplicação:/WordCount** da fonte **MyWatchdog**. O relatório de saúde contém informações sobre a "disponibilidade" da propriedade de saúde num estado de saúde de erro, com o infinito TimeToLive. Em seguida, questiona a saúde da aplicação, que devolve erros agregados do Estado de Saúde e os eventos de saúde relatados na lista de eventos de saúde.

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

## <a name="health-model-usage"></a>Utilização do modelo de saúde
O modelo de saúde permite que os serviços de nuvem e a plataforma de Tecido de Serviço subjacente à escala, porque a monitorização e as determinações de saúde são distribuídas entre os diferentes monitores dentro do cluster.
Outros sistemas têm um único serviço centralizado ao nível do cluster que analisa todas as informações *potencialmente* úteis emitidas pelos serviços. Esta abordagem dificulta a sua escalabilidade. Também não lhes permite recolher informações específicas para ajudar a identificar problemas e potenciais problemas o mais próximo possível da causa principal.

O modelo de saúde é fortemente utilizado para monitorização e diagnóstico, para avaliar a saúde do cluster e aplicação, e para atualizações monitorizadas. Outros serviços utilizam dados de saúde para realizar reparações automáticas, construir o histórico de saúde do cluster e emitir alertas sobre determinadas condições.

## <a name="next-steps"></a>Passos Seguintes
[Ver relatórios de saúde de tecido de serviço](service-fabric-view-entities-aggregated-health.md)

[Use relatórios de saúde do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como reportar e verificar a saúde do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adicione relatórios de saúde personalizados de tecido de serviço](service-fabric-report-health.md)

[Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade de aplicação de tecido de serviço](service-fabric-application-upgrade.md)

