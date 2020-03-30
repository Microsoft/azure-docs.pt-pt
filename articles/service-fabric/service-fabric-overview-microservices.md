---
title: Introdução aos microserviços no Azure
description: Uma visão geral do porquê de construir aplicações em nuvem com uma abordagem de microserviços é importante para o desenvolvimento de aplicações modernas e como o Azure Service Fabric fornece uma plataforma para o conseguir.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750628"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Por que usar uma abordagem de microserviços para construir aplicações

Para os desenvolvedores de software, considerar uma aplicação em componentes não é novidade. Tipicamente, uma abordagem hierárquica é usada, com uma loja de back-end, lógica de negócio de nível médio, e uma interface de utilizador frontal (UI). O que *mudou* nos últimos anos é que os desenvolvedores estão a construir aplicações distribuídas para a nuvem.

Aqui estão algumas necessidades de negócio em mudança:

* Um serviço que foi construído e operado à escala para chegar aos clientes em novas regiões geográficas.
* Uma entrega mais rápida de funcionalidades e capacidades para responder às exigências dos clientes de forma ágil.
* Melhor utilização de recursos para reduzir custos.

Estas necessidades de negócio estão a afetar *a forma como* construímos aplicações.

Para obter mais informações sobre a abordagem Azure aos microserviços, consulte [Microservices: Uma revolução](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)de aplicações alimentada pela nuvem .

## <a name="monolithic-vs-microservices-design-approach"></a>Abordagem monolítica vs. microserviços

As aplicações evoluem ao longo do tempo. As aplicações bem sucedidas evoluem sendo úteis às pessoas. As aplicações mal sucedidas não evoluem e acabam por ser depreciadas. Eis a questão: o que sabe sobre os seus requisitos hoje e o que serão no futuro? Por exemplo, digamos que está a construir um pedido de reporte para um departamento na sua empresa. Tem a certeza que o pedido se aplica apenas no âmbito da sua empresa e que os relatórios não serão mantidos por muito tempo. A sua abordagem será diferente da de, digamos, construir um serviço que entregue conteúdo sonoro a dezenas de milhões de clientes.

Às vezes, tirar algo pela porta como prova de conceito é o fator de condução. Sabe que a aplicação pode ser redesenhada mais tarde. Há pouco sentido em engenharia excessiva algo que nunca é usado. Por outro lado, quando as empresas constroem para a nuvem, a expectativa é o crescimento e o uso. Crescimento e escala são imprevisíveis. Queremos protótipo rapidamente, sabendo também que estamos num caminho que pode lidar com o sucesso futuro. Esta é a abordagem de arranque magra: construir, medir, aprender e iterar.

Durante a era cliente/servidor, tendemos a focar-nos na construção de aplicações hierárquias, utilizando tecnologias específicas em cada nível. O termo aplicação *monolítica* surgiu para descrever estas abordagens. As interfaces tendiam a estar entre os níveis, e um design mais apertado foi usado entre componentes dentro de cada nível. Os desenvolvedores conceberam e consideraram classes que foram compiladas em bibliotecas e ligadas em alguns ficheiros executáveis e DLLs.

Há benefícios para uma abordagem monolítica de design. As aplicações monolíticas são muitas vezes mais simples de conceber, e as chamadas entre componentes são mais rápidas porque estas chamadas são muitas vezes sobre comunicação interprocess (IPC). Além disso, todos testam um único produto, que tende a ser uma utilização mais eficiente dos recursos humanos. A desvantagem é que há um acoplamento apertado entre camadas hierárquica, e não se pode escalar componentes individuais. Se precisar de fazer correções ou upgrades, tem de esperar que outros terminem os testes. É mais difícil ser ágil.

Os microserviços abordam estas desvantagens e alinham-se mais estreitamente com os requisitos comerciais anteriores. Mas também têm benefícios e passivos. Os benefícios dos microserviços são que cada um normalmente encapsula a funcionalidade de negócio mais simples, que pode escalar para cima ou para baixo, testar, implementar e gerir de forma independente. Um benefício importante de uma abordagem de microserviços é que as equipas são mais impulsionadas por cenários de negócio do que pela tecnologia. As equipas mais pequenas desenvolvem um microserviço com base num cenário de cliente e utilizam todas as tecnologias que queiram utilizar.

Por outras palavras, a organização não precisa de normalizar a tecnologia para manter aplicações de microserviços. As equipas individuais que possuem serviços podem fazer o que lhes faz sentido com base na experiência da equipa ou no que é mais adequado para resolver o problema. Na prática, um conjunto de tecnologias recomendadas, como uma determinada loja NoSQL ou quadro de aplicação web, é preferível.

A desvantagem dos microserviços é que você tem que gerir entidades mais separadas e lidar com implementações e versões mais complexas. O tráfego de rede entre os microserviços aumenta, assim como as correspondentes tardios da rede. Muitos serviços chatos e granulares podem causar um pesadelo de desempenho. Sem ferramentas para ajudá-lo a ver estas dependências, é difícil ver todo o sistema.

As normas fazem com que a abordagem dos microserviços funcione especificando como comunicar e tolerar apenas as coisas que precisa de um serviço, em vez de contratos rígidos. É importante definir estes contratos na frente no design porque os serviços atualizam independentemente uns dos outros. Outra descrição cunhada para conceber com uma abordagem de microserviços é "arquitetura orientada para o serviço de grãos finos (SOA)".

***Na sua mais simples, a abordagem de design de microserviços é sobre uma federação de serviços dissociada, com alterações independentes a cada um e normas acordadas para a comunicação.***

À medida que mais aplicações em nuvem são produzidas, as pessoas descobriram que esta decomposição da aplicação global em serviços independentes e focados em cenários é uma melhor abordagem a longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicações

![Desenvolvimento de aplicações da plataforma Service Fabric][Image1]

1) Uma aplicação monolítica contém funcionalidade específica de domínio e é normalmente dividida em camadas funcionais como web, negócios e dados.

2) Escala-se uma aplicação monolítica clonando-a em vários servidores/máquinas/contentores virtuais.

3) Uma aplicação de microserviço separa a funcionalidade em serviços menores separados.

4) A abordagem dos microserviços esescala-se implantando cada serviço de forma independente, criando instâncias destes serviços através de servidores/máquinas/contentores virtuais.

Conceber com uma abordagem de microserviços não é apropriado para todos os projetos, mas alinha-se mais estreitamente com os objetivos de negócio descritos anteriormente. Começar com uma abordagem monolítica pode fazer sentido se souber que terá a oportunidade de refazer o código mais tarde num design de microserviços. Mais comummente, começa-se com uma aplicação monolítica e separa-se lentamente por etapas, começando pelas áreas funcionais que precisam de ser mais escaláveis ou ágeis.

Quando utiliza uma abordagem de microserviços, compõe a sua aplicação de muitos pequenos serviços. Estes serviços funcionam em contentores que são implantados através de um conjunto de máquinas. As equipas mais pequenas desenvolvem um serviço que se foca num cenário e teste independentemente, versão, implantação e escala de cada serviço para que toda a aplicação possa evoluir.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?

Existem diferentes definições de microserviços. Mas a maioria destas características dos microserviços são amplamente aceites:

* Encapsular um cenário de cliente ou negócio. Que problema estás a resolver?
* Desenvolvido por uma pequena equipa de engenharia.
* Escrito em qualquer linguagem de programação, usando qualquer enquadramento.
* Consistem em código, e opcionalmente estado, ambos são versão independente, implantado e dimensionado.
* Interaja com outros microserviços sobre interfaces e protocolos bem definidos.
* Tenha nomes únicos (URLs) que são usados para resolver a sua localização.
* Mantenha-se consistente e disponível na presença de falhas.

Resumindo:

***As aplicações de microserviço são compostas por pequenos serviços de versão independente e escalável do cliente que comunicam entre si através de protocolos padrão com interfaces bem definidas.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Escrito em qualquer linguagem de programação, utilizando qualquer quadro

Como desenvolvedores, queremos ser livres para escolher uma língua ou enquadramento, dependendo das nossas competências e necessidades do serviço que estamos a criar. Para alguns serviços, pode valorizar os benefícios de desempenho de C++ acima de qualquer outra coisa. Para outros, a facilidade de desenvolvimento gerido que obtém de C# ou Java pode ser mais importante. Em alguns casos, poderá ser necessário utilizar uma biblioteca específica de parceiros, tecnologia de armazenamento de dados ou método para expor o serviço aos clientes.

Depois de escolher uma tecnologia, tem de considerar a gestão operacional ou do ciclo de vida e a escalação do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que código e estado sejam versão, implantada e dimensionada independentemente

Não importa como escreva os seus microserviços, o código, e opcionalmente o Estado, deve implementar, atualizar e escalar de forma independente. Este problema é difícil de resolver porque se resume à sua escolha de tecnologias. Para escalar, compreender como dividir (ou fragmento) tanto o código como o estado é um desafio. Quando o código e o estado usam diferentes tecnologias, o que é comum hoje em dia, os scripts de implementação para o seu microserviço precisam ser capazes de escalar as duas. Esta separação também tem a ver com agilidade e flexibilidade, para que possa atualizar alguns dos microserviços sem ter de atualizar todos eles de uma só vez.

Voltemos à nossa comparação das abordagens monolíticas e microserviços por um momento. Este diagrama mostra as diferenças nas abordagens para armazenar o estado:

#### <a name="state-storage-for-the-two-approaches"></a>Armazenamento do Estado para as duas abordagens

![Armazenamento estatal da plataforma de tecido de serviço][Image2]

***A abordagem monolítica, à esquerda, tem uma única base de dados e níveis de tecnologias específicas.***

***A abordagem dos microserviços, à direita, tem um gráfico de microserviços interligados onde o Estado é normalmente abordado para o microserviço e são utilizadas várias tecnologias.***

Numa abordagem monolítica, a aplicação normalmente utiliza uma única base de dados. A vantagem de usar uma base de dados é que está num único local, o que facilita a implementação. Cada componente pode ter uma única mesa para armazenar o seu estado. As equipas precisam de separar estritamente o estado, o que é um desafio. Inevitavelmente, alguém será tentado a adicionar uma coluna a uma mesa de clientes existente, fazer uma união entre mesas e criar dependências na camada de armazenamento. Depois disto acontecer, não se pode escalar componentes individuais.

Na abordagem dos microserviços, cada serviço gere e armazena o seu próprio estado. Cada serviço é responsável pela escala de código e estado em conjunto para satisfazer as exigências do serviço. Uma desvantagem é que quando precisa de criar pontos de vista, ou consultas, dos dados da sua aplicação, precisa de consultar várias lojas estatais. Este problema é normalmente resolvido por um microserviço separado que constrói uma vista através de uma coleção de microserviços. Se precisar de executar múltiplas consultas improvisadas nos dados, deve considerar escrever os dados de cada microserviço a um serviço de armazenamento de dados para análise offline.

Os microserviços são versionados. É possível que diferentes versões de um microserviço corram lado a lado. Uma versão mais recente de um microserviço pode falhar durante uma atualização e precisa de ser relançada para uma versão anterior. A versão também é útil para testes A/B, onde diferentes utilizadores experimentam diferentes versões do serviço. Por exemplo, é comum atualizar um microserviço para um conjunto específico de clientes para testar novas funcionalidades antes de a lançar mais amplamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microserviços sobre interfaces e protocolos bem definidos

Ao longo dos últimos 10 anos, foram publicadas extensas informações descrevendo padrões de comunicação em arquiteturas orientadas para o serviço. Geralmente, a comunicação de serviço utiliza uma abordagem REST com protocolos HTTP e TCP e XML ou JSON como formato de serialização. Do ponto de vista da interface, trata-se de ter uma abordagem de web design. Mas nada deve impedi-lo de usar protocolos binários ou os seus próprios formatos de dados. Basta estar ciente de que as pessoas terão mais dificuldade em usar os seus microserviços se estes protocolos e formatos não estiverem abertamente disponíveis.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome único (URL) usado para resolver a sua localização

O seu microserviço tem de ser endereçado onde quer que esteja a funcionar. Se estás a pensar em máquinas e qual deles está a executar um microserviço específico, as coisas podem correr mal rapidamente.

Da mesma forma que o DNS resolve um URL específico para uma determinada máquina, o seu microserviço precisa de um nome único para que a sua localização atual seja detetável. Os microserviços precisam de nomes endereçados que sejam independentes da infraestrutura em que estão a funcionar. Isto implica que há uma interação entre a forma como o seu serviço é implementado e como é descoberto, porque tem de haver um registo de serviço. Quando uma máquina falha, o serviço de registo precisa de lhe dizer para onde o serviço foi transferido.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Mantém-se consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de resolver, especialmente num sistema distribuído. Grande parte do código que escrevemos como desenvolvedores é para lidar com exceções. Durante os testes, também passamos mais tempo em tratamento de exceções. O processo está mais envolvido do que escrever código para lidar com falhas. O que acontece quando a máquina em que o microserviço está a funcionar falha? Tens de detetar a falha, que é um problema difícil por si só. Mas também precisa reiniciar o seu microserviço.

Para disponibilidade, um microserviço precisa de ser resiliente a falhas e capaz de reiniciar outra máquina. Além destes requisitos de resiliência, os dados não devem ser perdidos, e os dados precisam de se manter consistentes.

A resiliência é difícil de alcançar quando as falhas acontecem durante uma atualização da aplicação. O microserviço, trabalhando com o sistema de implantação, não precisa de se recuperar. Tem de determinar se pode continuar a avançar para a versão mais recente ou voltar para uma versão anterior para manter um estado consistente. Você precisa considerar algumas questões, como se máquinas suficientes estão disponíveis para continuar a avançar e como recuperar versões anteriores do microserviço. Para tomar estas decisões, precisa do microserviço para emitir informações de saúde.

### <a name="reports-health-and-diagnostics"></a>Relatórios de saúde e diagnósticos

Pode parecer óbvio, e muitas vezes é negligenciado, mas um microserviço precisa reportar a sua saúde e diagnósticos. Caso contrário, tem pouca visão sobre a sua saúde do ponto de vista das operações. Correlacionar eventos de diagnóstico através de um conjunto de serviços independentes, e lidar com os desvios do relógio da máquina para dar sentido à ordem do evento, é um desafio. Da mesma forma que interage com um microserviço sobre protocolos e formatos de dados acordados, é necessário normalizar como registar eventos de saúde e diagnóstico que acabarão por acabar numa loja de eventos para consulta e visualização. Com uma abordagem de microserviços, diferentes equipas precisam de chegar a acordo sobre um único formato de exploração madeireira. Tem de haver uma abordagem consistente para ver os eventos de diagnóstico no conjunto da aplicação.

A saúde é diferente dos diagnósticos. A saúde tem a ver com o microserviço que reporta o seu estado atual para tomar as medidas adequadas. Um bom exemplo é trabalhar com mecanismos de atualização e implementação para manter a disponibilidade. Embora um serviço possa estar atualmente pouco saudável devido a uma falha de processo ou a um reboot de máquina, o serviço ainda pode estar operacional. A última coisa que precisas é de piorar a situação, iniciando uma atualização. A melhor abordagem é investigar primeiro ou dar tempo para o microserviço recuperar. Os eventos de saúde de um microserviço ajudam-nos a tomar decisões informadas e, de facto, ajudam a criar serviços de auto-cura.

## <a name="guidance-for-designing-microservices-on-azure"></a>Orientação para a conceção de microserviços no Azure

Visite o centro de arquitetura Azure para obter orientações sobre conceção e construção de [microserviços no Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

## <a name="service-fabric-as-a-microservices-platform"></a>Tecido de Serviço como uma plataforma de microserviços

O Azure Service Fabric surgiu quando a Microsoft passou da entrega de produtos encaixotados, que eram tipicamente monolíticos, para a prestação de serviços. A experiência de construção e operação de grandes serviços, como a Azure SQL Database e a Azure Cosmos DB, em forma de Tecido de Serviço. A plataforma evoluiu ao longo do tempo à medida que mais serviços a adotaram. O Service Fabric teve de funcionar não só no Azure, mas também em implementações autónomas do Windows Server.

***O objetivo do Service Fabric é resolver os problemas difíceis de construir e executar um serviço e utilizar os recursos de infraestrutura de forma eficiente, para que as equipas possam resolver problemas de negócio utilizando uma abordagem de microserviços.***

O Service Fabric ajuda-o a construir aplicações que utilizam uma abordagem de microserviços, fornecendo:

* Uma plataforma que presta serviços de sistema para implementar, atualizar, detetar e reiniciar serviços falhados, descobrir serviços, direcionar mensagens, gerir o Estado e monitorizar a saúde.
* A capacidade de implementar aplicações em contentores ou como processos. O Tecido de Serviço é um recipiente e orquestrador de processos.
* APIs de programação produtiva para ajudá-lo a construir aplicações como microserviços: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Por exemplo, você pode obter informações de saúde e diagnóstico, ou você pode aproveitar a alta disponibilidade incorporada.

***O Service Fabric é agnóstico sobre como constrói o seu serviço, e pode usar qualquer tecnologia. Mas fornece APIs de programação incorporados que facilitam a construção de microserviços.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrar aplicações existentes para o Tecido de Serviço

O Service Fabric permite-lhe reutilizar o código existente e modernizá-lo com novos microserviços. Há cinco fases para a modernização da aplicação, e pode começar e parar em qualquer fase. As fases são:

1) Comece com uma aplicação monolítica tradicional.  
2) Migrar. Utilize recipientes ou executáveis de hóspedes para alojar o código existente no Tecido de Serviço.  
3) Modernizar. Adicione novos microserviços ao lado do código contentorizado existente.  
4) Inovar. Quebre a aplicação monolítica em microserviços com base nas necessidades.  
5) Transformar aplicações em microserviços. Transforme as aplicações monolíticas existentes ou construa novas aplicações de greenfield.

![Migração para microserviços][Image3]

Lembre-se, pode *começar e parar em qualquer uma destas fases.* Não tens de progredir para a próxima fase. 

Vejamos exemplos para cada uma destas fases.

**Migrar**  
Por duas razões, muitas empresas estão a migrar aplicações monolíticas existentes para contentores:

* Redução de custos, quer devido à consolidação e remoção do hardware existente, quer devido a aplicações de execução com maior densidade.
* Um contrato de implantação consistente entre o desenvolvimento e as operações.

As reduções de custos são simples. Na Microsoft, muitas aplicações existentes estão a ser contentorizadas, levando a milhões de dólares em poupanças. Uma implementação consistente é mais difícil de avaliar, mas igualmente importante. Significa que os desenvolvedores podem escolher as tecnologias que lhes convém, mas as operações aceitarão apenas um único método para implementar e gerir as aplicações. Alivia as operações de terem de lidar com a complexidade do apoio a diferentes tecnologias sem obrigar os desenvolvedores a escolher apenas certas. Essencialmente, cada aplicação é contentorizada em imagens de implantação autónomas.

Muitas organizações param aqui. Eles já têm os benefícios dos contentores, e o Service Fabric fornece a experiência completa de gestão, incluindo implementação, upgrades, versão, retrocessos e monitorização da saúde.

**Modernizar**  
A modernização é a adição de novos serviços ao lado do código contentorizado existente. Se vai escrever um novo código, é melhor dar pequenos passos pelo caminho dos microserviços. Isto pode significar a adição de um novo ponto final rest API ou uma nova lógica de negócio. Desta forma, inicia-se o processo de construção de novos microserviços e práticas de desenvolvimento e implantação dos mesmos.

**Inove**  
Uma abordagem de microserviços acomoda as necessidades de negócio em mudança. Nesta fase, você precisa decidir se deve começar a dividir a aplicação monolítica em serviços, ou inovar. Um exemplo clássico aqui é quando uma base de dados que você está usando como uma fila de fluxo de trabalho torna-se um estrangulamento de processamento. À medida que o número de pedidos de fluxo de trabalho aumenta, o trabalho tem de ser distribuído por escala. Pegue nessa peça particular da aplicação que não está a escalonar, ou que precisa de ser atualizada com mais frequência, e divida-a como um microserviço e inovar.

**Transformar aplicações em microserviços**  
Nesta fase, a sua aplicação é totalmente composta por (ou divididos) por microserviços. Para chegar a este ponto, fez a viagem de microserviços. Pode começar aqui, mas fazê-lo sem uma plataforma de microserviços para ajudá-lo requer um investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>Os microserviços estão certos para a minha candidatura?

Talvez sim. Na Microsoft, à medida que mais equipas começaram a construir para a nuvem por razões de negócio, muitas delas perceberam os benefícios de adotar uma abordagem semelhante a um microserviço. Bing, por exemplo, usa microserviços há anos. Para outras equipas, a abordagem dos microserviços era nova. As equipas descobriram que havia problemas difíceis de resolver fora das suas áreas centrais de força. É por isso que o Service Fabric ganhou tração como tecnologia para serviços de construção.

O objetivo do Service Fabric é reduzir as complexidades da construção de aplicações de microserviços para que não tenha de passar por tantas remodelações dispendiosas. Comece com pequena escala quando necessário, deprece os serviços, adicione novos e evolua com o uso do cliente. Sabemos também que há muitos outros problemas ainda por resolver para tornar os microserviços mais acessíveis para a maioria dos desenvolvedores. Os contentores e o modelo de programação do ator são exemplos de pequenos passos nesse sentido. Estamos certos de que mais inovações surgirão para facilitar uma abordagem de microserviços.

## <a name="next-steps"></a>Passos seguintes

* [Microserviços: Uma revolução de aplicações alimentada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centro de Arquitetura Azure: Construção de microserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Aplicação azure service fabric e boas práticas de cluster](service-fabric-best-practices-overview.md)
* [Visão geral da terminologia do tecido de serviço](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
