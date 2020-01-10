---
title: Saiba mais sobre o Azure Service Fabric
description: Saiba mais sobre os conceitos principais e as principais áreas do Azure Service Fabric. Fornece uma visão geral estendida de Service Fabric e como criar microserviços.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4e6e21f5f9ebfeddb5292e00dc8a929341e77372
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458145"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Você quer saber mais sobre Service Fabric?
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  No entanto, Service Fabric tem uma grande área de superfície, e há muito a aprender.  Este artigo fornece uma sinopse de Service Fabric e descreve os principais conceitos, modelos de programação, ciclo de vida do aplicativo, testes, clusters e monitoramento de integridade. Leia a [visão geral](service-fabric-overview.md) e [o que são os microserviços?](service-fabric-overview-microservices.md) para obter uma introdução e como Service Fabric pode ser usado para criar microserviços. Este artigo não contém uma lista de conteúdo abrangente, mas vincula a visão geral e artigos de introdução para cada área de Service Fabric. 

## <a name="core-concepts"></a>Conceitos-chave
[Service Fabric terminologia](service-fabric-technical-overview.md), [modelo de aplicativo](service-fabric-application-model.md)e [modelos de programação com suporte](service-fabric-choose-framework.md) fornecem mais conceitos e descrições, mas aqui estão os conceitos básicos.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Tempo de design: tipo de serviço, pacote de serviço e manifesto, tipo de aplicativo, pacote de aplicativos e manifesto
Um tipo de serviço é o nome/versão atribuído aos pacotes de código, pacotes de dados e pacotes de configuração de um serviço. Isso é definido em um arquivo de manifesto. xml. O tipo de serviço é composto por código executável e definições de configuração de serviço, que são carregados em tempo de execução e dados estáticos que são consumidos pelo serviço.

Um pacote de serviço é um diretório de disco que contém o arquivo Service manifest. XML do tipo de serviço, que faz referência ao código, aos dados estáticos e aos pacotes de configuração para o tipo de serviço. Por exemplo, um pacote de serviço pode referir-se ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço banco de dados.

Um tipo de aplicativo é o nome/versão atribuído a uma coleção de tipos de serviço. Isso é definido em um arquivo ApplicationManifest. xml.

![Tipos de aplicativos Service Fabric e tipos de serviço][cluster-imagestore-apptypes]

O pacote de aplicativos é um diretório de disco que contém o arquivo ApplicationManifest. XML do tipo de aplicativo, que faz referência aos pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicativo. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviço de fila, um pacote de serviço de front-end e um pacote de serviço de banco de dados.  

Os arquivos no diretório do pacote de aplicativos são copiados para o armazenamento de imagens do Cluster Service Fabric. Em seguida, você pode criar um aplicativo nomeado desse tipo de aplicativo, que é executado no cluster. Depois de criar um aplicativo nomeado, você pode criar um serviço nomeado de um dos tipos de serviço do tipo de aplicativo. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Tempo de execução: clusters e nós, chamados de aplicativos, chamados de serviços, partições e réplicas
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Os clusters podem ser dimensionados para milhares de máquinas.

Uma máquina ou VM que faça parte de um cluster é denominada um nó. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como as propriedades de colocação. Cada máquina ou VM tem um serviço de início automático do Windows, `FabricHost.exe`, que começa a ser executado na inicialização e, em seguida, inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Esses dois executáveis compõem o nó. Para cenários de desenvolvimento ou teste, você pode hospedar vários nós em um único computador ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe`.

Um aplicativo nomeado é uma coleção de serviços nomeados que executa uma determinada função ou funções. Um serviço executa uma função completa e autônoma (ela pode ser iniciada e executada independentemente de outros serviços) e é composta de código, configuração e dados. Depois que um pacote de aplicativos é copiado para o repositório de imagens, você cria uma instância do aplicativo dentro do cluster especificando o tipo de aplicativo do pacote de aplicativos (usando seu nome/versão). Cada instância de tipo de aplicativo recebe um nome de URI semelhante a *Fabric:/MyNamedApp*. Em um cluster, você pode criar vários aplicativos nomeados a partir de um único tipo de aplicativo. Você também pode criar aplicativos nomeados de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e versionado de forma independente.

Depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço (um serviço nomeado) dentro do cluster especificando o tipo de serviço (usando seu nome/versão). Cada instância de tipo de serviço recebe um nome de URI com escopo no URI do aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MyDatabase" em um aplicativo chamado "MyNamedApp", o URI será semelhante a: *Fabric:/MyNamedApp/MyDatabase*. Em um aplicativo nomeado, você pode criar um ou mais serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância/réplica. 

Há dois tipos de serviços: sem estado e com estado. Os serviços sem estado não armazenam o estado no serviço. Os serviços sem estado não têm armazenamento persistente nem armazenam o estado persistente em um serviço de armazenamento externo, como o armazenamento do Azure, o banco de dados SQL do Azure ou o Azure Cosmos DB. Um serviço com estado armazena o estado no serviço e usa coleções confiáveis ou modelos de programação de Reliable Actors para gerenciar o estado. 

Ao criar um serviço nomeado, você especifica um esquema de partição. Os serviços com grandes quantidades de estado dividem os dados entre partições. Cada partição é responsável por uma parte do estado completo do serviço, que é distribuído entre os nós do cluster.  

O diagrama a seguir mostra a relação entre aplicativos e instâncias de serviço, partições e réplicas.

![Partições e réplicas em um serviço][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Particionamento, dimensionamento e disponibilidade
O [particionamento](service-fabric-concepts-partitioning.md) não é exclusivo para Service Fabric. Uma forma bem conhecida de particionamento é o particionamento de dados ou A fragmentação. Serviços com estado com grandes quantidades de estado dividem os dados entre partições. Cada partição é responsável por uma parte do estado completo do serviço. 

As réplicas de cada partição são distribuídas entre os nós do cluster, o que permite que o estado do serviço nomeado seja [dimensionado](service-fabric-concepts-scalability.md). À medida que os dados precisam crescer, as partições crescem e Service Fabric reequilibra partições entre nós para fazer uso eficiente dos recursos de hardware. Se você adicionar novos nós ao cluster, Service Fabric reequilibrará as réplicas de partição entre o maior número de nós. O desempenho geral do aplicativo melhora e a contenção de acesso à memória diminui. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. Service Fabric novamente reequilibra as réplicas de partição entre o número reduzido de nós para fazer melhor uso do hardware em cada nó.

Em uma partição, os serviços nomeados sem estado têm instâncias, enquanto os serviços nomeados com estado têm réplicas. Normalmente, os serviços nomeados sem monitoração de Estado têm apenas uma partição, pois não têm estado interno. As instâncias de partição fornecem [disponibilidade](service-fabric-availability-services.md). Se uma instância falhar, outras instâncias continuarão a operar normalmente e, em seguida, Service Fabric criar uma nova instância. Os serviços nomeados com estado mantêm seu estado dentro das réplicas e cada partição tem seu próprio conjunto de réplicas. As operações de leitura e gravação são executadas em uma réplica (chamada de primária). As alterações no estado de operações de gravação são replicadas para várias outras réplicas (chamadas de secundárias ativas). Se uma réplica falhar, Service Fabric criará uma nova réplica a partir das réplicas existentes.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microsserviços com e sem monitorização de estado para o Service Fabric
O Service Fabric permite-lhe criar aplicações que consistem em microsserviços ou contentores. Os microsserviços sem estado (como gateways de protocolos e proxies Web) não mantêm um estado mutável fora dos pedidos nem na respetiva resposta do serviço. As funções de trabalho dos Serviços Cloud do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (como contas de utilizador, bases de dados, dispositivos, carrinhos de compras e filas) mantêm um estado mutável e autoritativo para lá do pedido e da respetiva resposta. As aplicações à escala da cloud dos nossos dias são compostas por uma combinação de microsserviços com e sem estado. 

Uma importante diferença na Service Fabric é seu forte enfoque na criação de serviços com estado, com os [modelos de programação internos](service-fabric-choose-framework.md) ou com serviços com estado em contêineres. Os [cenários de aplicação](service-fabric-application-scenarios.md) descrevem os cenários em que são utilizados os serviços com estado.

Por que ter microserviços com estado junto com aqueles sem monitoração de estado? Os dois principais motivos são:

* Você pode criar serviços OLTP (processamento de transações online) de alta taxa de transferência, baixa latência e tolerante a falhas mantendo o código e os dados fechados no mesmo computador. Alguns exemplos são vitrines interativas, sistemas de pesquisa, Internet das Coisas (IoT), sistemas comerciais, sistemas de detecção de fraudes e processamento de cartão de crédito e gerenciamento de registros pessoais.
* Você pode simplificar o design do aplicativo. Os microserviços com estado eliminam a necessidade de filas e caches adicionais, que são tradicionalmente necessárias para atender aos requisitos de disponibilidade e latência de um aplicativo puramente sem estado. Os serviços com estado são naturalmente de alta disponibilidade e baixa latência, o que reduz o número de partes móveis a serem gerenciadas em seu aplicativo como um todo.

## <a name="supported-programming-models"></a>Modelos de programação suportados
O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços. Os serviços podem usar as APIs de Service Fabric para aproveitar ao máximo os recursos e as estruturas de aplicativo da plataforma. Os serviços também podem ser qualquer programa executável compilado escrito em qualquer linguagem e hospedado em um Cluster Service Fabric. Para obter mais informações, consulte [modelos de programação com suporte](service-fabric-choose-framework.md).

### <a name="containers"></a>Contentores
Por padrão, o Service Fabric implanta e ativa serviços como processos. Service Fabric também pode implantar serviços em [contêineres](service-fabric-containers-overview.md). É importante que você possa misturar serviços em processos e serviços em contêineres no mesmo aplicativo. O Service Fabric dá suporte à implantação de contêineres do Linux e contêineres do Windows no Windows Server 2016. Você pode implantar aplicativos existentes, serviços sem estado ou serviços com estado em contêineres. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) é uma estrutura leve para escrever serviços que se integram à plataforma de Service Fabric e se beneficiam do conjunto completo de recursos de plataforma. Reliable Services pode ser sem monitoração de estado (semelhante à maioria das plataformas de serviço, como servidores Web ou funções de trabalho nos serviços de nuvem do Azure), em que o estado é persistido em uma solução externa, como o Azure DB ou o armazenamento de tabelas do Azure. Reliable Services também pode ser com estado, em que o estado é persistido diretamente no próprio serviço usando coleções confiáveis. O estado se torna [altamente disponível](service-fabric-availability-services.md) por meio da replicação e distribuído por meio de [particionamento](service-fabric-concepts-partitioning.md), tudo gerenciado automaticamente pelo Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Baseado em Reliable Services, a estrutura de [ator confiável](service-fabric-reliable-actors-introduction.md) é uma estrutura de aplicativo que implementa o padrão de ator virtual, com base no padrão de design de ator. A estrutura de ator confiável usa unidades independentes de computação e estado com execução de thread único chamada atores. A estrutura de ator confiável fornece comunicação interna para atores e configurações de expansão e persistência de estado predefinido.

### <a name="aspnet-core"></a>ASP.NET Core
O Service Fabric integra-se com [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) como um modelo de programação de primeira classe para a criação de aplicativos Web e de API.  ASP.NET Core pode ser usado de duas maneiras diferentes no Service Fabric:

- Hospedado como um executável convidado. Isso é usado principalmente para executar aplicativos ASP.NET Core existentes no Service Fabric sem alterações de código.
- Execute dentro de um serviço confiável. Isso permite uma melhor integração com o tempo de execução do Service Fabric e permite serviços de ASP.NET Core com estado.

### <a name="guest-executables"></a>Executáveis de convidado
Um [executável convidado](service-fabric-guest-executables-introduction.md) é um executável existente e arbitrário (escrito em qualquer linguagem) hospedado em um Cluster Service Fabric junto com outros serviços. Os executáveis convidados não se integram diretamente às APIs de Service Fabric. No entanto, eles ainda se beneficiam dos recursos oferecidos pela plataforma, como integridade personalizada e geração de relatórios de carga e descoberta de serviço chamando APIs REST. Eles também têm suporte total ao ciclo de vida do aplicativo. 

## <a name="application-lifecycle"></a>Ciclo de vida da aplicação
Assim como em outras plataformas, um aplicativo em Service Fabric geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. O Service Fabric fornece suporte de primeira classe para o ciclo de vida completo do aplicativo de aplicativos em nuvem, desde o desenvolvimento até a implantação, gerenciamento diário e manutenção até o eventual encerramento. O modelo de serviço permite que várias funções diferentes participem de forma independente no ciclo de vida do aplicativo. [Service Fabric ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e como elas são usadas pelas diferentes funções em todas as fases do ciclo de vida do aplicativo Service Fabric. 

Todo o ciclo de vida do aplicativo pode ser gerenciado usando [cmdlets do PowerShell](/powershell/module/ServiceFabric/), [comandos da CLI](service-fabric-sfctl.md), [ C# APIs](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [APIs do Java](/java/api/overview/azure/servicefabric)e [APIs REST](/rest/api/servicefabric/). Você também pode configurar pipelines de integração contínua/implantação contínua usando ferramentas como [Azure pipelines](service-fabric-set-up-continuous-integration.md) ou [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

## <a name="test-applications-and-services"></a>Testar aplicações e serviços
Para criar serviços verdadeiramente em escala de nuvem, é essencial verificar se seus aplicativos e serviços podem resistir a falhas do mundo real. O serviço de análise de falha foi projetado para serviços de teste criados em Service Fabric. Com o [serviço de análise de falha](service-fabric-testability-overview.md), você pode induzir falhas significativas e executar cenários de teste completos em seus aplicativos. Essas falhas e cenários exercitam e validam os inúmeros Estados e as transições que um serviço experimentará durante seu tempo de vida, tudo de maneira controlada, segura e consistente.

As [ações](service-fabric-testability-actions.md) destinam-se a um serviço para teste usando falhas individuais. Um desenvolvedor de serviços pode usá-los como blocos de construção para escrever cenários complicados. Exemplos de falhas simuladas são:

* Reinicie um nó para simular qualquer número de situações em que um computador ou VM é reinicializado.
* Mova uma réplica de seu serviço com estado para simular o balanceamento de carga, failover ou atualização de aplicativo.
* Invoque a perda de quorum em um serviço com estado para criar uma situação em que as operações de gravação não podem continuar porque não há réplicas "backup" ou "secundária" suficientes para aceitar novos dados.
* Invoque a perda de dados em um serviço com estado para criar uma situação em que todo o estado na memória seja completamente apagado.

[Cenários](service-fabric-testability-scenarios.md) são operações complexas compostas de uma ou mais ações. O serviço de análise de falha fornece dois cenários completos internos:

* [Cenário de caos](service-fabric-controlled-chaos.md)– simula falhas contínuas e intercaladas (normais e inconvenientes) em todo o cluster por longos períodos de tempo.
* [Cenário de failover](service-fabric-testability-scenarios.md#failover-test)– uma versão do cenário de teste de caos que tem como alvo uma partição de serviço específica, deixando outros serviços não afetados.

## <a name="clusters"></a>Clusters
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Os clusters podem ser dimensionados para milhares de máquinas. Uma máquina ou VM que faz parte de um cluster é chamada de nó de cluster. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como as propriedades de colocação. Cada máquina ou VM tem um serviço de início automático, `FabricHost.exe`, que começa a ser executado na inicialização e, em seguida, inicia dois executáveis: Fabric. exe e FabricGateway. exe. Esses dois executáveis compõem o nó. Para cenários de teste, você pode hospedar vários nós em um único computador ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe`.

Service Fabric clusters podem ser criados em máquinas virtuais ou físicas que executam o Windows Server ou o Linux. Você pode implantar e executar Service Fabric aplicativos em qualquer ambiente em que tenha um conjunto de computadores Windows Server ou Linux interconectados: no local, em Microsoft Azure ou em qualquer provedor de nuvem.

### <a name="clusters-on-azure"></a>Clusters no Azure
A execução de clusters Service Fabric no Azure fornece integração com outros recursos e serviços do Azure, o que torna as operações e o gerenciamento do cluster mais fáceis e confiáveis. Um cluster é um recurso Azure Resource Manager, para que você possa modelar clusters como qualquer outro recurso no Azure. O Gerenciador de recursos também fornece um gerenciamento fácil de todos os recursos usados pelo cluster como uma única unidade. Os clusters no Azure são integrados ao diagnóstico do Azure e aos logs de Azure Monitor. Os tipos de nó de cluster são [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/index), portanto, a funcionalidade de dimensionamento automático é interna.

Você pode criar um cluster no Azure por meio do [portal do Azure](service-fabric-cluster-creation-via-portal.md), de um [modelo](service-fabric-cluster-creation-via-arm.md)ou do [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

O Service Fabric no Linux permite que você crie, implante e gerencie aplicativos altamente disponíveis e altamente escalonáveis no Linux, assim como faria no Windows. As estruturas de Service Fabric (Reliable Services e Reliable Actors) estão disponíveis em Java no Linux, além de C# (.NET Core). Você também pode criar [Serviços executáveis convidados](service-fabric-guest-executables-introduction.md) com qualquer linguagem ou estrutura. Também há suporte para a orquestração de contêineres do Docker. Os contêineres do Docker podem executar executáveis de convidado ou serviços de Service Fabric nativos, que usam as estruturas de Service Fabric. Para obter mais informações, leia sobre [Service Fabric no Linux](service-fabric-deploy-anywhere.md).

Há alguns recursos com suporte no Windows, mas não no Linux. Para saber mais, leia as [diferenças entre Service Fabric no Linux e no Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Clusters autónomos
O Service Fabric fornece um pacote de instalação para criar clusters de Service Fabric autônomos localmente ou em qualquer provedor de nuvem. Os clusters autônomos oferecem a você a liberdade de hospedar um cluster onde quiser. Se seus dados estiverem sujeitos a restrições de conformidade ou normativas ou se você quiser manter seus dados locais, poderá hospedar seu próprio cluster e aplicativos. Service Fabric aplicativos podem ser executados em vários ambientes de hospedagem sem alterações, portanto, seu conhecimento de criação de aplicativos é transmitido de um ambiente de hospedagem para outro. 

[Criar seu primeiro Cluster Service Fabric autônomo](service-fabric-cluster-creation-for-windows-server.md)

Os clusters autônomos do Linux ainda não têm suporte.

### <a name="cluster-security"></a>Segurança em clusters
Os clusters devem ser protegidos para impedir que usuários não autorizados se conectem ao seu cluster, especialmente quando ele tem cargas de trabalho de produção em execução. Embora seja possível criar um cluster não seguro, isso permite que usuários anônimos se conectem a ele se os pontos de extremidade de gerenciamento forem expostos à Internet pública. Não é possível habilitar a segurança posteriormente em um cluster desprotegido: a segurança do cluster está habilitada no momento da criação do cluster.

Os cenários de segurança do cluster são:
* Segurança de nó para nó
* Segurança de cliente para nó
* Controlo de acesso baseado em funções (RBAC)

Para obter mais informações, leia [proteger um cluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Dimensionamento
Se você adicionar novos nós ao cluster, Service Fabric reequilibrará as réplicas e instâncias de partição entre o maior número de nós. O desempenho geral do aplicativo melhora e a contenção de acesso à memória diminui. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. Service Fabric novamente reequilibra as réplicas e instâncias de partição em todo o número de nós reduzido para fazer melhor uso do hardware em cada nó. Você pode dimensionar clusters no Azure de forma [manual](service-fabric-cluster-scale-up-down.md) ou [programática](service-fabric-cluster-programmatic-scaling.md). Os clusters autônomos podem ser dimensionados [manualmente](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Atualizações de cluster
Periodicamente, novas versões do Service Fabric Runtime são lançadas. Execute atualizações de tempo de execução ou malhas no cluster para que você sempre esteja executando uma [versão com suporte](service-fabric-support.md). Além das atualizações de malha, você também pode atualizar a configuração de cluster, como certificados ou portas de aplicativo.

Um Cluster Service Fabric é um recurso que você possui, mas é parcialmente gerenciado pela Microsoft. A Microsoft é responsável por aplicar patches no sistema operacional subjacente e executar atualizações de malha no cluster. Você pode definir seu cluster para receber atualizações automáticas de malha, quando a Microsoft lançar uma nova versão ou optar por selecionar uma versão de malha com suporte que você deseja. Atualizações de malha e configuração podem ser definidas por meio do portal do Azure ou por meio do Resource Manager. Para obter mais informações, leia [atualizar um cluster Service Fabric](service-fabric-cluster-upgrade.md). 

Um cluster autônomo é um recurso que você possui inteiramente. Você é responsável por aplicar patches no sistema operacional subjacente e iniciar atualizações de malha. Se o cluster puder se conectar ao [https://www.microsoft.com/download](https://www.microsoft.com/download), você poderá definir o cluster para baixar e provisionar automaticamente o novo pacote de tempo de execução do Service Fabric. Em seguida, você iniciaria a atualização. Se o cluster não puder acessar [https://www.microsoft.com/download](https://www.microsoft.com/download), você poderá baixar manualmente o novo pacote de tempo de execução de um computador conectado à Internet e iniciar a atualização. Para obter mais informações, leia [atualizar um cluster Service Fabric autônomo](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorização do estado de funcionamento
Service Fabric introduz um [modelo de integridade](service-fabric-health-introduction.md) projetado para sinalizar condições de cluster e de aplicativo não íntegras em entidades específicas (como nós de cluster e réplicas de serviço). O modelo de integridade usa relatórios de integridade (componentes do sistema e Watchdogs). O objetivo é o diagnóstico e o reparo rápidos e fáceis. Os gravadores de serviço precisam pensar antecipadamente sobre a integridade e como [projetar relatórios de integridade](service-fabric-report-health.md#design-health-reporting). Qualquer condição que possa afetar a integridade deve ser relatada, especialmente se puder ajudar a sinalizar problemas próximos à raiz. As informações de integridade podem poupar tempo e esforço na depuração e investigação quando o serviço está em execução em escala em produção.

Os relatórios de Service Fabric monitoram as condições de interesse identificadas. Eles relatam nessas condições com base em sua exibição local. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega os dados de integridade enviados por todos os relatórios para determinar se as entidades estão íntegras globalmente. O modelo deve ser avançado, flexível e fácil de usar. A qualidade dos relatórios de integridade determina a precisão da exibição de integridade do cluster. Falsos positivos que mostram incorretamente problemas não íntegros podem afetar negativamente as atualizações ou outros serviços que usam dados de integridade. Exemplos desses serviços são os serviços de reparo e os mecanismos de alerta. Portanto, algumas idéias são necessárias para fornecer relatórios que capturam condições de interesse da melhor maneira possível.

Os relatórios podem ser feitos em:
* A réplica ou instância do serviço monitorado Service Fabric.
* Watchdogs internos implantados como um serviço de Service Fabric (por exemplo, um serviço sem estado Service Fabric que monitora as condições e os relatórios de problemas). Os Watchdogs podem ser implantados em todos os nós ou podem ser relacionadosdos para o serviço monitorado.
* Watchdogs internos que são executados nos nós de Service Fabric, mas não são implementados como Service Fabric serviços.
* Watchdogs externos que investigam o recurso de fora do cluster de Service Fabric (por exemplo, serviço de monitoramento como Gomez).

Na caixa, os componentes do Service Fabric relatam a integridade em todas as entidades no cluster. Os [relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) fornecem visibilidade da funcionalidade do cluster e do aplicativo e sinalizam problemas por meio da integridade. Para aplicativos e serviços, os relatórios de integridade do sistema verificam se as entidades estão implementadas e se comportando corretamente da perspectiva do tempo de execução do Service Fabric. Os relatórios não fornecem nenhum monitoramento de integridade da lógica de negócios do serviço ou detectam processos que pararam de responder. Para adicionar informações de integridade específicas à lógica do serviço, [implemente relatórios de integridade personalizados](service-fabric-report-health.md) em seus serviços.

Service Fabric fornece várias maneiras de [exibir relatórios de integridade](service-fabric-view-entities-aggregated-health.md) agregados no repositório de integridade:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização.
* Consultas de integridade (por meio do [PowerShell](/powershell/module/ServiceFabric/), da [CLI](service-fabric-sfctl.md), das APIs do [ C# FabricClient](/dotnet/api/system.fabric.fabricclient.healthclient) e das APIs do [Java FabricClient](/java/api/system.fabric)ou das [APIs REST](/rest/api/servicefabric)).
* Consultas gerais que retornam uma lista de entidades que têm integridade como uma das propriedades (por meio do PowerShell, da CLI, das APIs ou do REST).

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico
O [monitoramento e o diagnóstico](service-fabric-diagnostics-overview.md) são essenciais para o desenvolvimento, o teste e a implantação de aplicativos e serviços em qualquer ambiente. Service Fabric soluções funcionam melhor quando você planeja e implementa o monitoramento e o diagnóstico que ajudam a garantir que os aplicativos e serviços estejam funcionando conforme o esperado em um ambiente de desenvolvimento local ou em produção.

Os principais objetivos do monitoramento e diagnóstico são:

- Detectar e diagnosticar problemas de hardware e de infraestrutura
- Detectar problemas de software e aplicativos, reduzir o tempo de inatividade do serviço
- Entender o consumo de recursos e ajudar as decisões de operações
- Otimizar o desempenho de aplicativos, serviços e infraestrutura
- Gerar informações de negócios e identificar áreas de aprimoramento
 
O fluxo de trabalho geral de monitoramento e diagnóstico consiste em três etapas:

1. Geração de eventos: isso inclui eventos (logs, rastreamentos, eventos personalizados) na infraestrutura (cluster), plataforma e nível de serviço/aplicativo
2. Agregação de eventos: os eventos gerados precisam ser coletados e agregados antes que possam ser exibidos
3. Análise: os eventos precisam ser visualizados e estar acessíveis em algum formato, para permitir a análise e a exibição conforme necessário

Há vários produtos disponíveis que abrangem essas três áreas, e você é livre para escolher tecnologias diferentes para cada um. Para obter mais informações, leia [monitoramento e diagnóstico para o Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autónomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Experimente criar um serviço com os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como [migrar dos serviços de nuvem](service-fabric-cloud-services-migration-differences.md).
* Aprenda a [monitorar e diagnosticar serviços](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Aprenda a [testar seus aplicativos e serviços](service-fabric-testability-overview.md).
* Aprenda a [gerenciar e orquestrar recursos de cluster](service-fabric-cluster-resource-manager-introduction.md).
* Examine os [exemplos de Service Fabric](https://aka.ms/servicefabricsamples).
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md).
* Leia o [blog da equipe](https://blogs.msdn.microsoft.com/azureservicefabric/) para obter artigos e anúncios.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
