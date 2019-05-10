---
title: Introdução ao microsserviços no Azure | Documentos da Microsoft
description: Uma visão geral de por que criar aplicações na cloud com uma abordagem de microsserviços é importante no desenvolvimento de aplicativos modernos e como o Azure Service Fabric fornece uma plataforma para atingir esse objetivo.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: atsenthi
ms.openlocfilehash: feb82d2abb756d636aeb77042cc817b7b05f6b0c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233676"
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Por que um microsserviços aborda a criação de aplicações?

Como os desenvolvedores de software, a pensar sobre o cálculo de um aplicativo em partes de componente é nada de novo. Normalmente, uma abordagem em camadas é feita com um armazenamento de back-end, a lógica de negócios de camada intermediária e uma interface de front-end de utilizador (IU). O que *tem* alterado ao longo dos últimos anos é que estamos, como desenvolvedores, a criar aplicações distribuídas para a cloud.

As necessidades comerciais variáveis são:

* Um serviço que é criado e operado em escala para alcançar clientes em novas regiões geográficas.
* Entrega mais rápida de funcionalidades e capacidades para poder responder às necessidades dos clientes de forma ágil.
* Utilização de recursos aperfeiçoado para reduzir os custos.

Essas necessidades de negócios estão a afetar *como* criamos aplicativos.

Para obter mais informações sobre a abordagem do Azure para os microsserviços, leia [Microsserviços: Uma revolução de aplicação com a tecnologia de cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolíticos vs. a abordagem de design de microsserviços

Aplicativos evoluam ao longo do tempo. Aplicativos bem-sucedidos evoluem pelo facto de ser úteis para pessoas. Aplicativos sem êxito não evoluir e, eventualmente, são preteridos. A pergunta é: Quanto sabe sobre os requisitos de hoje e quais serão eles no futuro? Por exemplo, digamos que está criando um aplicativo de geração de relatórios para um departamento. Tem a certeza de que o aplicativo só se aplica no âmbito da sua empresa e que os relatórios são curta duração. À sua escolha da abordagem é diferente de, digamos, criando um serviço que disponibiliza conteúdos de vídeo para dezenas de milhões de clientes.

Às vezes, a obter algo para trás como prova de conceito é o fator de condução, sabendo que o aplicativo pode ser projetado novamente mais tarde. Há muito sentido em excesso de engenharia algo que nunca é usado. Por outro lado, quando as empresas falam sobre como criar para a nuvem, a expectativa é de crescimento e utilização. O problema é que o crescimento e dimensionamento são imprevisíveis. Gostaríamos de ser capaz de protótipo rapidamente também sabendo que estamos num caminho que possa lidar com sucesso no futuro. Essa é a abordagem de "lean startup": compilar, medir, aprender e iterar.

Durante a era do cliente-servidor, tendemos de se concentrar na criação de aplicativos em camadas através de tecnologias específicas em cada escalão. O termo *monolítica* aplicação surgiu para essas abordagens. As interfaces tendiam a ser entre as camadas e um design mais rigidamente acoplado foi utilizado entre os componentes em cada camada. Os desenvolvedores projetado e decomposto classes que foram compilados em bibliotecas e vinculados juntos em alguns executáveis e DLLs.

Há benefícios para uma abordagem de design monolítico. Muitas vezes, é mais simples de design e tem mais rápidas chamadas entre os componentes como essas chamadas são, muitas vezes, ao longo do (IPC interprocess communication). Além disso, todas as pessoas testa um único produto, o que tende a ser mais pessoas-recurso eficiente. A desvantagem é que há uma grande União entre camadas em camadas, e não é possível dimensionar a componentes individuais. Se precisar de efetuar as correções ou atualizações, terá de aguardar por outras pessoas ao fim dos testes. É mais difícil de ser ágil.

Microsserviços resolver essas desvantagens e muito mais alinham com os requisitos de negócios anterior, mas também têm vantagens e passivos. As vantagens dos microsserviços são a que cada um deles encapsula, normalmente, a funcionalidade de negócios mais simples, que pode aumentar ou reduzir verticalmente, testar, implementar e gerir de forma independente. Uma vantagem importante de uma abordagem de microsserviços é que as equipes são mais ligadas a cenários de negócios que pela tecnologia. Na prática, equipas mais pequenas desenvolver um microsserviço com base num cenário de cliente e utilizam qualquer tecnologia que escolher.

Em outras palavras, a organização não precisa de padronizar tech para manter as aplicações de microsserviços. Equipas individuais que os próprios serviços podem fazer o que lhes faça sentido com base nos conhecimentos de equipe ou o que é mais adequado para resolver o problema. Na prática, um conjunto de tecnologias recomendadas, como um determinado NoSQL armazenar ou estrutura de aplicativo da web, é preferível.

A desvantagem dos microsserviços é fornecido no gerenciamento do aumento do número de entidades separadas e lidar com as implementações mais complexas e controle de versão. Tráfego de rede entre os microsserviços aumenta, bem como as latências de rede correspondente. Muitos serviços chatty e granulares são uma receita para um pesadelo de desempenho. Sem as ferramentas para ajudar a ver estas dependências, é difícil "ver" todo o sistema.

Padrões de tornam a abordagem de microsserviços profissional concordar sobre como se comunicar e ao ser tolerância a falhas de apenas as informações necessárias de um serviço, em vez de rígidas contratos. É importante definir esses contratos com antecedência no design, porque os serviços de atualização independentemente uns dos outros. Descrição de outro inventada para desenvolver com uma abordagem de microsserviços é "refinado service-oriented architecture (SOA)."

***Em sua forma mais simples, a abordagem de design de microsserviços trata de uma federação desacoplada de serviços, com alterações independentes para cada um e acordado as normas para a comunicação.***

Que são produzidas de mais aplicações em cloud, as pessoas tenham detetados que este decomposição da aplicação global para os serviços independentes, voltada para o cenário é uma abordagem melhor a longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre as abordagens de desenvolvimento de aplicativos

![Desenvolvimento de aplicativos de plataforma do Service Fabric][Image1]

1) Uma aplicação monolítica contém funcionalidades específicas do domínio e normalmente é dividida por camadas funcionais, como web, a empresa e dados.

2) Dimensionar uma aplicação monolítica através da clonagem-lo em várias máquinas virtuais/servidores/contentores.

3) Uma aplicação de microsserviços separa a funcionalidade em serviços separados do menores.

4) As escalas de abordagem de microsserviços ao implementar cada serviço de forma independente, criação de instâncias desses serviços em máquinas virtuais/servidores/contentores.

Conceber com um microsserviço abordagem não é uma solução completa para todos os projetos, mas alinhar mais de perto com os objetivos de negócio descritos anteriormente. Começando com uma abordagem monolítica pode ser aceitável se souber que terá a oportunidade de Refazer o código mais tarde num design de microsserviços. Em geral, começar com uma aplicação monolítica e lentamente dividi-lo em fases, começando com as áreas funcionais que precisam ser mais escalonável ou ágil.

A abordagem de microsserviços significa que compõem a sua aplicação de vários serviços pequenos. Estes serviços são executados em contentores que são implementadas em clusters de máquinas. Equipas mais pequenas desenvolver um serviço que se concentra num cenário e testar a forma independente, versão, implementarem e dimensionar cada serviço, para que todo o aplicativo pode evoluir.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?

Existem definições diferentes de microsserviços. No entanto, a maioria das seguintes características dos microsserviços é amplamente aceitas:

* Encapsula um cenário de negócio ou do cliente. O que é o problema que está a resolver?
* Desenvolvido por uma pequena equipa de engenharia.
* Escritos em qualquer linguagem de programação e utilizar qualquer estrutura.
* Consistem em código e (opcionalmente) o estado, que estão com versões independentes, implementados e dimensionados.
* Interagir com os outros microsserviços através de interfaces bem definidos e protocolos.
* Têm nomes exclusivos (URLs) utilizados para resolver a respetiva localização.
* Permanece consistente e disponíveis na presença de falhas.

Em resumo:

***Aplicações de Microsserviços são compostas de pequenos com versões independentes serviços e dimensionáveis com foco no cliente que se comunicam entre si através de protocolos padrão com interfaces bem definidos.***

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Escritos em qualquer linguagem de programação e utilizar qualquer estrutura

Como desenvolvedores, queremos ser livres para escolher uma linguagem ou arquitetura que queremos, consoante as nossas habilidades ou as necessidades do serviço. Em alguns serviços, poderá valor os benefícios de desempenho do C++ acima de tudo mais. Outros serviços, a facilidade de desenvolvimento gerenciado em c# ou Java pode ser mais importante. Em alguns casos, terá de utilizar uma biblioteca de parceiros específicos, a tecnologia de armazenamento de dados ou o meio de expor o serviço para clientes.

Depois de optar por uma tecnologia, que são fornecidos para a gestão operacional ou o ciclo de vida e o dimensionamento do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e o estado ser com versões independentes, implementados e dimensionados

Não importa o que escolhe para escrever os microsserviços, o código e, opcionalmente, o estado, deve independentemente implementar, atualizar e dimensionar. Este é um dos problemas mais difícil porque trata-se à sua escolha de tecnologias. Para compreensão de dimensionamento, como partição (ou fragmentar) o código e o estado é um desafio. Quando o código e o estado de usam tecnologias separadas, o que é comum hoje em dia, os scripts de implantação para os microsserviços tem de ser capaz de dimensionar ambos. Isso também é sobre agilidade e flexibilidade, que pode atualizar algumas dos microsserviços sem ter de atualizar todos eles ao mesmo tempo.

Retornando monolíticos versus a abordagem de microsserviços para por um momento, o diagrama seguinte mostra as diferenças na abordagem para armazenar o estado.

#### <a name="state-storage-between-application-styles"></a>Armazenamento de estado entre os estilos de aplicação

![Armazenamento de estado de plataforma do Service Fabric][Image2]

***Uma abordagem monolítica no lado esquerdo tem uma base de dados e camadas de tecnologias específicas.***

***A abordagem de microsserviços no lado direito tem um gráfico de microsserviços interconectados em que estado normalmente tem um âmbito para os microsserviços e várias tecnologias são utilizadas.***

Numa abordagem monolítica, o aplicativo usa normalmente uma base de dados. A vantagem é que é um local único, o que torna mais fácil de implementar. Cada componente pode ter uma única tabela para armazenar seu estado. As equipes precisam estritamente separar o estado, que é um desafio. Inevitavelmente existem temptations para adicionar uma nova coluna a uma tabela de cliente existente, realize uma associação entre as tabelas e criar dependências na camada de armazenamento. Depois de tal acontecer, não é possível dimensionar componentes individuais.

Na abordagem de microsserviços, cada serviço gerencia e armazena seu próprio Estado. Cada serviço é responsável por dimensionar o código e o estado em conjunto para satisfazer as necessidades do serviço. Uma desvantagem é que quando for necessário para criar vistas ou consultas, de dados da sua aplicação, terá de consultar em vários armazenamentos de estado. Normalmente, isso é resolvido fazendo com que um microsserviço separado que cria uma vista numa coleção de microsserviços. Se precisar de efetuar várias consultas improvisadas nos dados, cada microsserviço deve considerar a escrita de seus dados para um serviço de armazenamento de dados para análise offline.

Microsserviços têm versões e é possível que as versões diferentes de um microsserviço poderão estar em execução lado a lado. Uma versão mais recente de um microsserviço pode falhar durante a atualização e tem de ser revertido para uma versão anterior. Também é útil para testes A/B-estilo, em que diferentes usuários experiência versões diferentes do serviço de controle de versão. Por exemplo, é comum para atualizar um microsserviço de um conjunto específico de clientes para testar a nova funcionalidade antes de distribuí-lo mais amplamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microsserviços através de interfaces bem definidos e protocolos

Extensa literatura sobre arquitetura orientada a serviço tiver sido publicada nos últimos 10 anos, que descreve padrões de comunicação. Em geral, comunicação de serviço utiliza uma abordagem REST com protocolos HTTP e TCP e o XML ou JSON como formato de serialização. Da perspectiva de interface, trata-se adotando a abordagem de design da web. Mas nada o impede de utilizar protocolos binários ou de seus formatos de dados. Esteja preparado para as pessoas têm um tempo mais difícil com os microsserviços se estes protocolos e formatos não estão abertos.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome exclusivo utilizado para resolver a localização (URL)

Os microsserviços tem de ser endereçável onde quer que está a ser executado. Se está pensando em máquinas e qual delas está em execução um microsserviço particular, as coisas correm ruins rapidamente.

Da mesma forma que o DNS resolve um URL específico a um determinado computador, os microsserviços tem um nome exclusivo para que a sua localização atual seja detectável. Microsserviços tem nomes endereçáveis que são independentes da infraestrutura que são executadas. Isso implica que existe uma interação entre a forma como o seu serviço é implementado e como é detetado, porque deve haver um registo de serviço. Quando ocorre uma falha numa máquina, o serviço de registo tem indicam onde o serviço foi movido.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de resolver, especialmente num sistema distribuído. Boa parte do código que escrevemos como desenvolvedores está a processar exceções, e isso também é onde mais tempo é gasto no teste. O problema é mais envolvido que escrever código para lidar com falhas. O que acontece quando ocorre uma falha de máquina em que está a executar os microsserviços? Não só é necessário detetar a falha (um problema difícil no seu próprio), mas também tem de reiniciar seus microsserviços.

Por motivos de disponibilidade, um microsserviço deve ser resiliente a falhas e conseguir reiniciar em outra máquina. Além do código em execução a ser resiliente, não deve haver qualquer perda de dados e os dados têm de permanecer consistente.

A resiliência é difícil de materializar quando as falhas ocorrem durante uma atualização da aplicação. Os microsserviços, trabalhar com o sistema de implementação, não precisam de recuperar. Ele precisa decidir se ele pode continuar a seguir em frente para a versão mais recente ou em vez disso, reverter para uma versão anterior para manter um estado consistente. Perguntas como se os computadores suficientes estão disponíveis para que seguir em frente e como recuperar versões anteriores dos microsserviços precisam ser consideradas. Isto requer que os microsserviços para emitir as informações de estado de funcionamento para tomar essas decisões.

### <a name="reports-health-and-diagnostics"></a>Estado de funcionamento de relatórios e diagnósticos

Pode parecer óbvio e são muitas vezes ignorada, mas um microsserviço deve comunicar o estado de funcionamento e diagnóstico. Caso contrário, não há pouco insight para seu estado de funcionamento de uma perspectiva de operações. Correlacionar eventos de diagnóstico num conjunto de serviços independentes e lidar com os desvios de relógio de máquina dar sentido a ordem dos eventos, é um desafio. Da mesma forma que interage com um microsserviço sobre acordado protocolos e formatos de dados, terá de padronizar como iniciar o estado de funcionamento e eventos de diagnóstico que em última análise, acabará num arquivo de eventos para consultar e visualizar. Numa abordagem de microsserviços, ele tem chave que diferentes equipes chegarem a acordo num formato de registo único. Deve haver uma abordagem consistente para visualização de eventos de diagnóstico do aplicativo como um todo.

Estado de funcionamento é diferente do diagnóstico. Estado de funcionamento é sobre os microsserviços reporting seu estado atual para executar as ações apropriadas. Um bom exemplo está trabalhando com mecanismos de atualização e implantação para manter a disponibilidade. Embora um serviço pode estar danificado devido a uma falha de processo ou reinicialização do computador, o serviço poderá ainda estar operacional. É a última coisa que precisa facilitar o pior executar uma atualização. A melhor abordagem é fazer primeiro uma investigação ou aguarde algum tempo para os microsserviços recuperar. Eventos de estado de funcionamento de um microsserviço ajudam-na tomar decisões informadas e, em vigor, ajudar a criar serviços de recuperação.

## <a name="microservices-design-guidance-on-azure"></a>Orientações de conceção de Microsserviços no Azure
Visite o Centro de arquitetura do Azure para obter orientações de design no [a criação de microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)

## <a name="service-fabric-as-a-microservices-platform"></a>Recursos de infraestrutura do serviço como uma plataforma de microsserviços

O Azure Service Fabric surgiu quando Microsoft transitou do fornecimento de produtos emoldurados, que eram normalmente monolíticos em estilo, ao fornecimento de serviços. A experiência de construção e operar serviços grandes, como o Azure SQL Database e o Azure Cosmos DB, em forma de Service Fabric. A plataforma se desenvolveram ao longo do tempo à medida que os serviços de cada vez mais o ADOTARAM. Service Fabric tinha a execução não apenas no Azure, mas também nas implantações do Windows Server autónomo.

***O objetivo do Service Fabric é resolver os problemas difíceis de criar e executar um serviço e utilizar recursos de infraestrutura de forma eficiente, para que as equipes podem resolver problemas de negócios usando uma abordagem de microsserviços.***

Service Fabric ajuda a criar aplicativos que usam uma abordagem de microsserviços, fornecendo:

* Uma plataforma que fornece serviços do sistema para implementar, atualizar, detetar e reinicie os serviços com falha, descobrir serviços, encaminhar mensagens, gerir o estado e monitorizar o estado de funcionamento.
* Capacidade de implementar aplicações de qualquer execução nos contentores ou processos. O Service Fabric é um contentor e um orquestrador de processo.
* Produtivas as APIs de programação, para ajudar a criar aplicações como microsserviços: [ASP.NET Core, dos Reliable Actors e serviços fiáveis](service-fabric-choose-framework.md). Por exemplo, pode obter informações de estado de funcionamento e diagnóstico, ou pode tirar partido da elevada disponibilidade incorporada.

***O Service Fabric é agnóstico em como criar o seu serviço, e pode usar qualquer tecnologia. No entanto, ele fornece APIs de programação internas que facilitam a criação de microsserviços.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrando aplicativos existentes para o Service Fabric

Service Fabric permite-lhe reutilizar o código existente, o que, em seguida, pode ser modernizado com novos microsserviços. Existem cinco fases para a modernização de aplicações, e pode iniciar e parar qualquer uma das etapas. Nomeadamente:

1) Começar com uma aplicação monolítica tradicional.  
2) Levantar e deslocar - utilizar contentores ou executáveis de convidado para alojar o código existente no Service Fabric.  
3) Modernização - novos microsserviços adicionados em conjunto com o código em contentores existente.  
4) Inove - quebrar o monolítica em microsserviços puramente com base na necessidade.  
5) Transformado em microsserviços - a transformação dos aplicativos monolíticos de existente ou criar novas aplicações de ambiente intacto.

![Migração para os Microsserviços][Image3]

É importante enfatizar novamente que o utilizador pode **iniciar e parar qualquer um desses estágios**. Não são forçada para avançar para o próximo estágio. Agora vejamos exemplos para cada uma destas fases.

**Migração lift- and -Shift**  
Um grande número de empresas é elevação e está mudando aplicativos monolíticos existentes em contentores por dois motivos:

* Redução de custos devido a consolidação e a remoção dos aplicativos existentes de hardware ou de execução na densidade superior.
* Contrato de implementação consistente entre desenvolvimento e operações.

Reduções de custos são compreensíveis e dentro da Microsoft, um grande número de aplicativos existentes está a ser em contentores simplesmente para poupar milhões de dólares. Implantação consistente é mais difícil avaliar, mas igualmente importante. Isso significa que os desenvolvedores podem ainda ser livres para escolher a tecnologia ideal para eles, no entanto, as operações irão aceitar apenas uma única maneira de implementar e gerir estas aplicações. Minimiza as operações de ter que lidar com a complexidade de várias tecnologias diferentes ou forçar os desenvolvedores a escolha apenas determinadas aplicações. Essencialmente, cada aplicativo estiver em contentor nas imagens de implantação autônomo.

Muitas organizações parar por aqui. Que já têm os benefícios de contentores e o Service Fabric fornece a experiência de gestão completa de implementação, atualizações, controle de versão, reversões, etc de monitorização de estado de funcionamento.

**Modernização**  
A adição de novos serviços em conjunto com existente em contentores de código. Se pretender escrever novo código, é melhor optar por adotar pequenas etapas para baixo o caminho de microsserviços. Isto pode estar adicionando um novo ponto final de REST API ou a nova lógica de negócios. Dessa forma, começar a jornada de criar novos microsserviços e prática de desenvolvimento e implantá-los.

**Inove**  
Uma abordagem de microsserviços permite a necessidades empresariais em evolução. Nesta fase, a decisão é se precisa para começar a dividir a aplicação monolítica em serviços ou a inovar. Aqui um exemplo clássico é quando uma base de dados a ser utilizado como uma fila de fluxo de trabalho se tornar um afunilamento de processamento. Como o número de fluxo de trabalho solicitações aumenta, o trabalho tem de ser distribuídos para dimensionamento. Para esse componente específico do aplicativo que não é escalonável, ou que precisam de ser atualizada com mais frequência, dividir isso num microsserviço e inove.

**Transformado em microsserviços**  
Esta funcionalidade é onde a aplicação é totalmente composto (ou dividir em) microsserviços. Para chegar aqui, efetuar a viagem de microsserviços. Pode começar aqui, mas para fazer isso sem um microsserviços plataforma para o ajudar a é um investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>São os microsserviços diretamente para a minha aplicação?

Talvez. O que podemos teve foi que, como cada vez mais equipas da Microsoft começaram a criar para a cloud por razões de negócio, muitos deles perceberam os benefícios de usar uma abordagem de microsserviços semelhantes. Bing, por exemplo, desenvolve através dos microsserviços há anos. Para outras equipes, a abordagem de microsserviços era nova. As equipes que lá localizadas eram problemas difíceis de resolver fora suas áreas de núcleo de força. Este é o motivo pelo qual o Service Fabric obtidas força como a tecnologia à escolha para a criação de serviços.

O objetivo do Service Fabric é reduzir as complexidades de criar aplicações de microsserviços, para que não tem de passar por tantos reformulações dispendiosas. Comece por algo pequeno, dimensione quando necessário, preterir serviços, adicionar novos e evoluem com a utilização do cliente. Também Sabemos que existem muitos outros problemas de ser resolvidos para que os microsserviços mais acessível para a maioria dos desenvolvedores. Contentores e o modelo de programação de ator são exemplos de pequenas etapas nessa direção, e estamos-se de que as inovações mais surgirão para facilitar o processo.


## <a name="next-steps"></a>Passos Seguintes

* [Microsserviços: Uma revolução de aplicação com a tecnologia de cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centro de arquitetura do Azure: Criar microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Melhores práticas de aplicação do Service Fabric do Azure e de cluster](service-fabric-best-practices-overview.md)
* [Descrição geral da terminologia do Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
