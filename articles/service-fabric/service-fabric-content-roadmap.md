---
title: Saiba mais sobre o Tecido de Serviço Azure
description: Conheça os conceitos centrais e as principais áreas do Tecido de Serviço Azure. Fornece uma visão geral alargada do Tecido de Serviço e como criar microserviços.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 573b1ec662bdc7e72f964698f5e0670860895586
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791855"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Quer aprender sobre o Tecido de Serviço?
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  O Tecido de Serviço tem uma grande área de superfície, no entanto, e há muito a aprender.  Este artigo fornece uma sinopse de Tecido de Serviço e descreve os conceitos fundamentais, modelos de programação, ciclo de vida da aplicação, testes, clusters e monitorização da saúde. Leia a [visão geral](service-fabric-overview.md) e quais são [os microserviços?](service-fabric-overview-microservices.md) Este artigo não contém uma lista completa de conteúdos, mas liga-se à visão geral e ao início de artigos para todas as áreas do Tecido de Serviço. 

## <a name="core-concepts"></a>Conceitos-chave
[A terminologia](service-fabric-technical-overview.md)do tecido de serviço, o modelo de [aplicação](service-fabric-application-model.md)e os modelos de [programação suportados](service-fabric-choose-framework.md) fornecem mais conceitos e descrições, mas aqui estão os básicos.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Tempo de design: tipo de serviço, pacote de serviço e manifesto, tipo de aplicação, pacote de aplicação e manifesto
Um tipo de serviço é o nome/versão atribuído aos pacotes de código de um serviço, pacotes de dados e pacotes de configuração. Isto é definido num ficheiro ServiceManifest.xml. O tipo de serviço é composto por definições de código e configuração de serviço executáveis, que são carregadas no tempo de execução, e dados estáticos que são consumidos pelo serviço.

Um pacote de serviço é um diretório de disco que contém o ficheiro ServiceManifest.xml do tipo de serviço, que faz referência ao código, dados estáticos e pacotes de configuração para o tipo de serviço. Por exemplo, um pacote de serviço poderia referir-se ao código, dados estáticos e pacotes de configuração que compõem um serviço de base de dados.

Um tipo de aplicação é o nome/versão atribuído a uma coleção de tipos de serviço. Isto é definido num ficheiro ApplicationManifest.xml.

![Tipos de aplicações de tecido de serviço e tipos de serviço][cluster-imagestore-apptypes]

O pacote de aplicação é um diretório de disco que contém o ficheiro ApplicationManifest.xml do tipo de aplicação, que faz referência aos pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicação. Por exemplo, um pacote de aplicação para um tipo de pedido de e-mail poderia conter referências a um pacote de serviço de fila, um pacote de serviço frontend e um pacote de serviço de base de dados.  

Os ficheiros do diretório do pacote de aplicações são copiados para a loja de imagens do cluster Service Fabric. Em seguida, pode criar uma aplicação nomeada a partir deste tipo de aplicação, que depois funciona dentro do cluster. Depois de criar uma aplicação nomeada, pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicação. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Tempo de execução: aglomerados e nós, aplicações nomeadas, serviços nomeados, divisórias e réplicas
Um [cluster de tecido](service-fabric-deploy-anywhere.md) de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Os clusters podem ser dimensionados para milhares de máquinas.

Uma máquina ou VM que faça parte de um cluster é denominada um nó. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como as propriedades de colocação. Cada máquina ou VM tem um `FabricHost.exe`serviço Windows de arranque automático, que começa `Fabric.exe` `FabricGateway.exe`a funcionar sobre o arranque e, em seguida, inicia dois executáveis: e . Estes dois executáveis compõem o nó. Para cenários de desenvolvimento ou teste, pode hospedar vários nós numa única `Fabric.exe` `FabricGateway.exe`máquina ou VM executando várias instâncias de e .

Uma aplicação nomeada é uma coleção de serviços nomeados que desempenha uma determinada função ou funções. Um serviço desempenha uma função completa e autónoma (pode iniciar e funcionar independentemente de outros serviços) e é composto por código, configuração e dados. Depois de um pacote de aplicação ser copiado para a loja de imagens, cria-se uma instância da aplicação dentro do cluster, especificando o tipo de aplicação do pacote de aplicação (utilizando o seu nome/versão). Cada instância do tipo de aplicação é atribuída a um nome URI que se parece com *tecido:/MyNamedApp*. Dentro de um cluster, pode criar múltiplas aplicações nomeadas a partir de um único tipo de aplicação. Também pode criar aplicações nomeadas de diferentes tipos de aplicações. Cada aplicação nomeada é gerida e versão independentemente.

Depois de criar uma aplicação nomeada, pode criar uma instância de um dos seus tipos de serviço (um serviço nomeado) dentro do cluster, especificando o tipo de serviço (utilizando o seu nome/versão). Cada instância do tipo de serviço é atribuída a um nome URI com o nome URI da sua aplicação. Por exemplo, se criar um serviço "MyDatabase" nomeado dentro de uma aplicação nomeada "MyNamedApp", o URI parece: *tecido:/MyNamedApp/MyDatabase*. Dentro de uma aplicação nomeada, pode criar um ou mais serviços nomeados. Cada serviço nomeado pode ter o seu próprio esquema de partição e contagens de exemplo/réplica. 

Existem dois tipos de serviços: apátridas e apátridas. Os serviços apátridas não armazenam o estado dentro do serviço. Os serviços apátridas não têm armazenamento persistente ou armazenam estado persistente num serviço de armazenamento externo, como o Azure Storage, azure SQL Database ou Azure Cosmos DB. Um estado de lojas de serviços estado dentro do serviço e usa Coleções Fiáveis ou modelos de programação De Atores Fiáveis para gerir o estado. 

Ao criar um serviço nomeado, especifice um esquema de partição. Os serviços com grandes quantidades de Estado dividem os dados entre divisórias. Cada partição é responsável por uma parte do estado completo do serviço, que está espalhada pelos nós do cluster.  

O diagrama seguinte mostra a relação entre aplicações e instâncias de serviço, divisórias e réplicas.

![Divisórias e réplicas dentro de um serviço][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partição, escalação e disponibilidade
[A partilha](service-fabric-concepts-partitioning.md) não é exclusiva do Tecido de Serviço. Uma forma bem conhecida de divisão é a partilha de dados, ou sharding. Serviços estatais com grandes quantidades de Estado dividem os dados entre divisórias. Cada partição é responsável por uma parte do estado completo do serviço. 

As réplicas de cada partição estão espalhadas pelos nós do cluster, o que permite escalar o estado do seu serviço [nomeado.](service-fabric-concepts-scalability.md) À medida que os dados crescem, as divisórias crescem e o Service Fabric reequilibra divisórias em nós para fazer uso eficiente dos recursos de hardware. Se adicionar novos nós ao cluster, o Tecido de Serviço reequilibrará as réplicas da partição através do número aumentado de nós. O desempenho global da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Service Fabric reequilibra novamente as réplicas da divisória através do número reduzido de nós para fazer melhor uso do hardware em cada nó.

Dentro de uma partição, os serviços nomeados apátridas têm casos enquanto os serviços nomeados pelo Estado têm réplicas. Normalmente, os serviços apátridas só têm uma divisória, uma vez que não têm estado interno. Os casos de partição prevêem [disponibilidade.](service-fabric-availability-services.md) Se uma instância falhar, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Os serviços de nome stateful mantêm o seu estado dentro de réplicas e cada partição tem o seu próprio conjunto de réplicas. As operações de leitura e escrita são realizadas numa réplica (chamada Primária). As alterações ao estado das operações de escrita são replicadas para várias outras réplicas (chamadas Secundárias Ativas). Se uma réplica falhar, o Service Fabric constrói uma nova réplica das réplicas existentes.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microsserviços com e sem monitorização de estado para o Service Fabric
O Service Fabric permite-lhe criar aplicações que consistem em microsserviços ou contentores. Os microsserviços sem estado (como gateways de protocolos e proxies Web) não mantêm um estado mutável fora dos pedidos nem na respetiva resposta do serviço. As funções de trabalho dos Serviços Cloud do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (como contas de utilizador, bases de dados, dispositivos, carrinhos de compras e filas) mantêm um estado mutável e autoritativo para lá do pedido e da respetiva resposta. As aplicações à escala da cloud dos nossos dias são compostas por uma combinação de microsserviços com e sem estado. 

Uma diferenciação fundamental com o Service Fabric é o seu forte foco na construção de serviços estatais, quer com os [construídos em modelos de programação,](service-fabric-choose-framework.md) quer com serviços estatais contentorizados. Os [cenários de aplicação](service-fabric-application-scenarios.md) descrevem os cenários em que são utilizados os serviços com estado.

Por que ter microserviços imponentes juntamente com os apátridas? As duas principais razões são:

* Pode construir serviços de processamento de transações online (OLTP) de alta alta qualidade, de baixa latência e tolerante sinuoso, mantendo o código e os dados próximos da mesma máquina. Alguns exemplos são montras interativas, pesquisa, sistemas internet of things (IoT), sistemas de negociação, sistemas de processamento de cartões de crédito e sistemas de deteção de fraudes, e gestão de registos pessoais.
* Pode simplificar o design da aplicação. Os microserviços estatais eliminam a necessidade de filas e caches adicionais, tradicionalmente necessários para responder aos requisitos de disponibilidade e latência de uma aplicação puramente apátrida. Os serviços estatais são naturalmente de alta disponibilidade e baixa latência, o que reduz o número de peças móveis para gerir na sua aplicação como um todo.

## <a name="supported-programming-models"></a>Modelos de programação suportados
O Service Fabric oferece várias formas de escrever e gerir os seus serviços. Os serviços podem utilizar as APIs de Tecido de Serviço para tirar o máximo partido das funcionalidades e quadros de aplicação da plataforma. Os serviços também podem ser qualquer programa executável compilado escrito em qualquer idioma e hospedado num cluster de Tecido de Serviço. Para mais informações, consulte modelos de [programação suportados.](service-fabric-choose-framework.md)

### <a name="containers"></a>Contentores
Por padrão, o Tecido de Serviço implanta e ativa os serviços como processos. Serviço Tecido também pode implementar serviços em [contentores](service-fabric-containers-overview.md). Importante, pode misturar serviços em processos e serviços em contentores na mesma aplicação. O Service Fabric suporta a implementação de contentores Linux e windows no Windows Server 2016. Pode implementar aplicações existentes, serviços apátridas ou serviços audais em contentores. 

### <a name="reliable-services"></a>Reliable Services
[A Reliable Services](service-fabric-reliable-services-introduction.md) é um quadro leve para serviços de escrita que se integram com a plataforma Service Fabric e beneficiam de todo o conjunto de funcionalidades da plataforma. Os Serviços Fiáveis podem ser apátridas (semelhantes à maioria das plataformas de serviços, como servidores web ou funções de trabalhador em Serviços Azure Cloud), onde o estado é persistido numa solução externa, como o Azure DB ou o Azure Table Storage. Os Serviços Fiáveis também podem ser audacionais, onde o estado é persistido diretamente no próprio serviço usando Coleções Fiáveis. O Estado é [altamente disponibilizado](service-fabric-availability-services.md) através da replicação e distribuído através de [divisórias,](service-fabric-concepts-partitioning.md)tudo gerido automaticamente pela Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Construído em cima de Serviços Fiáveis, o quadro [de Ator Fiável](service-fabric-reliable-actors-introduction.md) é um quadro de aplicação que implementa o padrão de Ator Virtual, baseado no padrão de design de ator. O quadro do Ator Fiável usa unidades independentes de computação e estado com execução de um fio único chamado atores. O quadro do Ator Fiável fornece uma comunicação para atores e a persistência do estado pré-definido e configurações de escala para fora.

### <a name="aspnet-core"></a>Núcleo de ASP.NET
Service Fabric [integra-se](service-fabric-reliable-services-communication-aspnetcore.md) com ASP.NET Core como um modelo de programação de primeira classe para a construção de aplicações web e API.  ASP.NET Core pode ser usado de duas maneiras diferentes em Tecido de Serviço:

- Hospedado como um hóspede executável. Isto é usado principalmente para executar aplicações ASP.NET Core existentes no Tecido de Serviço sem alterações de código.
- Corra dentro de um serviço de confiança. Isto permite uma melhor integração com o tempo de execução do Tecido de Serviço e permite serviços de ASP.NET Core.

### <a name="guest-executables"></a>Executáveis de convidado
Um [hóspede executável](service-fabric-guest-executables-introduction.md) é um executável arbitrário existente (escrito em qualquer idioma) hospedado num cluster de Tecido de Serviço ao lado de outros serviços. Os executáveis dos hóspedes não se integram diretamente com APIs de Tecido de Serviço. No entanto, ainda beneficiam de funcionalidades que a plataforma oferece, como relatórios de saúde personalizados e de reporte de carga e de descoberta de serviços, chamando ASAP SREST. Também têm suporte completo de vida de vida de aplicação. 

## <a name="application-lifecycle"></a>Ciclo de vida da aplicação
Tal como acontece com outras plataformas, uma aplicação no Service Fabric passa geralmente pelas seguintes fases: design, desenvolvimento, teste, implementação, upgrade, manutenção e remoção. O Service Fabric fornece suporte de primeira classe para o ciclo de vida completo das aplicações em nuvem, desde o desenvolvimento até à implantação, gestão diária e manutenção até eventual desmantelamento. O modelo de serviço permite que várias funções diferentes participem de forma independente no ciclo de vida da aplicação. O ciclo de vida da [aplicação service Fabric](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e como são utilizados pelas diferentes funções ao longo das fases do ciclo de vida da aplicação service Fabric. 

Todo o ciclo de vida da aplicação pode ser gerido utilizando [cmdlets PowerShell,](/powershell/module/ServiceFabric/) [comandos CLI,](service-fabric-sfctl.md) [C# APIs,](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) [Java APIs](/java/api/overview/azure/servicefabric)e [REST APIs](/rest/api/servicefabric/). Também pode configurar gasodutos de integração contínua/implantação contínua utilizando ferramentas como [pipelines Azure](service-fabric-set-up-continuous-integration.md) ou [Jenkins.](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## <a name="test-applications-and-services"></a>Testar aplicações e serviços
Para criar serviços verdadeiramente à escala de nuvem, é fundamental verificar se as suas aplicações e serviços podem resistir a falhas no mundo real. O Serviço de Análise de Falhas foi concebido para testar serviços que são construídos em Tecido de Serviço. Com o Serviço de Análise de [Falhas,](service-fabric-testability-overview.md)pode induzir falhas significativas e executar cenários completos de teste contra as suas aplicações. Estas falhas e cenários exercitam e validam os inúmeros estados e transições que um serviço irá experimentar ao longo da sua vida, tudo de forma controlada, segura e consistente.

[As ações](service-fabric-testability-actions.md) visam um serviço para testes utilizando falhas individuais. Um desenvolvedor de serviços pode usá-los como blocos de construção para escrever cenários complicados. Exemplos de falhas simuladas são:

* Reinicie um nó para simular qualquer número de situações em que uma máquina ou VM é reiniciada.
* Mova uma réplica do seu serviço imponente para simular o equilíbrio de carga, a falha ou a atualização da aplicação.
* Invocar a perda de quórum num serviço imponente para criar uma situação em que as operações de escrita não podem prosseguir porque não há réplicas suficientes de "back-up" ou "secundário" para aceitar novos dados.
* Invoque a perda de dados num serviço imponente para criar uma situação em que todo o estado de memória é completamente eliminado.

[Os cenários](service-fabric-testability-scenarios.md) são operações complexas compostas por uma ou mais ações. O Serviço de Análise de Falhas fornece dois cenários completos embutidos:

* [Cenário](service-fabric-controlled-chaos.md)de caos - simula falhas contínuas e intercaladas (graciosas e ingraciosas) em todo o cluster durante longos períodos de tempo.
* [Cenário de failover](service-fabric-testability-scenarios.md#failover-test)- uma versão do cenário de teste do caos que visa uma partilha específica de serviço, deixando outros serviços inalterados.

## <a name="clusters"></a>Clusters
Um [cluster de tecido](service-fabric-deploy-anywhere.md) de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Os clusters podem ser dimensionados para milhares de máquinas. Uma máquina ou VM que faz parte de um cluster é chamado de nó de cluster. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como as propriedades de colocação. Cada máquina ou VM tem um `FabricHost.exe`serviço de arranque automático, que começa a funcionar sobre o arranque e, em seguida, inicia dois executáveis: Fabric.exe e FabricGateway.exe. Estes dois executáveis compõem o nó. Para cenários de teste, pode hospedar vários nós numa única máquina `Fabric.exe` `FabricGateway.exe`ou VM executando várias instâncias de e .

Os clusters de tecido de serviço podem ser criados em máquinas virtuais ou físicas que executam o Windows Server ou o Linux. É possível implementar e executar aplicações de Tecido de Serviço em qualquer ambiente onde tenha um conjunto de computadores Windows Server ou Linux que estejam interligados: no local, no Microsoft Azure ou em qualquer fornecedor de nuvem.

### <a name="clusters-on-azure"></a>Clusters no Azure
Os clusters de tecido sinuoso em Azure proporcionam integração com outras funcionalidades e serviços do Azure, o que torna as operações e a gestão do cluster mais fáceis e fiáveis. Um cluster é um recurso do Gestor de Recursos Azure, para que possa modelar clusters como qualquer outro recurso em Azure. O Gestor de Recursos também fornece uma gestão fácil de todos os recursos utilizados pelo cluster como uma única unidade. Os clusters em Azure estão integrados com diagnósticos Azure e registos do Monitor Azure. Os tipos de nó cluster são [conjuntos de escala](/azure/virtual-machine-scale-sets/index)de máquina virtual, por isso a funcionalidade de autoscalcificação é incorporada.

Você pode criar um cluster em Azure através do [portal Azure](service-fabric-cluster-creation-via-portal.md), a partir de um [modelo](service-fabric-cluster-creation-via-arm.md), ou do [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

O Service Fabric no Linux permite-lhe construir, implementar e gerir aplicações altamente disponíveis e altamente escaláveis no Linux, tal como faria no Windows. As estruturas de Tecido de Serviço (Serviços Fiáveis e Atores Fiáveis) estão disponíveis em Java no Linux, além de C# (.NET Core). Também pode construir [serviços executáveis de hóspedes](service-fabric-guest-executables-introduction.md) com qualquer idioma ou enquadramento. A orquestração de contentores Docker também é apoiada. Os recipientes Docker podem executar executáveis de hóspedes ou serviços nativo sinuosos de Serviço Fabric, que utilizam as estruturas de Tecido de Serviço. Para mais informações, leia sobre [o Serviço Fabric no Linux](service-fabric-deploy-anywhere.md).

Existem algumas funcionalidades que são suportadas no Windows, mas não no Linux. Para saber mais, leia [Diferenças entre tecido de serviço no Linux e Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Clusters autónomos
O Service Fabric fornece um pacote de instalação para que possa criar clusters de tecido de serviço autónomo no local ou em qualquer fornecedor de nuvem. Aglomerados autónomos dão-lhe a liberdade de acolher um aglomerado onde quiser. Se os seus dados estiverem sujeitos a restrições de conformidade ou regulação, ou se pretender manter os seus dados locais, pode alojar o seu próprio cluster e aplicações. As aplicações service Fabric podem ser executadas em vários ambientes de hospedagem sem alterações, pelo que o seu conhecimento de aplicações de construção passa de um ambiente de hospedagem para outro. 

[Crie o primeiro cluster autónomo do Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Os aglomerados autónomos de Linux ainda não são apoiados.

### <a name="cluster-security"></a>Segurança do cluster
Os clusters devem ser protegidos para evitar que os utilizadores não autorizados se conectem ao seu cluster, especialmente quando tem cargas de trabalho de produção a funcionar nele. Embora seja possível criar um cluster não seguro, fazê-lo permite que utilizadores anónimos se conectem a ele se os pontos finais de gestão estiverem expostos à internet pública. Não é possível permitir posteriormente a segurança num cluster não seguro: a segurança do cluster é ativada no tempo de criação do cluster.

Os cenários de segurança do cluster são:
* Segurança nó-ao-nó
* Segurança cliente-a-nó
* Controlo de acesso baseado em funções (RBAC)

Para mais informações, leia [Secure a cluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Dimensionamento
Se adicionar novos nós ao cluster, o Tecido de Serviço reequilibra as réplicas e instâncias de partição em todo o número aumentado de nós. O desempenho global da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Service Fabric reequilibra novamente as réplicas e instâncias da partição através do número reduzido de nós para fazer melhor uso do hardware em cada nó. Pode escalar aglomerados em Azure [manualmente](service-fabric-cluster-scale-in-out.md) ou [programáticamente](service-fabric-cluster-programmatic-scaling.md). Os clusters autónomos podem ser dimensionados [manualmente](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Upgrades de cluster
Periodicamente, são lançadas novas versões do tempo de execução do Tecido de Serviço. Execute o tempo de execução, ou tecido, upgrades no seu cluster para que esteja sempre a executar uma [versão suportada](service-fabric-support.md). Além de atualizações de tecidos, também pode atualizar a configuração do cluster, como certificados ou portas de aplicação.

Um cluster de Tecido de Serviço é um recurso que possui, mas é parcialmente gerido pela Microsoft. A Microsoft é responsável por corrigir o SISTEMA subjacente e realizar atualizações de tecido no seu cluster. Pode configurar o seu cluster para receber atualizações automáticas de tecido, quando a Microsoft lançar uma nova versão, ou optar por selecionar uma versão de tecido suportado que deseja. As atualizações de tecidos e configurações podem ser definidas através do portal Azure ou através do Gestor de Recursos. Para mais informações, leia [Atualizar um cluster de Tecido de Serviço](service-fabric-cluster-upgrade.md). 

Um aglomerado autónomo é um recurso que tu és inteiramente dono. É responsável por remendar os sistemas operativos subjacentes e dar início a atualizações de tecidos. Se o seu [https://www.microsoft.com/download](https://www.microsoft.com/download)cluster se pode ligar a, pode configurar o seu cluster para descarregar e fornecer automaticamente o novo pacote de tempo de funcionação do Tecido de Serviço. Em seguida, iniciaria a atualização. Se o seu cluster [https://www.microsoft.com/download](https://www.microsoft.com/download)não conseguir aceder, pode descarregar manualmente o novo pacote de tempo de funcionamento a partir de uma máquina ligada à Internet e, em seguida, iniciar a atualização. Para mais informações, leia [Atualizar um cluster de Tecido de Serviço autónomo](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorização do estado de funcionamento
Service Fabric introduz um modelo de [saúde](service-fabric-health-introduction.md) concebido para sinalizar condições de cluster e aplicação pouco saudáveis em entidades específicas (tais como nós de cluster e réplicas de serviço). O modelo de saúde utiliza repórteres de saúde (componentes do sistema e cães de guarda). O objetivo é fácil e rápido de diagnóstico e reparação. Os escritores de serviços precisam de pensar frontalmente sobre saúde e como conceber relatórios de [saúde.](service-fabric-report-health.md#design-health-reporting) Qualquer condição que possa ter impacto na saúde deve ser reportada, especialmente se puder ajudar problemas de sinalização perto da raiz. As informações de saúde podem economizar tempo e esforço na depuração e investigação uma vez que o serviço está em funcionamento em escala na produção.

Os repórteres do Serviço Fabric monitorizam as condições de interesse identificadas. Relatam essas condições com base na sua visão local. A loja de [saúde](service-fabric-health-introduction.md#health-store) agrega dados de saúde enviados por todos os repórteres para determinar se as entidades são globalmente saudáveis. O modelo destina-se a ser rico, flexível e fácil de usar. A qualidade dos relatórios de saúde determina a exatidão da visão de saúde do cluster. Falsos positivos que mostram erradamente problemas insalubres podem impactar negativamente upgrades ou outros serviços que usam dados de saúde. Exemplos desses serviços são serviços de reparação e mecanismos de alerta. Por conseguinte, é necessário pensar em fornecer relatórios que captem as condições de interesse da melhor forma possível.

A comunicação pode ser feita a partir de:
* A réplica ou instância do serviço de serviço de serviço de serviço monitorizado.
* Cães de guarda internos implantados como um serviço de tecido de serviço (por exemplo, um serviço apátrida service Fabric que monitoriza as condições e os relatórios de problemas). Os cães de guarda podem ser implantados em todos os nós ou podem ser afinados com o serviço monitorizado.
* Cães de guarda internos que funcionam nos nódosos de Tecido de Serviço mas não são implementados como serviços de Fabricação de Serviço.
* Cães de guarda externos que sondam o recurso de fora do cluster Service Fabric (por exemplo, serviço de monitorização como o Gomez).

Fora da caixa, os componentes do Tecido de Serviço reportam saúde a todas as entidades do cluster. [Os relatórios de saúde](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) do sistema proporcionam visibilidade na funcionalidade de cluster e aplicação e problemas de bandeira através da saúde. Para aplicações e serviços, os relatórios de saúde do sistema verificam que as entidades são implementadas e estão a comportar-se corretamente na perspetiva do tempo de funcionamento do Tecido de Serviço. Os relatórios não fornecem qualquer monitorização da saúde da lógica empresarial do serviço ou detetam processos que tenham deixado de responder. Para adicionar informações de saúde específicas à lógica do seu serviço, [implemente relatórios de saúde personalizados](service-fabric-report-health.md) nos seus serviços.

O Service Fabric fornece múltiplas formas de [ver relatórios](service-fabric-view-entities-aggregated-health.md) de saúde agregados na loja de saúde:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização.
* Consultas de saúde (através [de PowerShell,](/powershell/module/ServiceFabric/) [CLI,](service-fabric-sfctl.md) [C# FabricClient APIs](/dotnet/api/system.fabric.fabricclient.healthclient) e [Java FabricClient APIs](/java/api/system.fabric), ou [REST APIs](/rest/api/servicefabric)).
* Consultas gerais que devolvem uma lista de entidades que têm saúde como uma das propriedades (através de PowerShell, CLI, APIs ou REST).

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico
[A monitorização e diagnóstico](service-fabric-diagnostics-overview.md) são fundamentais para o desenvolvimento, teste e implantação de aplicações e serviços em qualquer ambiente. As soluções service Fabric funcionam melhor quando planeia e implementa monitorização e diagnósticos que ajudam a garantir que as aplicações e serviços funcionam como esperado num ambiente de desenvolvimento local ou em produção.

Os principais objetivos da monitorização e diagnóstico são:

- Detetar e diagnosticar problemas de hardware e infraestrutura
- Detete imediar problemas de software e aplicativos, reduza o tempo de inatividade do serviço
- Compreender o consumo de recursos e ajudar a impulsionar as decisões de operações
- Otimizar o desempenho da aplicação, serviço e infraestrutura
- Gerar insights de negócio e identificar áreas de melhoria
 
O fluxo global de trabalho da monitorização e diagnóstico consiste em três etapas:

1. Geração de eventos: isto inclui eventos (registos, vestígios, eventos personalizados) na infraestrutura (cluster), plataforma e nível de aplicação/serviço
2. Agregação de eventos: eventos gerados precisam de ser recolhidos e agregados antes de poderem ser exibidos
3. Análise: os eventos precisam de ser visualizados e acessíveis em algum formato, para permitir a análise e exibição conforme necessário

Vários produtos estão disponíveis que cobrem estas três áreas, e você é livre de escolher diferentes tecnologias para cada um. Para mais informações, leia [Monitorização e diagnósticos para tecido](service-fabric-diagnostics-overview.md)de serviço Azure .

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autónomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Experimente criar um serviço com os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como [migrar dos Serviços cloud.](service-fabric-cloud-services-migration-differences.md)
* Aprenda a [monitorizar e diagnosticar serviços.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) 
* Aprenda a [testar as suas apps e serviços.](service-fabric-testability-overview.md)
* Aprenda a [gerir e orquestrar recursos](service-fabric-cluster-resource-manager-introduction.md)de cluster.
* Veja as amostras de [Tecido de Serviço.](https://aka.ms/servicefabricsamples)
* Saiba mais sobre as opções de suporte do [Tecido de Serviço.](service-fabric-support.md)
* Leia o blog da [equipa](https://blogs.msdn.microsoft.com/azureservicefabric/) para artigos e anúncios.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
