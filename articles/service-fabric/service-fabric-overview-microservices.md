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
ms.date: 06/18/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcb52165c7cae18b807eff03c80b51eae8e2717
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204794"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Por que usar uma abordagem de microsserviços para criar aplicações?

Para desenvolvedores de software, cálculo de um aplicativo em partes de componente é nada de novo. Normalmente, uma abordagem em camadas é utilizada, com um armazenamento de back-end, a lógica de negócios de camada intermediária e uma interface de front-end de utilizador (IU). O que *tem* alterado ao longo dos últimos anos é que os desenvolvedores Criando aplicativos distribuídos para a nuvem.

Aqui estão algumas das necessidades de negócio:

* Um serviço que tenha criado e operado em escala para alcançar clientes em novas regiões geográficas.
* Entrega mais rápida de funcionalidades e capacidades para responder às necessidades dos clientes de forma ágil.
* Utilização de recursos aperfeiçoado para reduzir os custos.

Essas necessidades de negócios estão a afetar *como* criamos aplicativos.

Para obter mais informações sobre a abordagem do Azure para os microsserviços, veja [Microsserviços: Uma revolução de aplicação com a tecnologia de cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolíticos vs. a abordagem de design de microsserviços

Aplicativos evoluam ao longo do tempo. Aplicativos bem-sucedidos evoluem pelo facto de ser úteis para pessoas. Aplicativos sem êxito não evoluam e, eventualmente, são preteridos. Eis a questão: quanto sabe sobre os requisitos de hoje e o que eles serão no futuro? Por exemplo, digamos que estiver criando um aplicativo de geração de relatórios para um departamento na sua empresa. Tem a certeza, a aplicação aplica-se apenas no âmbito da sua empresa e que os relatórios não seja mantidos longo. Sua abordagem deverá ser diferente de, digamos, criando um serviço que disponibiliza conteúdos de vídeo para dezenas de milhões de clientes.

Às vezes, a obter algo para trás como uma prova de conceito é o fator de condução. Sabe que o aplicativo pode ser projetado novamente mais tarde. Há muito sentido em excesso de engenharia algo que nunca é usado. Por outro lado, quando criar empresas para a nuvem, a expectativa é de crescimento e utilização. Crescimento e dimensionamento são imprevisíveis. Queremos protótipo rapidamente também sabendo que estamos num caminho que possa lidar com sucesso no futuro. Essa é a abordagem de "lean startup": compilar, medir, aprender e iterar.

Durante a era do cliente/servidor, tendemos de se concentrar na criação de aplicativos em camadas através de tecnologias específicas em cada escalão. O termo *monolítica* aplicação surgiu para descrever essas abordagens. As interfaces tendiam a ser entre as camadas e um design mais rigidamente acoplado foi utilizado entre os componentes em cada camada. Os desenvolvedores projetado e decomposto classes que foram compilados em bibliotecas e vinculados juntos em alguns arquivos executáveis e DLLs.

Há benefícios para uma abordagem monolítica de design. Aplicativos monolíticos, muitas vezes, são mais simples de design e chamadas entre os componentes são mais rápidas do que uma vez que essas chamadas são, muitas vezes, ao longo do (IPC interprocess communication). Além disso, todas as pessoas testa um único produto, o que tende a ser uma utilização mais eficiente de recursos humanos. A desvantagem é que há uma grande União entre camadas em camadas, e não é possível dimensionar a componentes individuais. Se precisar de fazer correções ou atualizações, terá de aguardar por outras pessoas ao fim dos testes. É mais difícil de ser ágil.

Essas desvantagens de endereços de Microsserviços e muito mais alinham com os requisitos de negócios anterior. Mas também têm vantagens e passivos. As vantagens dos microsserviços são a que cada um deles encapsula, normalmente, a funcionalidade de negócios mais simples, que pode aumentar ou reduzir verticalmente, testar, implementar e gerir de forma independente. Uma vantagem importante de uma abordagem de microsserviços é que as equipes são mais ligadas a cenários de negócios que pela tecnologia. Equipas mais pequenas desenvolver um microsserviço com base num cenário de cliente e utilizam qualquer tecnologia que pretende utilizar.

Em outras palavras, a organização não precisa de padronizar tech para manter as aplicações de microsserviços. Equipas individuais que os próprios serviços podem fazer o que lhes faça sentido com base nos conhecimentos de equipe ou o que é mais adequado para resolver o problema. Na prática, um conjunto de recomendado tecnologias, como um arquivo de NoSQL específico ou estrutura de aplicativo da web, é preferível.

A desvantagem dos microsserviços é que tem de gerir entidades mais separadas e lidar com implementações mais complexas e de controlo de versões. Tráfego de rede entre os microsserviços aumenta, tal como as latências de rede correspondente. Muitos serviços chatty e granulares podem causar um pesadelo de desempenho. Sem ferramentas que ajudam a visualizar estas dependências, é difícil ver todo o sistema.

Padrões de tornam a abordagem de microsserviços profissional especificando como se comunicar e tolerating apenas as informações necessárias de um serviço, em vez de rígidas contratos. É importante definir esses contratos com antecedência no design, porque os serviços de atualização independentemente uns dos outros. Descrição de outro inventada para desenvolver com uma abordagem de microsserviços é "refinado service-oriented architecture (SOA)."

***Em sua forma mais simples, a abordagem de design de microsserviços trata de uma federação desacoplada de serviços, com alterações independentes para cada um e acordado padrões de comunicação.***

Que são produzidas de mais aplicações na cloud, as pessoas tenham detetados que este decomposição do aplicativo geral para os serviços independentes, voltada para o cenário é uma abordagem melhor a longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre as abordagens de desenvolvimento de aplicativos

![Desenvolvimento de aplicativos de plataforma do Service Fabric][Image1]

1) Uma aplicação monolítica contém funcionalidades específicas do domínio e normalmente é dividida em camadas funcionais, como web, a empresa e dados.

2) Dimensionar uma aplicação monolítica através da clonagem-lo em várias máquinas virtuais/servidores/contentores.

3) Uma aplicação de microsserviços separa a funcionalidade em serviços separados do menores.

4) As escalas de abordagem de microsserviços ao implementar cada serviço de forma independente, criação de instâncias desses serviços em máquinas virtuais/servidores/contentores.

Conceber com um microsserviços abordagem não é adequada para todos os projetos, mas alinhar mais de perto com os objetivos de negócio descritos anteriormente. Começando com uma abordagem monolítica, poderá fazer sentido se sabe, que terá a oportunidade de Refazer o código mais tarde num design de microsserviços. Em geral, começar com uma aplicação monolítica e lentamente dividi-lo em fases, começando com as áreas funcionais que precisam ser mais escalonável ou ágil.

Quando utilizar uma abordagem de microsserviços, que é a compor a aplicação de vários serviços pequenos. Estes serviços são executados em contentores que são implementadas em clusters de máquinas. Equipas mais pequenas desenvolver um serviço que se concentra num cenário e testar a forma independente, versão, implementarem e dimensionar cada serviço, para que todo o aplicativo pode evoluir.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?

Existem definições diferentes de microsserviços. Mas a maioria dessas características dos microsserviços é amplamente aceitas:

* Encapsula um cenário de negócio ou do cliente. Que problema estão resolvendo?
* Desenvolvido por uma pequena equipa de engenharia.
* Escritas em qualquer linguagem de programação, através de qualquer arquitetura.
* Consistem em código, e, opcionalmente, estado, sendo que ambas são com versões independentes, implantados e dimensionada.
* Interagir com os outros microsserviços através de interfaces bem definidos e protocolos.
* Têm nomes exclusivos (URLs) que são utilizados para resolver a respetiva localização.
* Permanece consistente e disponíveis na presença de falhas.

Para concluir que:

***Aplicações de Microsserviços são compostas de pequenos com versões independentes serviços e dimensionáveis com foco no cliente que se comunicam entre si através de protocolos padrão com interfaces bem definidos.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Escritas em qualquer linguagem de programação, através de qualquer arquitetura

Como desenvolvedores, queremos ser livres para escolher uma linguagem ou estrutura, dependendo das nossas habilidades e as necessidades do serviço que estamos criando. Para alguns serviços, poderá valorizar os benefícios de desempenho do C++ acima a qualquer outra coisa. Para outros, a facilidade de desenvolvimento gerenciado que a partir do C# ou Java pode ser mais importante. Em alguns casos, poderá ter de utilizar uma biblioteca de parceiros específicos, a tecnologia de armazenamento de dados ou o método para expor o serviço para clientes.

Depois de escolher uma tecnologia, precisa considerar o operacionais ou de ciclo de vida de gestão e dimensionamento do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e o estado ser com versões independentes, implementados e dimensionados

Não importa como escreve seus microsserviços, o código e, opcionalmente, o estado, deve independentemente implementar, atualizar e dimensionar. Esse problema é difícil porque trata-se à sua escolha de tecnologias. Para compreensão de dimensionamento, como partição (ou fragmentar) o código e o estado é um desafio. Quando o código e o estado de usam tecnologias diferentes, o que é comum hoje em dia, os scripts de implantação para os microsserviços tem de ser capaz de dimensionar ambos. Essa separação é também sobre agilidade e flexibilidade, que pode atualizar algumas dos microsserviços sem ter de atualizar todos eles ao mesmo tempo.

Vamos voltar ao nosso comparação das abordagens monolíticas e microsserviços por um momento. Este diagrama mostra as diferenças de abordagens para armazenar o estado:

#### <a name="state-storage-for-the-two-approaches"></a>Armazenamento de estado para as duas abordagens

![Armazenamento de estado de plataforma do Service Fabric][Image2]

***Uma abordagem monolítica, à esquerda, tem uma base de dados e camadas de tecnologias específicas.***

***A abordagem de microsserviços, à direita, tem um gráfico de microsserviços interconectados em que estado normalmente tem um âmbito para os microsserviços e várias tecnologias são utilizadas.***

Numa abordagem monolítica, o aplicativo usa normalmente uma base de dados. A vantagem de utilizar uma base de dados é que está a ser um único local, o que torna mais fácil de implementar. Cada componente pode ter uma única tabela para armazenar seu estado. As equipes precisam estritamente separar o estado, que é um desafio. Inevitavelmente, alguém será tentado a adicionar uma coluna para uma tabela de cliente existente, realize uma associação entre as tabelas e criar dependências na camada de armazenamento. Depois de tal acontecer, não é possível dimensionar componentes individuais.

Na abordagem de microsserviços, cada serviço gerencia e armazena seu próprio Estado. Cada serviço é responsável por dimensionar o código e o estado em conjunto para satisfazer as necessidades do serviço. Uma desvantagem é que quando precisa para criar vistas ou consultas, de dados da sua aplicação, terá de consultar em vários armazenamentos de estado. Esse problema é normalmente resolvido por um microsserviço separado que cria uma vista numa coleção de microsserviços. Se tiver de executar várias consultas improvisadas nos dados, deve considerar a escrita de dados de cada microsserviço para um serviço de armazenamento de dados para análise offline.

Microsserviços têm versões. É possível para as diferentes versões de um microsserviço para ser executado lado a lado. Uma versão mais recente de um microsserviço pode falhar durante uma atualização e tem de ser revertido para uma versão anterior. Controle de versão também é útil para A / B testes, em que diferentes usuários experiência versões diferentes do serviço. Por exemplo, é comum para atualizar um microsserviço de um conjunto específico de clientes para testar a nova funcionalidade antes de distribuí-lo mais amplamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microsserviços através de interfaces bem definidos e protocolos

Nos últimos 10 anos, informações abrangentes foi publicadas que descreve padrões de comunicação em arquiteturas orientadas a serviços. Em geral, comunicação de serviço utiliza uma abordagem REST com protocolos HTTP e TCP e XML ou JSON como formato de serialização. Da perspectiva de interface, trata-se usar uma abordagem de design da web. Mas nada deve parar de utilizar protocolos binários ou de seus formatos de dados. Apenas Lembre-se de que as pessoas terão vez mais difícil usando os microsserviços se estes protocolos e formatos não estão abertos.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome exclusivo utilizado para resolver a localização (URL)

Os microsserviços tem de ser endereçável onde quer que está a ser executado. Se estiver pensando em máquinas e qual delas está em execução um microsserviço específico, pode correr ruim rapidamente.

Da mesma forma que o DNS resolve um URL específico a um determinado computador, os microsserviços tem um nome exclusivo para que a sua localização atual seja detectável. Microsserviços tem nomes endereçáveis que são independentes da infraestrutura que sejam executadas em. Isso implica que existe uma interação entre a forma como o seu serviço é implementado e como é detetado, porque deve haver um registo de serviço. Quando ocorre uma falha numa máquina, tem de informar a onde o serviço foi movido para o serviço de registo.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de resolver, especialmente num sistema distribuído. Grande parte do código que escrevemos como desenvolvedores é para a manipulação de exceções. Durante o teste, podemos também gastar mais tempo na manipulação de exceção. O processo é mais envolvido que escrever código para lidar com falhas. O que acontece quando a máquina em que os microsserviços está em execução falha? Tem de detetar a falha, o que é um problema difícil por conta própria. Mas também tem de reiniciar seus microsserviços.

Para disponibilidade, um microsserviço deve ser resiliente a falhas e conseguir reiniciar em outra máquina. Além destes requisitos de resiliência, dados não devem ser perdidos e dados têm de permanecer consistente.

A resiliência é difícil de materializar quando as falhas ocorrem durante uma atualização da aplicação. Os microsserviços, trabalhar com o sistema de implementação, não precisam de recuperar. Ele precisa determinar se ele pode continuar a seguir em frente para a versão mais recente ou reverter para uma versão anterior para manter um estado consistente. É necessário considerar algumas questões, como se os computadores suficientes estão disponíveis para que seguir em frente e como recuperar versões anteriores dos microsserviços. Para tomar essas decisões, terá de microsserviços para emitir as informações de estado de funcionamento.

### <a name="reports-health-and-diagnostics"></a>Estado de funcionamento de relatórios e diagnósticos

Pode parecer óbvio e são muitas vezes ignorada, mas um microsserviço tem de comunicar o estado de funcionamento e diagnóstico. Caso contrário, terá um pouco insight para seu estado de funcionamento de uma perspectiva de operações. Correlacionar eventos de diagnóstico num conjunto de serviços independentes e lidar com os desvios de relógio de máquina dar sentido a ordem dos eventos, é um desafio. Da mesma forma que interage com um microsserviço sobre acordado protocolos e formatos de dados, terá de padronizar como iniciar o estado de funcionamento e eventos de diagnóstico que em última análise, acabará num arquivo de eventos para consultar e visualizar. Com uma abordagem de microsserviços, equipas diferentes precisam chegarem a acordo num formato de registo único. Deve haver uma abordagem consistente para visualização de eventos de diagnóstico do aplicativo como um todo.

Estado de funcionamento é diferente do diagnóstico. Estado de funcionamento é sobre os microsserviços reporting seu estado atual para executar as ações apropriadas. Um bom exemplo está trabalhando com mecanismos de atualização e implantação para manter a disponibilidade. Embora um serviço poderá ser danificado devido a uma falha de processo ou reinicialização do computador, o serviço poderá ainda estar operacional. A última coisa que precisa é piorar a situação ao iniciar uma atualização. A melhor abordagem é investigar pela primeira vez ou aguarde algum tempo para os microsserviços recuperar. Eventos de estado de funcionamento de um microsserviço ajudam-na tomar decisões informadas e, em vigor, ajudar a criar serviços de recuperação.

## <a name="guidance-for-designing-microservices-on-azure"></a>Documentação de orientação para estruturar microsserviços no Azure 
Visite o Centro de arquitetura do Azure para obter orientações sobre [estruturação e criação de microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Recursos de infraestrutura do serviço como uma plataforma de microsserviços

O Azure Service Fabric surgiu quando Microsoft transitou do fornecimento de produtos emoldurados, que eram normalmente monolíticos, ao fornecimento de serviços. A experiência de construção e serviços grandes, como o Azure SQL Database e o Azure Cosmos DB, operacionais em forma de Service Fabric. A plataforma se desenvolveram ao longo do tempo à medida que ADOTARAM mais serviços. Service Fabric tinha a execução não apenas no Azure, mas também nas implantações do Windows Server autónomo.

***O objetivo do Service Fabric é para resolver os problemas difíceis de criar e executar um serviço e utilizar recursos de infraestrutura de forma eficiente, para que as equipes podem resolver problemas de negócio com uma abordagem de microsserviços.***

Service Fabric ajuda a criar aplicativos que usam uma abordagem de microsserviços, fornecendo:

* Uma plataforma que fornece serviços do sistema para implementar, atualizar, detetar e reinicie os serviços com falha, descobrir serviços, encaminhar mensagens, gerir o estado e monitorizar o estado de funcionamento.
* A capacidade de implementar aplicações de qualquer execução nos contentores ou processos. O Service Fabric é um contentor e um orquestrador de processo.
* APIs de programação produtivas para o ajudar a criar aplicações como microsserviços: [ASP.NET Core, dos Reliable Actors e serviços fiáveis](service-fabric-choose-framework.md). Por exemplo, pode obter informações de estado de funcionamento e diagnóstico, ou pode tirar partido da elevada disponibilidade incorporada.

***O Service Fabric é agnóstico sobre como criar o seu serviço, e pode usar qualquer tecnologia. Mas ela fornece APIs de programação internas que facilitam a criação de microsserviços.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrando aplicativos existentes para o Service Fabric

Service Fabric permite-lhe reutilizar o código existente e modernizá-lo com os microsserviços de novo. Existem cinco fases para a modernização de aplicações, e pode iniciar e parar em qualquer fase. As fases são:

1) Começar com uma aplicação monolítica tradicional.  
2) Migre. Utilize contentores ou executáveis de convidado para alojar o código existente no Service Fabric.  
3) Modernize. Adicione novos microsserviços em conjunto com o código em contentores existente.  
4) Inove. Quebra a aplicação monolítica em microsserviços com base na necessidade.  
5) Transforme os aplicativos em microsserviços. Transformar aplicativos monolíticos existentes ou criar novas aplicações de ambiente intacto.

![Migração para os microsserviços][Image3]

Lembre-se de que pode *iniciar e parar qualquer um desses estágios*. Não é necessário para o curso para o próximo estágio. 

Vejamos exemplos para cada uma destas fases.

**Migrar**  
Por dois motivos, muitas empresas estão a migrar aplicativos monolíticos existentes em contentores:

* Redução de custos, devido a consolidação e a remoção de hardware existente ou devido a execução de aplicações em densidade superior.
* Um contrato de implementação consistente entre o desenvolvimento e operações.

Reduções de custos são simples. Na Microsoft, muitos aplicativos existentes estão a ser em contentor, que leva a milhões de dólares em economia. Implantação consistente é mais difícil avaliar mas igualmente importante. Isso significa que os desenvolvedores podem escolher as tecnologias que se adequam às-los, mas operações aceitará apenas um único método para implementar e gerir as aplicações. Ele atenua operações da necessidade de lidar com a complexidade do suporte a diferentes tecnologias sem forçar aos desenvolvedores escolher apenas determinados que são. Essencialmente, cada aplicativo estiver em contentor nas imagens de implantação autônomo.

Muitas organizações parar por aqui. Que já têm os benefícios de contentores, e o Service Fabric fornece a experiência de gestão completa, incluindo a implementação, atualizações, controle de versão, reversões e monitorização de estado de funcionamento.

**Modernize**  
Modernização é a adição de novos serviços em conjunto com o código em contentores existente. Se vai escrever código novo, é melhor dê passos pequenos o caminho de microsserviços. Isso pode significar a adição de um novo ponto final de REST API ou a nova lógica de negócios. Dessa forma, inicia o processo de criar novos microsserviços e prática de desenvolvimento e implantá-los.

**Inove**  
Uma abordagem de microsserviços permite a necessidades empresariais em evolução. Nesta fase, precisa decidir se pretende iniciar dividir o aplicativo monolítico em serviços ou a inovar. Aqui um exemplo clássico é quando uma base de dados que está a utilizar como uma fila de fluxo de trabalho se tornar um afunilamento de processamento. Como o número de fluxo de trabalho solicitações aumenta, o trabalho tem de ser distribuídos para dimensionamento. Tire esse componente específico do aplicativo que não é escalonável, ou que precisam de ser atualizada com mais frequência e dividi-lo como um microsserviço e inovar.

**Transformar os aplicativos em microsserviços**  
Nesta fase, a aplicação é totalmente composto (ou dividir em) microsserviços. Para aceder este ponto, fez a jornada de microsserviços. Pode começar aqui, mas fazer isso sem um microsserviços plataforma para o ajudar a requer um investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>São os microsserviços diretamente para a minha aplicação?

Talvez. Na Microsoft, à medida que mais equipas começaram a criar para a cloud por razões de negócio, muitos deles perceberam as vantagens de usar uma abordagem de microsserviços semelhantes. Bing, por exemplo, utiliza o microsserviços há anos. Para outras equipes, a abordagem de microsserviços era nova. As equipes que lá localizadas eram problemas difíceis de resolver fora suas áreas de núcleo de força. Este é o motivo pelo qual o Service Fabric obtidas força como a tecnologia para a criação de serviços.

O objetivo do Service Fabric é reduzir as complexidades de criar aplicações de microsserviços, para que não precisa passar por tantos reformulações dispendiosas. Comece por algo pequeno, dimensione quando necessário, preterir serviços, adicionar novos e evoluem com a utilização do cliente. Também Sabemos que existem muitos outros problemas de ser resolvidos para que os microsserviços mais acessível para a maioria dos desenvolvedores. Contentores e o modelo de programação de ator são exemplos de pequenas etapas nessa direção. Temos a certeza de que as inovações mais surgirão para facilitar a abordagem de microsserviços.


## <a name="next-steps"></a>Passos Seguintes

* [Microsserviços: Uma revolução de aplicação com a tecnologia de cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centro de arquitetura do Azure: Criar microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Melhores práticas de aplicação do Service Fabric do Azure e de cluster](service-fabric-best-practices-overview.md)
* [Descrição geral da terminologia do Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
