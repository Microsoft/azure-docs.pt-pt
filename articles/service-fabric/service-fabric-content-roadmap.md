---
title: Saiba mais sobre o Azure Service Fabric
description: Conheça os conceitos centrais e as principais áreas do Azure Service Fabric. Fornece uma visão geral alargada do Tecido de Serviço e como criar microserviços.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 011ddf5db1555e83a1a61a349cc19ed791ab900b
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526771"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Quer aprender sobre o Service Fabric?
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  O Tecido de Serviço tem uma grande área de superfície, no entanto, e há muito a aprender.  Este artigo fornece uma sinopse de Tecido de Serviço e descreve os conceitos fundamentais, modelos de programação, ciclo de vida da aplicação, testes, clusters e monitorização de saúde. Leia a [Visão Geral](service-fabric-overview.md) e [O que são microserviços?](service-fabric-overview-microservices.md) Este artigo não contém uma lista completa de conteúdos, mas liga-se a uma visão geral e a receber artigos para todas as áreas do Service Fabric. 

## <a name="core-concepts"></a>Conceitos-chave
[Terminologia de Tecido de Serviço,](service-fabric-technical-overview.md) [Modelo de Aplicação](service-fabric-application-model.md)e [Modelos de programação Suportados](service-fabric-choose-framework.md) fornecem mais conceitos e descrições, mas aqui estão os fundamentos.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Tempo de design: tipo de serviço, pacote de serviço e manifesto, tipo de aplicação, pacote de aplicação e manifesto
Um tipo de serviço é o nome/versão atribuído a pacotes de código de um serviço, pacotes de dados e pacotes de configuração. Isto é definido num ficheiro ServiceManifest.xml. O tipo de serviço é composto por definições de configuração de código e serviço executáveis, que são carregadas no tempo de execução, e dados estáticos que são consumidos pelo serviço.

Um pacote de serviço é um diretório de discos contendo o ficheiro de ServiceManifest.xml do tipo de serviço, que faz referência aos pacotes de código, dados estáticos e de configuração para o tipo de serviço. Por exemplo, um pacote de serviço poderia consultar o código, dados estáticos e pacotes de configuração que compõem um serviço de base de dados.

Um tipo de aplicação é o nome/versão atribuído a uma coleção de tipos de serviço. Isto é definido num ficheiro ApplicationManifest.xml.

![Tipos de aplicações e tipos de serviço de tecido de serviço][cluster-imagestore-apptypes]

O pacote de aplicações é um diretório de discos que contém o ficheiro ApplicationManifest.xml do tipo de aplicação, que faz referência aos pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicação. Por exemplo, um pacote de aplicação para um tipo de aplicação de e-mail poderia conter referências a um pacote de serviço de fila, um pacote de serviço de frontend e um pacote de serviço de base de dados.  

Os ficheiros do diretório de pacotes de aplicações são copiados para a loja de imagens do cluster Service Fabric. Em seguida, pode criar uma aplicação nomeada a partir deste tipo de aplicação, que depois corre dentro do cluster. Depois de criar uma aplicação nomeada, pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicação. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Tempo de execução: clusters e nóns, aplicações nomeadas, serviços nomeados, divisórias e réplicas
Um [cluster de tecido](service-fabric-deploy-anywhere.md) de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Os clusters podem ser dimensionados para milhares de máquinas.

Uma máquina ou VM que faça parte de um cluster é denominada um nó. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como as propriedades de colocação. Cada máquina ou VM tem um serviço Windows de arranque automático, `FabricHost.exe` que começa a funcionar no arranque e, em seguida, inicia dois executáveis: e `Fabric.exe` `FabricGateway.exe` . Estes dois executáveis compõem o nó. Para cenários de desenvolvimento ou teste, pode hospedar vários nós numa única máquina ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe` .

Uma aplicação nomeada é uma coleção de serviços nomeados que desempenham uma determinada função ou funções. Um serviço executa uma função completa e autónoma (pode iniciar e funcionar independentemente de outros serviços) e é composto por código, configuração e dados. Depois de um pacote de aplicações ser copiado para a loja de imagens, cria-se uma instância da aplicação dentro do cluster especificando o tipo de aplicação do pacote de aplicações (utilizando o seu nome/versão). Cada instância do tipo de aplicação é atribuída a um nome URI que se parece com *tecido:/MyNamedApp*. Dentro de um cluster, pode criar várias aplicações nomeadas a partir de um único tipo de aplicação. Também pode criar aplicações nomeadas de diferentes tipos de aplicações. Cada aplicação nomeada é gerida e versão independente.

Depois de criar uma aplicação nomeada, pode criar uma instância de um dos seus tipos de serviço (um serviço nomeado) dentro do cluster especificando o tipo de serviço (utilizando o seu nome/versão). Cada instância do tipo de serviço é atribuída a um nome URI analisado no âmbito do URI da sua aplicação. Por exemplo, se criar um serviço com o nome "MyDatabase" dentro de uma aplicação com o nome "MyNamedApp", o URI parece: *tecido:/MyNamedApp/MyDatabase*. Dentro de uma aplicação nomeada, pode criar um ou mais serviços nomeados. Cada serviço nomeado pode ter o seu próprio esquema de partição e contagens de caso/réplica. 

Existem dois tipos de serviços: apátridas e imponentes. Os serviços apátridas não armazenam o estado dentro do serviço. Os serviços apátridas não têm armazenamento persistente em todo o estado ou armazenam um estado persistente num serviço de armazenamento externo, como Azure Storage, Azure SQL Database, ou Azure Cosmos DB. Um estado de serviços estatais estatal dentro do serviço e usa modelos de programação Reliable Collections ou Reliable Actors para gerir o estado. 

Ao criar um serviço nomeado, especifique um esquema de partição. Serviços com grandes quantidades de estado dividem os dados entre divisórias. Cada divisória é responsável por uma parte do estado completo do serviço, que se espalha por todos os nós do cluster.  

O diagrama seguinte mostra a relação entre aplicações e instâncias de serviço, divisórias e réplicas.

![Divisórias e réplicas dentro de um serviço][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partição, escalagem e disponibilidade
[A partilha](service-fabric-concepts-partitioning.md) não é exclusiva do Service Fabric. Uma forma bem conhecida de partição é a divisão de dados, ou o fragmento. Serviços estatais com grandes quantidades de estado dividem os dados entre divisórias. Cada divisória é responsável por uma parte do estado completo do serviço. 

As réplicas de cada partição estão espalhadas pelos nós do cluster, o que permite que o estado do seu serviço de nome [seja dimensionado.](service-fabric-concepts-scalability.md) À medida que os dados precisam de crescer, as divisórias crescem e o Service Fabric reequilibra as divisórias através dos nós para fazer uso eficiente dos recursos de hardware. Se adicionar novos nós ao cluster, o Tecido de Serviço reequilibrará as réplicas de partição através do aumento do número de nós. O desempenho geral da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Tecido de Serviço reequilibra novamente as réplicas de partição através do número reduzido de nós para melhor utilizar o hardware em cada nó.

Dentro de uma partição, os serviços apátridas têm casos, enquanto os serviços de nomes estatais têm réplicas. Normalmente, os serviços apátridas só têm uma divisão, uma vez que não têm estado interno, embora [existam exceções.](./service-fabric-concepts-partitioning.md#partition-service-fabric-stateless-services) As instâncias de partição prevêem [disponibilidade.](service-fabric-availability-services.md) Se uma instância falhar, outras instâncias continuam a funcionar normalmente e, em seguida, o Tecido de Serviço cria uma nova instância. Os serviços com nomes imponentes mantêm o seu estado dentro de réplicas e cada divisória tem o seu próprio conjunto de réplicas. As operações de leitura e escrita são realizadas numa réplica (chamada Primária). As alterações ao estado das operações de escrita são replicadas para várias outras réplicas (chamadas Secundários Ativos). Em caso de falha de uma réplica, o Service Fabric constrói uma nova réplica a partir das réplicas existentes.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microsserviços com e sem monitorização de estado para o Service Fabric
O Service Fabric permite-lhe criar aplicações que consistem em microsserviços ou contentores. Os microsserviços sem estado (como gateways de protocolos e proxies Web) não mantêm um estado mutável fora dos pedidos nem na respetiva resposta do serviço. As funções de trabalho dos Serviços Cloud do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (como contas de utilizador, bases de dados, dispositivos, carrinhos de compras e filas) mantêm um estado mutável e autoritativo para lá do pedido e da respetiva resposta. As aplicações à escala da cloud dos nossos dias são compostas por uma combinação de microsserviços com e sem estado. 

Uma diferenciação fundamental com o Service Fabric é o seu forte foco na construção de serviços estatais, quer com o [construído em modelos de programação,](service-fabric-choose-framework.md) quer com serviços estatais contentorizados. Os [cenários de aplicação](service-fabric-application-scenarios.md) descrevem os cenários em que são utilizados os serviços com estado.

Por que tem microserviços imponentes juntamente com os apátridas? As duas principais razões são:

* Pode construir serviços de processamento de transações on-line tolerantes a falhas (OLTP) mantendo o código e os dados fechados na mesma máquina. Alguns exemplos são montras interativas, sistemas de pesquisa, Internet das Coisas (IoT), sistemas de negociação, sistemas de processamento de cartões de crédito e deteção de fraudes e gestão de registos pessoais.
* Pode simplificar o design de aplicações. Os microserviços imponentes eliminam a necessidade de filas e caches adicionais, que são tradicionalmente obrigados a responder aos requisitos de disponibilidade e latência de uma aplicação puramente apátrida. Os serviços estatais são naturalmente de alta disponibilidade e baixa latência, o que reduz o número de peças móveis para gerir na sua aplicação como um todo.

## <a name="supported-programming-models"></a>Modelos de programação suportados
A Service Fabric oferece várias formas de escrever e gerir os seus serviços. Os serviços podem utilizar as APIs de Tecido de Serviço para tirar o máximo partido das funcionalidades e dos quadros de aplicação da plataforma. Os serviços também podem ser qualquer programa compilado executável escrito em qualquer idioma e hospedado num cluster de Tecido de Serviço. Para obter mais informações, consulte [os modelos de programação suportados.](service-fabric-choose-framework.md)

### <a name="containers"></a>Contentores
Por predefinição, o Service Fabric implementa e ativa os serviços como processos. O Tecido de Serviço também pode implantar serviços em [contentores.](service-fabric-containers-overview.md) Importante, pode misturar serviços em processos e serviços em contentores na mesma aplicação. O Service Fabric suporta a colocação de contentores Linux e de Windows no Windows Server 2016. Pode implementar aplicações existentes, serviços apátridas ou serviços estatais em contentores. 

### <a name="reliable-services"></a>Reliable Services
[O Reliable Services](service-fabric-reliable-services-introduction.md) é um quadro leve para serviços de escrita que se integram com a plataforma Service Fabric e beneficiam de todo o conjunto de funcionalidades da plataforma. Os Serviços Fiáveis podem ser apátridas (semelhantes à maioria das plataformas de serviços, tais como servidores web ou Funções de Trabalhador em Azure Cloud Services), onde o estado é persistido numa solução externa, como Azure DB ou Azure Table Storage. Os Serviços Fiáveis também podem ser imponentes, onde o estado é persistido diretamente no próprio serviço usando Coleções Fiáveis. O Estado é [altamente disponibilizado](service-fabric-availability-services.md) através da replicação e distribuído através [da partição,](service-fabric-concepts-partitioning.md)tudo gerido automaticamente pela Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Construído em cima de Serviços Confiáveis, o quadro [de Ator Fiável](service-fabric-reliable-actors-introduction.md) é uma estrutura de aplicação que implementa o padrão de Ator Virtual, baseado no padrão de design do ator. O quadro de Ator Fiável usa unidades independentes de computação e estado com execução de linha única chamadas atores. O quadro de Ator Fiável fornece uma comunicação construída para os atores e configurações de persistência e escala pré-definidas.

### <a name="aspnet-core"></a>ASP.NET Core
O Service Fabric integra-se com [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) como um modelo de programação de primeira classe para a construção de aplicações web e API.  ASP.NET Core pode ser usado de duas maneiras diferentes no Tecido de Serviço:

- Hospedado como um hóspede executável. Isto é usado principalmente para executar aplicações core ASP.NET existentes no Tecido de Serviço sem alterações de código.
- Corra dentro de um Serviço Fiável. Isto permite uma melhor integração com o tempo de funcionamento do Service Fabric e permite serviços ASP.NET Core.

### <a name="guest-executables"></a>Executáveis de convidado
Um [hóspede executável](service-fabric-guest-executables-introduction.md) é um executável arbitrário existente (escrito em qualquer língua) hospedado num cluster de Tecido de Serviço ao lado de outros serviços. Os executáveis de hóspedes não se integram diretamente com APIs de Tecido de Serviço. No entanto, ainda beneficiam de funcionalidades que a plataforma oferece, como a saúde personalizada e o relato de carga e a descoberta do serviço, chamando ASE DO REST. Também têm suporte completo de ciclo de vida de aplicações. 

## <a name="application-lifecycle"></a>Ciclo de vida da aplicação
Tal como acontece com outras plataformas, uma aplicação no Service Fabric passa normalmente pelas seguintes fases: design, desenvolvimento, testes, implementação, upgrade, manutenção e remoção. A Service Fabric fornece suporte de primeira classe para o ciclo de vida completo da aplicação de aplicações em nuvem, desde o desenvolvimento até à implantação, gestão diária e manutenção até ao eventual desmantelamento. O modelo de serviço permite que várias funções diferentes participem de forma independente no ciclo de vida da aplicação. O ciclo de vida da [aplicação service Fabric](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e como são usadas pelas diferentes funções ao longo das fases do ciclo de vida da aplicação Service Fabric. 

Todo o ciclo de vida da aplicação pode ser gerido usando [cmdlets PowerShell,](/powershell/module/ServiceFabric/New-ServiceFabricService) [comandos CLI](service-fabric-sfctl.md), [C# APIs,](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) [Java APIs,](/java/api/overview/azure/servicefabric)e [REST APIs](/rest/api/servicefabric/). Também pode configurar gasodutos de integração/implantação contínua contínuos utilizando ferramentas como [Azure Pipelines](./service-fabric-tutorial-deploy-app-with-cicd-vsts.md) ou [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster).

## <a name="test-applications-and-services"></a>Testar aplicações e serviços
Para criar serviços verdadeiramente em nuvem, é fundamental verificar se as suas aplicações e serviços podem resistir a falhas no mundo real. O Serviço de Análise de Falhas foi concebido para serviços de testes construídos em Tecido de Serviço. Com o [Serviço de Análise de Falhas,](service-fabric-testability-overview.md)pode induzir falhas significativas e executar cenários completos de teste contra as suas aplicações. Estas falhas e cenários exercitam e validam os inúmeros estados e transições que um serviço irá experimentar ao longo da sua vida, tudo de forma controlada, segura e consistente.

[As ações](service-fabric-testability-actions.md) direcionam-se para um serviço para testes utilizando falhas individuais. Um desenvolvedor de serviços pode usá-los como blocos de construção para escrever cenários complicados. Exemplos de falhas simuladas são:

* Reinicie um nó para simular qualquer número de situações em que uma máquina ou VM seja reiniciada.
* Mova uma réplica do seu serviço imponente para simular o equilíbrio de carga, failover ou atualização de aplicação.
* Invocar a perda de quórum num serviço estatal para criar uma situação em que as operações de escrita não podem prosseguir porque não há réplicas "back-up" ou "secundárias" suficientes para aceitar novos dados.
* Invocar a perda de dados num serviço estatal para criar uma situação em que todo o estado na memória é completamente eliminado.

[Os cenários](service-fabric-testability-scenarios.md) são operações complexas compostas por uma ou mais ações. O Serviço de Análise de Falhas fornece dois cenários completos incorporados:

* [Cenário de caos](service-fabric-controlled-chaos.md)- simula falhas contínuas e intercaladas (graciosas e desagradáveis) ao longo do cluster durante longos períodos de tempo.
* [Cenário de failover](service-fabric-testability-scenarios.md#failover-test)- uma versão do cenário de teste do caos que visa uma partição específica do serviço, deixando outros serviços inalterados.

## <a name="clusters"></a>Clusters
Um [cluster de tecido](service-fabric-deploy-anywhere.md) de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Os clusters podem ser dimensionados para milhares de máquinas. Uma máquina ou VM que faz parte de um cluster é chamada de nó de cluster. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como as propriedades de colocação. Cada máquina ou VM tem um serviço de arranque automático, `FabricHost.exe` que começa a funcionar no arranque e, em seguida, inicia dois executáveis: Fabric.exe e FabricGateway.exe. Estes dois executáveis compõem o nó. Para os cenários de teste, pode hospedar vários nós numa única máquina ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe` .

Os clusters de tecido de serviço podem ser criados em máquinas virtuais ou físicas que executam o Windows Server ou o Linux. É possível implementar e executar aplicações service Fabric em qualquer ambiente onde tenha um conjunto de computadores Windows Server ou Linux que estejam interligados: no local, no Microsoft Azure ou em qualquer fornecedor de nuvem.

### <a name="clusters-on-azure"></a>Clusters no Azure
Os clusters de tecido de serviço de execução em Azure proporcionam integração com outras funcionalidades e serviços Azure, o que torna as operações e gestão do cluster mais fáceis e fiáveis. Um cluster é um recurso Azure Resource Manager, para que possa modelar clusters como qualquer outro recurso em Azure. O Gestor de Recursos também fornece uma gestão fácil de todos os recursos utilizados pelo cluster como uma única unidade. Os clusters em Azure são integrados com diagnósticos Azure e registos Azure Monitor. Os tipos de nó de cluster são [conjuntos de escala de máquina virtual,](../virtual-machine-scale-sets/index.yml)por isso a funcionalidade de autoescalagem é incorporada.

Pode criar um cluster no Azure através do [portal Azure,](service-fabric-cluster-creation-via-portal.md)a partir de um [modelo](service-fabric-cluster-creation-via-arm.md), ou do [Visual Studio](./service-fabric-cluster-creation-via-arm.md).

O Service Fabric no Linux permite-lhe construir, implementar e gerir aplicações altamente disponíveis e altamente escaláveis no Linux, tal como faria no Windows. Os quadros do Tecido de Serviço (Reliable Services e Reliable Actors) estão disponíveis em Java no Linux, além de C# (.NET Core). Também pode construir [serviços executáveis de hóspedes](service-fabric-guest-executables-introduction.md) com qualquer idioma ou enquadramento. Orquestrar contentores Docker também é suportado. Os contentores docker podem executar executáveis de hóspedes ou serviços nativos de Tecido de Serviço, que utilizam as estruturas do Tecido de Serviço. Para mais informações, leia sobre [o Tecido de Serviço no Linux.](service-fabric-deploy-anywhere.md)

Existem algumas funcionalidades que são suportadas no Windows, mas não no Linux. Para saber mais, leia [diferenças entre tecido de serviço no Linux e Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Clusters autónomos
O Service Fabric fornece um pacote de instalação para criar clusters de tecido de serviço autónomo no local ou em qualquer fornecedor de nuvem. Aglomerados autónomos dão-lhe a liberdade de acolher um aglomerado onde quiser. Se os seus dados estiverem sujeitos a restrições de conformidade ou regulamentares, ou pretender manter os seus dados locais, pode hospedar o seu próprio cluster e aplicações. As aplicações de Tecido de Serviço podem funcionar em vários ambientes de hospedagem sem alterações, pelo que o seu conhecimento de aplicações de construção vai de um ambiente de hospedagem para outro. 

[Crie o primeiro cluster autónomo do Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Os aglomerados autónomos linux ainda não estão apoiados.

### <a name="cluster-security"></a>Segurança do cluster
Os clusters devem ser protegidos para evitar que os utilizadores não autorizados se conectem ao seu cluster, especialmente quando tem cargas de trabalho de produção em funcionamento. Embora seja possível criar um cluster não seguro, assim permite que os utilizadores anónimos se conectem ao mesmo se os pontos finais de gestão estiverem expostos à internet pública. Não é possível permitir mais tarde a segurança num cluster não seguro: a segurança do cluster é ativada no tempo de criação do cluster.

Os cenários de segurança do cluster são:
* Segurança nó-a-nó
* Segurança cliente-nó
* Controlo de acesso baseado em funções do Tecido de Serviço

Para mais informações, leia [Secure a cluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Dimensionamento
Se adicionar novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição e as instâncias através do aumento do número de nós. O desempenho geral da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Tecido de Serviço reequilibra novamente as réplicas de partição e instâncias através do número reduzido de nós para melhor utilizar o hardware em cada nó. Pode escalar os aglomerados em Azure [manualmente](service-fabric-cluster-scale-in-out.md) ou [programático.](service-fabric-cluster-programmatic-scaling.md) Os aglomerados autónomos podem ser dimensionados [manualmente.](service-fabric-cluster-windows-server-add-remove-nodes.md)

### <a name="cluster-upgrades"></a>Atualizações de cluster
Periodicamente, são lançadas novas versões do tempo de execução do Tecido de Serviço. Execute upgrades de tempo de execução ou tecido no seu cluster para que esteja sempre a executar uma [versão suportada](service-fabric-support.md). Além das atualizações de tecidos, também pode atualizar a configuração do cluster, como certificados ou portas de aplicação.

Um cluster de Tecido de Serviço é um recurso que possui, mas é parcialmente gerido pela Microsoft. A Microsoft é responsável por corrigir o sistema operativo subjacente e realizar atualizações de tecidos no seu cluster. Pode configurar o seu cluster para receber atualizações automáticas de tecidos, quando a Microsoft lançar uma nova versão, ou optar por selecionar uma versão de tecido suportado que pretende. As atualizações de tecido e configuração podem ser definidas através do portal Azure ou através do Gestor de Recursos. Para mais informações, leia [Upgrade um cluster de tecido de serviço.](service-fabric-cluster-upgrade.md) 

Um aglomerado autónomo é um recurso que possui inteiramente. Você é responsável por remendar o so subjacente e iniciar upgrades de tecido. Se o seu cluster puder [https://www.microsoft.com/download](https://www.microsoft.com/download) ligar-se, pode configurar o seu cluster para descarregar e providenciar automaticamente o novo pacote de tempo de execução do Service Fabric. Em seguida, iniciaria a atualização. Se o seu cluster não puder [https://www.microsoft.com/download](https://www.microsoft.com/download) aceder, pode descarregar manualmente o novo pacote de tempo de execução a partir de uma máquina ligada à Internet e, em seguida, iniciar a atualização. Para mais informações, leia [Upgrade um cluster de tecido de serviço autónomo.](service-fabric-cluster-upgrade-windows-server.md)

## <a name="health-monitoring"></a>Monitorização do estado de funcionamento
O Service Fabric introduz um [modelo de saúde](service-fabric-health-introduction.md) concebido para sinalizar condições de cluster e aplicação pouco saudáveis em entidades específicas (como nódes de cluster e réplicas de serviço). O modelo de saúde utiliza repórteres de saúde (componentes do sistema e cães de guarda). O objetivo é um diagnóstico e reparação fáceis e rápidos. Os escritores de serviços precisam de pensar frontalmente sobre a saúde e como [projetar relatórios de saúde.](service-fabric-report-health.md#design-health-reporting) Qualquer condição que possa ter impacto na saúde deve ser reportada, especialmente se puder ajudar a sinalizar problemas próximos da raiz. A informação de saúde pode economizar tempo e esforço na depuragem e investigação uma vez que o serviço está em funcionamento em escala na produção.

Os repórteres do Service Fabric monitorizam as condições de interesse identificadas. Relatam essas condições com base na sua visão local. A [loja de saúde](service-fabric-health-introduction.md#health-store) agrega dados de saúde enviados por todos os repórteres para determinar se as entidades são globalmente saudáveis. O modelo destina-se a ser rico, flexível e fácil de usar. A qualidade dos relatórios de saúde determina a precisão da visão de saúde do cluster. Falsos positivos que mostram erradamente problemas insalubres podem impactar negativamente atualizações ou outros serviços que usam dados de saúde. Exemplos destes serviços são serviços de reparação e mecanismos de alerta. Portanto, alguns pensamentos são necessários para fornecer relatórios que captam as condições de interesse da melhor maneira possível.

A comunicação pode ser feita a partir de:
* A réplica ou instância de serviço de serviço de serviço de serviço monitorizado.
* Cães de guarda internos implantados como um serviço de Tecido de Serviço (por exemplo, um serviço apátrida de Tecido de Serviço que monitoriza as condições e relatórios de problemas). Os cães de guarda podem ser implantados em todos os nós ou podem ser afinados com o serviço monitorizado.
* Cães de guarda internos que funcionam nos nós do Tecido de Serviço mas não são implementados como serviços de Service Fabric.
* Cães de guarda externos que sondam o recurso de fora do cluster de Tecido de Serviço (por exemplo, serviço de monitorização como o Gomez).

Fora da caixa, os componentes do Service Fabric reportam saúde a todas as entidades do cluster. [Os relatórios de saúde do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) proporcionam visibilidade na funcionalidade do cluster e aplicação e problemas de bandeira através da saúde. Para aplicações e serviços, os relatórios de saúde do sistema verificam se as entidades estão implementadas e estão a comportar-se corretamente do ponto de vista do tempo de funcionamento do Tecido de Serviço. Os relatórios não fornecem qualquer monitorização de saúde da lógica empresarial do serviço ou detetam processos que deixaram de responder. Para adicionar informações de saúde específicas à lógica do seu serviço, [implemente relatórios de saúde personalizados](service-fabric-report-health.md) nos seus serviços.

A Service Fabric fornece várias formas de [visualizar relatórios de saúde](service-fabric-view-entities-aggregated-health.md) agregados na loja de saúde:
* [Explorador de Tecidos de Serviço](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização.
* Consultas de saúde (através [de PowerShell](/powershell/module/ServiceFabric/New-ServiceFabricService), [CLI,](service-fabric-sfctl.md) [C# C# Fitis cclient](/dotnet/api/system.fabric.fabricclient.healthclient) e [Java FabricClient APIs](/java/api/system.fabric), ou [REST APIs](/rest/api/servicefabric)).
* Consultas gerais que devolvem uma lista de entidades que têm saúde como uma das propriedades (através de PowerShell, CLI, APIs ou REST).

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico
[A monitorização e os diagnósticos](service-fabric-diagnostics-overview.md) são fundamentais para o desenvolvimento, teste e implantação de aplicações e serviços em qualquer ambiente. As soluções de Tecido de Serviço funcionam melhor quando planeia e implementa monitorização e diagnósticos que ajudam a garantir que as aplicações e serviços estão a funcionar como esperado num ambiente de desenvolvimento local ou em produção.

Os principais objetivos de monitorização e diagnósticos são:

- Detetar e diagnosticar problemas de hardware e infraestruturas
- Detetar problemas de software e aplicativos, reduzir o tempo de inatividade do serviço
- Compreender o consumo de recursos e ajudar a impulsionar as decisões de operações
- Otimizar o desempenho da aplicação, serviço e infraestrutura
- Gerar insights de negócio e identificar áreas de melhoria
 
O fluxo de trabalho global de monitorização e diagnósticos consiste em três etapas:

1. Geração de eventos: isto inclui eventos (logs, vestígios, eventos personalizados) na infraestrutura (cluster), plataforma e nível de aplicação/serviço
2. Agregação de eventos: eventos gerados precisam de ser recolhidos e agregados antes de poderem ser exibidos
3. Análise: os eventos precisam de ser visualizados e acessíveis em algum formato, para permitir a análise e exibição conforme necessário

Vários produtos estão disponíveis que cobrem estas três áreas, e você é livre de escolher diferentes tecnologias para cada um. Para mais informações, leia [Monitorização e diagnósticos para O Tecido de Serviço Azure.](service-fabric-diagnostics-overview.md)

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autónomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Experimente criar um serviço com os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como [migrar dos Serviços cloud.](service-fabric-cloud-services-migration-differences.md)
* Aprenda a [monitorizar e diagnosticar serviços.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) 
* Aprenda a [testar as suas aplicações e serviços.](service-fabric-testability-overview.md)
* Aprenda a [gerir e orquestrar recursos de cluster.](service-fabric-cluster-resource-manager-introduction.md)
* Veja as [amostras de Tecido de Serviço.](/samples/browse/?products=azure)
* Saiba mais sobre [as opções de suporte do Tecido de Serviço.](service-fabric-support.md)
* Leia o blog da [equipa](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) para artigos e anúncios.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
