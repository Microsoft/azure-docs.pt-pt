---
title: Saiba mais sobre o Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre os conceitos centrais e as áreas principais do Azure Service Fabric. Fornece uma descrição geral alargada do Service Fabric e como criar microsserviços.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: ryanwi
ms.openlocfilehash: 12c5ba06eed37072d4c122be9e47a80ce94a5f1b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872833"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Portanto, quiser saber mais sobre o Service Fabric?
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  Service Fabric tem uma grande área de superfície, no entanto, e há muito a aprender.  Este artigo fornece uma sinopse do Service Fabric e descreve os principais conceitos, modelos, ciclo de vida do aplicativo, teste, clusters e monitorização de estado de funcionamento de programação. Leitura a [descrição geral](service-fabric-overview.md) e [o que são microsserviços?](service-fabric-overview-microservices.md) para uma introdução e como o Service Fabric pode ser usado para criar microsserviços. Este artigo não contém uma lista de conteúdo abrangente, mas associar à descrição geral e ao obter os artigos de introdução para todas as áreas do Service Fabric. 

## <a name="core-concepts"></a>Conceitos-chave
[Terminologia do Service Fabric](service-fabric-technical-overview.md), [modelo de aplicativo](service-fabric-application-model.md), e [modelos de programação suportados](service-fabric-choose-framework.md) fornecer mais conceitos e as descrições, mas aqui estão os fundamentos básicos.

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Tempo de design: tipo de aplicação, o tipo de serviço, o pacote de aplicação e manifesto, o pacote de serviço e manifesto
Um tipo de aplicação é o nome/versão atribuído a uma coleção de tipos de serviço. Isso é definido numa *Applicationmanifest* arquivo, que é incorporado num diretório do pacote de aplicação. O pacote de aplicação, em seguida, é copiado para armazenamento de imagens do cluster do Service Fabric. Em seguida, pode criar uma aplicação com o nome deste tipo de aplicação, que, em seguida, é executado dentro do cluster. 

Um tipo de serviço é o nome/versão atribuído a um serviço código pacotes, pacotes de dados e pacotes de configuração. Isso é definido num ficheiro servicemanifest. XML, que é incorporado num diretório do pacote de serviço. Diretório do pacote de serviço, em seguida, é referenciado por um pacote de aplicação *Applicationmanifest* ficheiro. Dentro do cluster, depois de criar um aplicativo nomeado, pode criar um serviço com nome de um dos tipos de serviço do tipo de aplicação. Um tipo de serviço é descrito pelo respetivo *servicemanifest. XML* ficheiro. O tipo de serviço é composta por definições de configuração do código executável serviço, que são carregadas em tempo de execução, bem como dados estáticos, que são utilizados pelo serviço.

![Tipos de recursos de infraestrutura do serviço de aplicações e tipos de serviço][cluster-imagestore-apptypes]

O pacote de aplicação é um diretório de disco que contém o tipo de aplicativo *Applicationmanifest* arquivo, que faz referência os pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicação. Por exemplo, um pacote de aplicação de um tipo de aplicação de e-mail pode conter referências a um pacote de serviço de fila, um pacote de serviço de front-end e um pacote de serviço de base de dados. Os ficheiros no diretório do pacote de aplicação são copiados para o arquivo de imagens do cluster do Service Fabric. 

Um pacote de serviço é um diretório de disco que contém o tipo de serviço *servicemanifest. XML* arquivo, que faz referência a código, dados estáticos e pacotes de configuração para o tipo de serviço. Os ficheiros no diretório do pacote de serviço são referenciados pelo tipo de aplicação *Applicationmanifest* ficheiro. Por exemplo, um pacote de serviço pode fazer referência a código, dados estáticos e pacotes de configuração que compõem um serviço de base de dados.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Tempo de execução: clusters e nós, chamados de aplicativos, serviços, partições e réplicas
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Os clusters podem ser dimensionados para milhares de máquinas.

Uma máquina ou VM que faça parte de um cluster é denominada um nó. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como as propriedades de colocação. Cada máquina ou a VM tem um serviço do Windows de início automático, `FabricHost.exe`, que começa a ser executado na inicialização e, em seguida, inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Estes dois executáveis compõem o nó. Para desenvolvimento ou cenários de teste, pode alojar vários nós numa única máquina ou VM ao executar várias instâncias `Fabric.exe` e `FabricGateway.exe`.

Um aplicativo nomeado é uma coleção de serviços com o nome que realiza uma determinada função ou funções. Um serviço executa uma função completa e autónoma (que pode iniciar e executar independentemente dos outros serviços) e é composto por código, configuração e dados. Depois de um pacote de aplicação é copiado para o armazenamento de imagens, cria uma instância da aplicação dentro do cluster, especificando o tipo de aplicação do pacote de aplicação (com o respetivo nome/versão). Cada instância de tipo de aplicação é atribuída um nome URI que se assemelha *fabric: / MyNamedApp*. Dentro de um cluster, pode criar várias aplicações com o nome de um tipo de aplicação único. Também pode criar aplicações com o nome de diferentes tipos de aplicativos. Cada aplicativo nomeado é com a versão e não geridos independentemente.

Depois de criar uma aplicação com nome, pode criar uma instância de um dos respetivos tipos de serviço (um serviço com nome) dentro do cluster, especificando o tipo de serviço (utilizando o respetivo nome/versão). Cada instância de tipo de serviço é atribuída um nome URI de âmbito no URI de seu aplicativo com nome. Por exemplo, se criar um serviço dentro de uma aplicação com o nome "MyNamedApp" com o nome "MyDatabase", o URI é semelhante a: *fabric: / MyNamedApp/MyDatabase*. Dentro de um aplicativo nomeado, pode criar um ou mais serviços com nome. Cada serviço com nome pode ter seu próprio esquema de partição e contagens de instâncias/réplicas. 

Existem dois tipos de serviços: com e sem estado. Serviços sem estado não armazenar o estado no serviço. Serviços sem estado tem sem armazenamento persistente de todo ou armazenam o estado persistente num serviço de armazenamento externo, como o armazenamento do Azure, base de dados do Azure SQL ou do Azure Cosmos DB. Um serviço com estado armazena o estado no serviço e utiliza modelos de programação Reliable Collections ou Reliable Actors para gerir o estado. 

Ao criar um serviço com nome, especificar um esquema de partição. Serviços com grandes quantidades de estado de dividir os dados entre partições. Cada partição é responsável por uma parte do estado completo do serviço, que é distribuído por todos nós do cluster.  

O diagrama seguinte mostra a relação entre aplicações e instâncias de serviço, partições e réplicas.

![As partições e réplicas dentro de um serviço][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Criação de partições, o dimensionamento e disponibilidade
[Criação de partições](service-fabric-concepts-partitioning.md) não é exclusivo para o Service Fabric. Uma forma bem conhecida de criação de partições é a criação de partições de dados de mensagens em fila ou a fragmentação. Serviços com estado com grandes quantidades de estado de dividir os dados entre partições. Cada partição é responsável por uma parte do Estado do serviço concluída. 

As réplicas de cada partição são distribuídas por nós do cluster, que permite que o estado do seu serviço com nome para [dimensionamento](service-fabric-concepts-scalability.md). Como os dados têm de aumentar, aumentam de partições e o Service Fabric rebalances partições entre os nós para fazer uso eficiente de recursos de hardware. Se adicionar novos nós ao cluster, o Service Fabric reequilibrar as réplicas de partição entre o maior número de nós. Em geral melhora o desempenho do aplicativo e reduz a contenção de acesso à memória. Se os nós do cluster não estão a ser utilizados com eficiência, pode diminuir o número de nós do cluster. Service Fabric rebalances novamente as réplicas de partição entre o menor número de nós para garantir uma melhor utilização do hardware em cada nó.

Dentro de uma partição, serviços com nome sem monitoração de estado têm o instâncias, enquanto nomeados de serviços com estado têm as réplicas. Normalmente, os serviços de nomeado sem monitoração de estado têm apenas uma partição, uma vez que não têm nenhum Estado interno. As instâncias de partição fornecem para [disponibilidade](service-fabric-availability-services.md). Se uma instância falhar, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Com monitorização de estado com o nome dos serviços de manter o estado dentro de réplicas e cada partição tem seu próprio conjunto de réplica. Operações de leitura e escrita são executadas numa réplica (chamada primário). Escrita de alterações para o estado de operações são replicadas para várias outras réplicas (chamadas de bases de dados secundárias de Active Directory). Se uma réplica falhar, o Service Fabric cria uma nova réplica a partir das réplicas existentes.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microsserviços com e sem monitorização de estado para o Service Fabric
O Service Fabric permite-lhe criar aplicações que consistem em microsserviços ou contentores. Os microsserviços sem estado (como gateways de protocolos e proxies Web) não mantêm um estado mutável fora dos pedidos nem na respetiva resposta do serviço. As funções de trabalho dos Serviços Cloud do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (como contas de utilizador, bases de dados, dispositivos, carrinhos de compras e filas) mantêm um estado mutável e autoritativo para lá do pedido e da respetiva resposta. As aplicações à escala da cloud dos nossos dias são compostas por uma combinação de microsserviços com e sem estado. 

Das principais diferenciações do Service Fabric é que se concentra na criação de serviços com estado, qualquer um deles com o [modelos de programação internas](service-fabric-choose-framework.md) ou com serviços com estado contentorizados. Os [cenários de aplicação](service-fabric-application-scenarios.md) descrevem os cenários em que são utilizados os serviços com estado.

Por que tem os microsserviços com estado, juntamente com aqueles sem monitoração de estado? Os dois motivos principais são:

* Pode criar o alto débito, baixa latência, tolerância a falhas falha de processamento de transações online services (OLTP) ao manter o código e dos dados na mesma máquina. Alguns exemplos são lojas interativas, pesquisa, sistemas de Internet das coisas (IoT), sistemas de negociação, sistemas de detecção de processamento e fraudes de cartões de crédito e gestão de registo pessoais.
* Pode simplificar o design do aplicativo. Os microsserviços com estado remover a necessidade de mais filas e caches, que são, tradicionalmente, necessárias para abordar os requisitos de disponibilidade e latência de um aplicativo puramente sem monitoração de estado. Serviços com estado são, naturalmente, elevada disponibilidade e baixa latência, que reduz o número de partes móveis para gerir em seu aplicativo como um todo.

## <a name="supported-programming-models"></a>Modelos de programação suportados
Service Fabric oferece várias maneiras de escrever e gerir os seus serviços. Serviços podem utilizar as APIs de recursos de infraestrutura do serviço para tirar partido das funcionalidades e estruturas de aplicativo da plataforma. Os serviços também podem ser qualquer programa executável compilado escritas em qualquer linguagem e alojadas num cluster do Service Fabric. Para obter mais informações, consulte [modelos de programação suportados](service-fabric-choose-framework.md).

### <a name="containers"></a>Contentores
Por predefinição, o Service Fabric implementa e ativa a serviços como processos. Service Fabric também pode implementar serviços no [contentores](service-fabric-containers-overview.md). Importante, pode combinar serviços em processos e serviços em contentores no mesmo aplicativo. Service Fabric suporta a implementação de contentores do Linux e contentores do Windows no Windows Server 2016. Pode implementar aplicações existentes, serviços sem estado ou serviços com estado em contentores. 

### <a name="reliable-services"></a>Reliable Services
[Serviços fiáveis](service-fabric-reliable-services-introduction.md) é uma estrutura leve para serviços que se integram com a plataforma do Service Fabric e o conjunto completo de funcionalidades de plataforma se beneficiar de escrever. Reliable Services pode ser sem estado (semelhante a maioria das plataformas de serviço, como servidores web ou funções de trabalho no Azure Cloud Services), em que estado é mantido numa solução externa, como o Azure DB ou o armazenamento de tabelas do Azure. Reliable Services também pode ser com monitoração de estado, onde o estado é mantido diretamente no serviço do próprio utilizando as coleções fiáveis. Estado é efetuado [elevada disponibilidade](service-fabric-availability-services.md) através da replicação e distribuídos por meio [criação de partições](service-fabric-concepts-partitioning.md), todos os geridos automaticamente pelo Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Criado com base no Reliable Services, o [Reliable Actor](service-fabric-reliable-actors-introduction.md) framework é uma estrutura de aplicações que implementa o padrão de Ator Virtual, com base no padrão de design de ator. A estrutura de Reliable Actor usa unidades independentes de computação e o estado com a execução de thread único chamada atores. O Reliable Actor framework fornece incorporada de comunicação para atores e previamente definir as configurações de escalamento horizontal e persistência de estado.

### <a name="aspnet-core"></a>Núcleo de ASP.NET
Service Fabric integra [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) como um modelo de programação de primeira classe para a criação de aplicações web e API.  ASP.NET Core podem ser utilizado de duas formas diferentes no Service Fabric:

- Hospedado como um executável de convidado. Isso é usado principalmente para executar aplicativos existentes do ASP.NET Core no Service Fabric sem alterações de código.
- Execute dentro de um serviço fiável. Isso permite uma melhor integração com o tempo de execução do Service Fabric e permite com monitoração de estado ASP.NET Core serviços.

### <a name="guest-executables"></a>Executáveis de convidado
R [executável convidado](service-fabric-guest-executables-introduction.md) é um existente, arbitrário executável (escrito em qualquer linguagem) alojado num cluster do Service Fabric juntamente com outros serviços. Executáveis de convidado se integra diretamente com as APIs de recursos de infraestrutura do serviço. No entanto eles ainda beneficiam de recursos, a plataforma oferece, como o estado de funcionamento personalizado e relatórios de carga e capacidade de deteção do serviço chamando as APIs REST. Também têm suporte de ciclo de vida do aplicativo completo. 

## <a name="application-lifecycle"></a>Ciclo de vida da aplicação
Como com outras plataformas, uma aplicação no Service Fabric normalmente executa as seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. O Service Fabric fornece suporte de primeira classe para o ciclo de vida do aplicativo completo das aplicações na cloud, desde o desenvolvimento através de implementação, gestão diária e manutenção até à desativação final. O modelo de serviço permite que várias funções diferentes para participar de forma independente do ciclo de vida do aplicativo. [O ciclo de vida do Service Fabric application](service-fabric-application-lifecycle.md) fornece uma descrição geral das APIs e como elas são usadas pelas funções de diferentes em todas as fases do ciclo de vida de aplicação do Service Fabric. 

O ciclo de vida do aplicativo inteiro pode ser gerido através de [cmdlets do PowerShell](/powershell/module/ServiceFabric/), [comandos da CLI](service-fabric-sfctl.md), [APIs c#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [Java APIs](/java/api/overview/azure/servicefabric), e [ REST APIs](/rest/api/servicefabric/). Pode configurar a integração contínua/contínua pipelines de implantação com ferramentas, como também [Pipelines do Azure](service-fabric-set-up-continuous-integration.md) ou [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

## <a name="test-applications-and-services"></a>Testar aplicações e serviços
Para realmente criar serviços de escala da cloud, é fundamental para verificar que seus aplicativos e serviços podem suportar a falhas do mundo real. O serviço de análise de falhas foi concebido para fins de teste de serviços que são criados no Service Fabric. Com o [serviço de análise de falhas](service-fabric-testability-overview.md), pode induza falhas significativas e executar cenários de teste completa contra as suas aplicações. Estas falhas e cenários de exercitem e validar os diversos Estados e transições que um serviço irá ocorrer em toda a sua vida, tudo de forma consistente, controlada e segura.

[Ações](service-fabric-testability-actions.md) um serviço para testes com falhas individuais de destino. Um desenvolvedor de serviço pode utilizar como blocos de construção para cenários complicados de escrever. São exemplos de falhas simulados:

* Reinicie o nó para simular a qualquer número de situações em que uma máquina ou VM é reiniciada.
* Mova uma réplica do seu serviço com monitorização de estado para simular o balanceamento de carga, ativação pós-falha ou atualização da aplicação.
* Invoca perda de quórum no serviço com estado para criar uma situação em que as operações de escrita não podem continuar porque não existe suficiente réplicas "backup" ou "secundárias" para aceitar novos dados.
* Invoca perda de dados num serviço com monitorização de estado para criar uma situação em que todos os Estados de dentro da memória é completamente eliminados.

[Cenários](service-fabric-testability-scenarios.md) operações complexas são compostas de uma ou mais ações. O serviço de análise de falhas fornece dois cenários completos incorporados:

* [Cenário de Chaos](service-fabric-controlled-chaos.md)-simula contínuas, intercaladas falhas (amigável e inesperadas) em todo o cluster através de longos períodos de tempo.
* [Cenário de ativação pós-falha](service-fabric-testability-scenarios.md#failover-test)-uma versão do cenário de teste chaos que tenha como destino uma partição de serviço específico, deixando os outros serviços afetados.

## <a name="clusters"></a>Clusters
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Os clusters podem ser dimensionados para milhares de máquinas. Uma máquina ou VM que faça parte de um cluster é chamado de um nó de cluster. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como as propriedades de colocação. Cada máquina ou a VM tem um serviço de início automático, `FabricHost.exe`, que começa a ser executado na inicialização e, em seguida, inicia dois executáveis: Fabric.exe e FabricGateway.exe. Estes dois executáveis compõem o nó. Para cenários de teste, pode alojar vários nós numa única máquina ou VM ao executar várias instâncias `Fabric.exe` e `FabricGateway.exe`.

Clusters do Service Fabric podem ser criadas em máquinas virtuais ou físicas que executem Windows Server ou Linux. Pode implementar e executar aplicações do Service Fabric em qualquer ambiente em que tem um conjunto de computadores Windows Server ou Linux que estão interligados: no local, no Microsoft Azure ou em qualquer fornecedor de cloud.

### <a name="clusters-on-azure"></a>Clusters no Azure
Executar clusters do Service Fabric no Azure fornece integração com outras funcionalidades do Azure e serviços, o que torna as operações e gestão do cluster mais fácil e mais confiável. Um cluster é um recurso do Azure Resource Manager, para que pode modelar clusters, como quaisquer outros recursos no Azure. Gestor de recursos também fornece gerenciamento fácil de todos os recursos utilizados pelo cluster, como uma única unidade. Clusters no Azure estão integrados com o diagnóstico do Azure e o Log Analytics. Tipos de nó de cluster são [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/index), por isso, a funcionalidade de dimensionamento automático é criada.

Pode criar um cluster no Azure através da [portal do Azure](service-fabric-cluster-creation-via-portal.md), de um [modelo](service-fabric-cluster-creation-via-arm.md), ou a partir de [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric no Linux permite-lhe criar, implementar e gerir aplicações de elevada disponibilidade e altamente dimensionáveis no Linux, tal como faria no Windows. As estruturas de Service Fabric (Reliable Services e Reliable Actors) estão disponíveis em Java no Linux, além de c# (.NET Core). Também pode criar [serviços de convidados executáveis](service-fabric-guest-executables-introduction.md) com qualquer linguagem ou arquitetura. Também é suportada a orquestração de contentores do Docker. Contentores do docker podem executar executáveis convidados ou serviços nativos do Service Fabric, que utilizam as estruturas de Service Fabric. Para obter mais informações, leia sobre [Service Fabric no Linux](service-fabric-deploy-anywhere.md).

Existem algumas funcionalidades que são suportadas no Windows, mas não no Linux. Para saber mais, leia [diferenças entre o Service Fabric no Linux e Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Clusters autónomos
O Service Fabric fornece um pacote de instalação para a criação de autónomo Service Fabric clusters no local ou em qualquer fornecedor de cloud. Clusters autónomos dão-lhe a liberdade de um cluster de anfitrião onde quiser. Se os seus dados estão sujeitos a conformidade ou regulamentação restrições ou pretende manter os seus dados local, pode alojar o seu próprio cluster e as aplicações. Aplicações do Service Fabric podem executar em vários ambientes de alojamento sem alterações, para que o seu conhecimento da criação de aplicativos realiza ao longo de um ambiente de alojamento para outro. 

[Crie seu primeiro cluster autónomo de Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Clusters autónomos do Linux ainda não são suportados.

### <a name="cluster-security"></a>Segurança do cluster
Clusters tem de ser protegidos para impedir que os utilizadores não autorizados a ligar ao seu cluster, especialmente quando se tem cargas de trabalho de produção em execução no mesmo. Embora seja possível criar um cluster não seguro, ao fazê-lo por isso, permite que os utilizadores anónimos ligar ao mesmo se os pontos finais de gestão são expostos à Internet pública. Não é possível ativar mais tarde a segurança num cluster não seguro: segurança do cluster está ativada no momento de criação do cluster.

Os cenários de segurança do cluster são:
* Segurança de nó para nó
* Segurança de cliente para nó
* Controlo de acesso baseado em funções (RBAC)

Para obter mais informações, leia [proteger um cluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Dimensionamento
Se adicionar novos nós ao cluster, o Service Fabric rebalances as réplicas de partição e as instâncias em aumento do número de nós. Em geral melhora o desempenho do aplicativo e reduz a contenção de acesso à memória. Se os nós do cluster não estão a ser utilizados com eficiência, pode diminuir o número de nós do cluster. Service Fabric rebalances novamente as instâncias e réplicas de partição entre o menor número de nós para garantir uma melhor utilização do hardware em cada nó. Pode dimensionar clusters no Azure seja [manualmente](service-fabric-cluster-scale-up-down.md) ou [programaticamente](service-fabric-cluster-programmatic-scaling.md). Clusters autónomos podem ser dimensionados [manualmente](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Atualizações de cluster
Periodicamente, são lançadas novas versões de runtime do Service Fabric. Efetuar atualizações de tempo de execução, ou recursos de infraestrutura, no seu cluster, de modo que sempre em execução uma [uma versão suportada](service-fabric-support.md). Para além das atualizações de recursos de infraestrutura, também pode atualizar a configuração de cluster, tais como certificados ou as portas da aplicação.

Um cluster do Service Fabric é um recurso que o proprietário, mas está parcialmente gerida pela Microsoft. A Microsoft é responsável pela aplicação de patches de sistema operacional subjacente e a execução de atualizações de recursos de infraestrutura no seu cluster. Pode definir o seu cluster para receber atualizações automáticas de recursos de infraestrutura, quando a Microsoft lança uma nova versão, ou optar por selecionar uma versão de recursos de infraestrutura suportadas que pretende. Atualizações de recursos de infraestrutura e a configuração podem ser definidas através do portal do Azure ou através do Resource Manager. Para obter mais informações, leia [atualizar um cluster do Service Fabric](service-fabric-cluster-upgrade.md). 

Um cluster autónomo é um recurso que inteiramente próprios. É responsável por aplicação de patches de sistema operacional subjacente e o início de atualizações de recursos de infraestrutura. Se o cluster pode se conectar à [ https://www.microsoft.com/download ](https://www.microsoft.com/download), pode definir o seu cluster transferir automaticamente e aprovisione o novo pacote de runtime do Service Fabric. Em seguida, deve iniciar a atualização. Se o cluster não conseguir aceder [ https://www.microsoft.com/download ](https://www.microsoft.com/download), pode transferir manualmente o novo pacote de tempo de execução de um computador de ligado à internet e, em seguida, iniciar a atualização. Para obter mais informações, leia [atualizar um cluster autónomo do Service Fabric](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorização do estado de funcionamento
Service Fabric introduz um [modelo de estado de funcionamento](service-fabric-health-introduction.md) projetado para sinalizar cluster mau estado de funcionamento e condições do aplicativo em entidades específicas (como nós de cluster e réplicas do serviço). O modelo de estado de funcionamento utiliza Informadores de estado de funcionamento (componentes do sistema e watchdogs). O objetivo é fácil e rápido de diagnóstico e reparo. Criadores de serviços precisam pensar iniciais sobre o estado de funcionamento e como [relatórios de estado de funcionamento de design](service-fabric-report-health.md#design-health-reporting). Deve ser relatada qualquer condição que pode afetar o estado de funcionamento, especialmente se ele pode ajudar a problemas de sinalizador próximo da raiz. As informações de estado de funcionamento podem poupar tempo e esforço de depuração e de investigação, uma vez o serviço está em execução em escala em produção.

O monitor do Service Fabric Informadores identificados condições de interesse. Que reportam nessas condições com base na respetiva vista local. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) agrega dados de estado de funcionamento enviados por todos os jornalistas para determinar se as entidades são globalmente em bom estadas. O modelo se destina a ser avançado, flexível e fácil de usar. A qualidade dos relatórios de estado de funcionamento determina a precisão da vista de estado de funcionamento do cluster. Falsos positivos que rostos mostram problemas de mau estado de funcionamento podem afetar negativamente as atualizações ou outros serviços que utilizam dados de estado de funcionamento. Exemplos de tais serviços são serviços de reparação e mecanismos de alerta. Por conseguinte, um pouco de reflexão é necessária para fornecer relatórios que capturam as condições de interesse da melhor forma possível.

Geração de relatórios pode ser feita em:
* A réplica de serviço do Service Fabric monitorizados ou a instância.
* Watchdogs internos implementados como um serviço do Service Fabric (por exemplo, um serviço Service Fabric sem monitoração de estado que monitoriza as condições e relatórios de problemas). Os watchdogs podem ser implementados em todos os nós ou podem ser com afinidade com o serviço monitorizado.
* Watchdogs internos que executam em nós de Service Fabric, mas não são implementados como serviços do Service Fabric.
* Watchdogs externos que o recurso de fora do cluster do Service Fabric (por exemplo, serviço de monitoramento, como Gomez) de sonda.

Imediato, componentes do Service Fabric comunicam o estado de funcionamento em todas as entidades no cluster. [Relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) proporcionar visibilidade para aplicações e clusters funcionalidade e o sinalizador de problemas por meio do Estado de funcionamento. Para aplicações e serviços, relatórios de estado de funcionamento do sistema Certifique-se de que as entidades são implementadas e estão se comportando corretamente da perspectiva do runtime do Service Fabric. Os relatórios não fornecem qualquer monitorização de estado de funcionamento da lógica comercial do serviço ou detectar processos bloqueados. Para adicionar informações de estado de funcionamento específicas a lógica do seu serviço, [implementar relatórios de estado de funcionamento personalizado](service-fabric-report-health.md) nos seus serviços.

O Service Fabric fornece várias formas de [ver relatórios de estado de funcionamento](service-fabric-view-entities-aggregated-health.md) agregados no arquivo de estado de funcionamento:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização.
* Consultas de estado de funcionamento (por meio [PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), o [c#: APIs FabricClient](/dotnet/api/system.fabric.fabricclient.healthclient) e [Java FabricClient APIs](/java/api/system.fabric), ou [REST APIs](/rest/api/servicefabric)).
* Geral consulta ou retorno uma lista de entidades que tenham o estado de funcionamento como uma das propriedades (através do PowerShell, CLI, APIs ou REST).

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico
[Monitorização e diagnóstico](service-fabric-diagnostics-overview.md) são fundamentais para o desenvolvimento, teste e implantação de aplicativos e serviços em qualquer ambiente. Soluções do Service Fabric funcionam melhor quando planear e implementar a monitorização e diagnóstico que o ajudam a garantir que aplicativos e serviços estão a funcionar conforme esperado num ambiente de desenvolvimento local ou na produção.

As principais metas da monitorização e diagnóstico são:

- Detetar e diagnosticar problemas de hardware e infraestrutura
- Detetar problemas de software e aplicação, a reduzir o período de indisponibilidade de serviço
- Compreender os recursos consumo e o ajudam a gerar operações decisões com base
- Otimizar o desempenho de aplicações, serviço e infraestrutura
- Gerar informações de negócio e identificar as áreas de melhoria
 
O fluxo de trabalho geral da monitorização e diagnóstico consiste em três passos:

1. Geração de evento: incluem eventos (registos, rastreios, eventos personalizados) no nível de aplicação / serviço, plataforma e infraestrutura (cluster)
2. Agregação de eventos: eventos gerados tem de ser recolhidos e agregados antes de poderem ser apresentados
3. Análise: eventos têm de ser visualizadas e acessíveis em algum formato, para permitir para análise e apresentar conforme necessário

Vários produtos estão disponíveis que abrangem os seguintes três pontos, e é livre para a escolha de tecnologias diferentes para cada um. Para obter mais informações, leia [monitorização e diagnóstico para o Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Passos Seguintes
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autónomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Experimente criar um serviço com os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como [migrar dos serviços Cloud](service-fabric-cloud-services-migration-differences.md).
* Aprenda a [monitorizar e diagnosticar serviços](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Aprenda a [testar seus aplicativos e serviços](service-fabric-testability-overview.md).
* Aprenda a [gerir e organizar recursos do cluster](service-fabric-cluster-resource-manager-introduction.md).
* Examine os [exemplos de Service Fabric](https://aka.ms/servicefabricsamples).
* Saiba mais sobre [opções de suporte do Service Fabric](service-fabric-support.md).
* Leitura a [blog da Equipe](https://blogs.msdn.microsoft.com/azureservicefabric/) para artigos e anúncios.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
