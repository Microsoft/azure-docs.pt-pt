---
title: Introdução aos microsserviços no Azure
description: Uma visão geral do porquê de construir aplicações em nuvem com uma abordagem de microserviços é importante para o desenvolvimento moderno de aplicações e como a Azure Service Fabric fornece uma plataforma para o conseguir.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d20e04820c87a7390d9c20e511259ee9860c27f5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575657"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Porquê usar uma abordagem de microserviços para a construção de aplicações

Para os desenvolvedores de software, considerar uma aplicação em peças componentes não é novidade. Tipicamente, uma abordagem hierárquica é usada, com uma loja de back-end, lógica de negócio de nível médio, e uma interface de utilizador frontal (UI). O que *mudou* ao longo dos últimos anos é que os desenvolvedores estão a construir aplicações distribuídas para a nuvem.

Eis algumas necessidades de negócio em mudança:

* Um serviço construído e operado em escala para chegar aos clientes em novas regiões geográficas.
* Entrega mais rápida de funcionalidades e capacidades para responder às exigências dos clientes de forma ágil.
* Melhor utilização dos recursos para reduzir custos.

Estas necessidades empresariais estão a afetar a *forma como* construímos aplicações.

Para obter mais informações sobre a abordagem Azure aos microserviços, consulte [Microservices: Uma revolução de aplicações alimentada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Abordagem monolítica vs. microserviços

As aplicações evoluem ao longo do tempo. As aplicações bem sucedidas evoluem sendo úteis às pessoas. As aplicações mal sucedidas não evoluem e acabam por ser depreciadas. Eis a questão: o que sabe sobre os seus requisitos de hoje e o que serão no futuro? Por exemplo, digamos que está a construir um pedido de reporte para um departamento da sua empresa. Tem a certeza que a aplicação se aplica apenas no âmbito da sua empresa e que os relatórios não serão mantidos por muito tempo. A sua abordagem será diferente da de, digamos, construir um serviço que entrega conteúdos de vídeo a dezenas de milhões de clientes.

Às vezes, tirar algo da porta como prova de conceito é o fator de condução. Sabe que a aplicação pode ser redesenhada mais tarde. Não faz sentido engenharia excessiva algo que nunca é usado. Por outro lado, quando as empresas constroem para a nuvem, a expectativa é o crescimento e o uso. O crescimento e a escala são imprevisíveis. Queremos protótipo rapidamente, sabendo também que estamos num caminho que pode lidar com o sucesso futuro. Esta é a abordagem lean startup: construir, medir, aprender e iterar.

Durante a era cliente/servidor, tendemos a focar-nos na construção de aplicações hierárquicos utilizando tecnologias específicas em cada nível. O termo aplicação *monolítica* surgiu para descrever estas abordagens. As interfaces tendiam a estar entre os níveis, e um design mais apertado foi usado entre componentes dentro de cada nível. Os desenvolvedores conceberam e factored classes que foram compiladas em bibliotecas e ligadas em alguns arquivos e DLLs executáveis.

Há benefícios para uma abordagem monolítica de design. As aplicações monolíticas são muitas vezes mais simples de conceber, e as chamadas entre componentes são mais rápidas porque estas chamadas são muitas vezes sobre comunicação interprocessa (IPC). Além disso, todos testam um único produto, que tende a ser uma utilização mais eficiente dos recursos humanos. A desvantagem é que há um acoplamento apertado entre camadas hierárquicas, e não se pode escalar componentes individuais. Se precisar de fazer correções ou atualizações, tem de esperar que outros terminem os seus testes. É mais difícil ser ágil.

Os microserviços abordam estas desvantagens e alinham-se mais de perto com os requisitos comerciais anteriores. Mas também têm benefícios e passivos. Os benefícios dos microserviços são que cada um normalmente encapsula uma funcionalidade de negócio mais simples, que você pode escalar para fora ou em, testar, implementar e gerir de forma independente. Um benefício importante de uma abordagem de microserviços é que as equipas são mais impulsionadas por cenários de negócio do que pela tecnologia. As equipas mais pequenas desenvolvem um microserviço com base num cenário de cliente e utilizam todas as tecnologias que queiram utilizar.

Por outras palavras, a organização não precisa de uniformizar a tecnologia para manter aplicações de microserviços. As equipas individuais que possuem serviços podem fazer o que faz sentido para eles com base na experiência da equipa ou no que é mais adequado para resolver o problema. Na prática, é preferível um conjunto de tecnologias recomendadas, como uma loja NoSQL em particular ou um quadro de aplicações web.

O lado negativo dos microserviços é que é preciso gerir entidades mais distintas e lidar com implementações e versões mais complexas. O tráfego de rede entre os microserviços aumenta, assim como as latências correspondentes da rede. Muitos serviços de conversação e granular podem causar um pesadelo de desempenho. Sem ferramentas para ajudá-lo a ver estas dependências, é difícil ver todo o sistema.

As normas fazem com que a abordagem dos microserviços funcione especificando como comunicar e tolerar apenas as coisas que precisa de um serviço, em vez de contratos rígidos. É importante definir estes contratos à frente no design porque os serviços atualizam-se independentemente uns dos outros. Outra descrição cunhada para conceber com uma abordagem de microserviços é "arquitetura orientada para o serviço de grãos finos (SOA)."

***Na sua mais simples, a abordagem de conceção de microserviços é sobre uma federação de serviços dissociada, com alterações independentes a cada um e normas acordadas para a comunicação.** _

À medida que mais aplicações em nuvem são produzidas, as pessoas descobriram que esta decomposição da aplicação global em serviços independentes e focados em cenários é uma melhor abordagem a longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicações

![Desenvolvimento de aplicações de plataforma de tecido de serviço][Image1]

1) Uma aplicação monolítica contém funcionalidade específica de domínio e é normalmente dividida em camadas funcionais como web, negócios e dados.

2) Escala-se uma aplicação monolítica clonando-a em vários servidores/máquinas virtuais/contentores.

3) Uma aplicação de microserviço separa a funcionalidade em serviços separados mais pequenos.

4) A abordagem dos microserviços escala-se implantando cada serviço de forma independente, criando instâncias destes serviços através de servidores/máquinas virtuais/contentores.

Desenhar com uma abordagem de microserviços não é apropriado para todos os projetos, mas alinha-se mais com os objetivos de negócio descritos anteriormente. Começar com uma abordagem monolítica pode fazer sentido se souber que terá a oportunidade de reformular o código mais tarde num design de microserviços. Mais comummente, começa-se com uma aplicação monolítica e desfaz-se lentamente por etapas, começando pelas áreas funcionais que precisam de ser mais escaláveis ou ágeis.

Quando utiliza uma abordagem de microserviços, compõe a sua aplicação de muitos pequenos serviços. Estes serviços funcionam em contentores que são implantados através de um conjunto de máquinas. As equipas mais pequenas desenvolvem um serviço que se foca num cenário e teste independente, versão, implementação e escala de cada serviço para que toda a aplicação possa evoluir.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?

Existem diferentes definições de microserviços. Mas a maioria destas características dos microserviços são amplamente aceites:

_ Encapsular um cenário de cliente ou negócio. Que problema está a resolver?
* Desenvolvido por uma pequena equipa de engenharia.
* Escrito em qualquer linguagem de programação, utilizando qualquer enquadramento.
* Consiste em código, e opcionalmente estado, ambos com versão independente, implantados e dimensionados.
* Interaja com outros microserviços sobre interfaces e protocolos bem definidos.
* Têm nomes únicos (URLs) que são usados para resolver a sua localização.
* Mantenha-se consistente e disponível na presença de falhas.

Resumindo:

***As aplicações de microserviços são compostas por pequenos serviços de pequeno, versão independente e escaláveis focados no cliente que comunicam entre si em protocolos padrão com interfaces bem definidas.** _

### <a name="written-in-any-programming-language-using-any-framework"></a>Escrito em qualquer linguagem de programação, utilizando qualquer enquadramento

Como desenvolvedores, queremos ser livres de escolher uma linguagem ou enquadramento, dependendo das nossas competências e das necessidades do serviço que estamos a criar. Para alguns serviços, poderá valorizar os benefícios de desempenho de C++ acima de qualquer outra coisa. Para outros, a facilidade de desenvolvimento gerido que obtém de C# ou Java pode ser mais importante. Em alguns casos, poderá necessitar de utilizar uma biblioteca de parceiros específica, tecnologia de armazenamento de dados ou método para expor o serviço aos clientes.

Depois de escolher uma tecnologia, tem de considerar a gestão operacional ou do ciclo de vida e a escala do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e o estado sejam independentemente versados, implementados e dimensionados

Não importa como escreva os seus microserviços, o código e opcionalmente o estado, deve implementar, atualizar e escalar de forma independente. Este problema é difícil de resolver porque se resume à sua escolha de tecnologias. Para escalonamento, compreender como dividir (ou fragmentos) tanto o código como o estado é desafiante. Quando o código e o estado usam diferentes tecnologias, o que é comum hoje em dia, os scripts de implantação para o seu microserviço precisam de ser capazes de escalar ambos. Esta separação também tem a ver com agilidade e flexibilidade, para que possa atualizar alguns dos microserviços sem ter de atualizar todos de uma só vez.

Vamos voltar à nossa comparação entre o monolítico e os microserviços aproxima-se por um momento. Este diagrama mostra as diferenças nas abordagens para armazenar estado:

#### <a name="state-storage-for-the-two-approaches"></a>Armazenamento do Estado para as duas abordagens

![Armazenamento do estado da plataforma de tecido de serviço][Image2]

_*_A abordagem monolítica, à esquerda, tem uma única base de dados e níveis de tecnologias específicas._*_

_*_A abordagem dos microserviços, à direita, tem um gráfico de microserviços interligados onde o estado é tipicamente alargado ao microserviço e são utilizadas várias tecnologias._*_

Numa abordagem monolítica, a aplicação normalmente utiliza uma única base de dados. A vantagem de usar uma base de dados é que está num único local, o que facilita a sua implantação. Cada componente pode ter uma única tabela para armazenar o seu estado. As equipas precisam de separar estritamente o estado, o que é um desafio. Inevitavelmente, alguém será tentado a adicionar uma coluna a uma mesa de clientes existente, fazer uma junção entre mesas e criar dependências na camada de armazenamento. Depois disto acontecer, não se pode escalar componentes individuais.

Na abordagem dos microserviços, cada serviço gere e armazena o seu próprio estado. Cada serviço é responsável por escalar o código e o estado em conjunto para satisfazer as exigências do serviço. Uma desvantagem é que quando precisa de criar visualizações, ou consultas, dos dados da sua aplicação, precisa de consultar várias lojas do Estado. Este problema é normalmente resolvido por um microserviço separado que constrói uma visão através de uma coleção de microserviços. Se precisar de executar várias consultas improvisadas sobre os dados, deve considerar escrever os dados de cada microserviço para um serviço de armazenamento de dados para análise offline.

Os microserviços são versados. É possível que diferentes versões de um microserviço corram lado a lado. Uma versão mais recente de um microserviço pode falhar durante uma atualização e precisa de ser revertida para uma versão anterior. A versão também é útil para testes A/B, onde diferentes utilizadores experimentam diferentes versões do serviço. Por exemplo, é comum atualizar um microserviço para um conjunto específico de clientes para testar uma nova funcionalidade antes de ser lançado mais amplamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microserviços sobre interfaces e protocolos bem definidos

Ao longo dos últimos 10 anos, foram publicadas informações extensivas que descrevem padrões de comunicação em arquiteturas orientadas para o serviço. Geralmente, a comunicação de serviço utiliza uma abordagem REST com protocolos HTTP e TCP e XML ou JSON como formato de serialização. Do ponto de vista da interface, trata-se de ter uma abordagem de web design. Mas nada deve impedi-lo de usar protocolos binários ou os seus próprios formatos de dados. Basta estar ciente de que as pessoas terão mais dificuldade em usar os seus microserviços se estes protocolos e formatos não estiverem abertamente disponíveis.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome único (URL) usado para resolver a sua localização

O seu microserviço tem de ser endereçada onde quer que esteja a funcionar. Se estás a pensar em máquinas e em qual delas está a fazer um microserviço em particular, as coisas podem correr mal rapidamente.

Da mesma forma que o DNS resolve um URL específico para uma determinada máquina, o seu microserviço precisa de um nome único para que a sua localização atual seja detetável. Os microserviços precisam de nomes que sejam independentes da infraestrutura em que estão a funcionar. Isto implica que há uma interação entre a forma como o seu serviço é implementado e como é descoberto, porque tem de haver um registo de serviço. Quando uma máquina falha, o serviço de registo tem de lhe dizer para onde o serviço foi transferido.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de resolver, especialmente num sistema distribuído. Grande parte do código que escrevemos como desenvolvedores é para lidar com exceções. Durante os testes, também passamos mais tempo no manuseamento de exceções. O processo está mais envolvido do que escrever código para lidar com falhas. O que acontece quando a máquina em que o micro-serviço está a funcionar falha? É preciso detetar a falha, que é um problema difícil por si só. Mas também precisa de reiniciar o seu microserviço.

Para disponibilidade, um microserviço precisa de ser resiliente a falhas e capaz de reiniciar em outra máquina. Além destes requisitos de resiliência, os dados não devem ser perdidos, e os dados precisam de permanecer consistentes.

A resiliência é difícil de conseguir quando as falhas acontecem durante uma atualização da aplicação. O microserviço, trabalhando com o sistema de implantação, não precisa de recuperar. Tem de determinar se pode continuar a avançar para a versão mais recente ou voltar para uma versão anterior para manter um estado consistente. É preciso considerar algumas questões, como se estão disponíveis máquinas suficientes para continuar a avançar e como recuperar versões anteriores do microserviço. Para tomar estas decisões, você precisa do micro serviço para emitir informações de saúde.

### <a name="reports-health-and-diagnostics"></a>Relatórios de saúde e diagnósticos

Pode parecer óbvio, e muitas vezes é negligenciado, mas um microserviço precisa reportar a sua saúde e diagnósticos. Caso contrário, tem pouca visão sobre a sua saúde do ponto de vista das operações. Correlacionar eventos de diagnóstico em um conjunto de serviços independentes, e lidar com relógios de máquinas distorce para dar sentido à ordem do evento, é um desafio. Da mesma forma que interage com um micro-serviço sobre protocolos e formatos de dados acordados, precisa de uniformizar como registar eventos de saúde e diagnóstico que acabarão por acabar numa loja de eventos para consulta e visualização. Com uma abordagem de microserviços, diferentes equipas precisam de chegar a acordo sobre um único formato de registo. Tem de haver uma abordagem coerente para ver os acontecimentos de diagnóstico no conjunto da aplicação.

A saúde é diferente dos diagnósticos. A saúde tem a ver com o microserviço que reporta o seu estado atual para tomar as medidas adequadas. Um bom exemplo é trabalhar com mecanismos de upgrade e implantação para manter a disponibilidade. Embora um serviço possa não ser saudável devido a uma falha de processo ou ao reboot da máquina, o serviço pode ainda estar operacional. A última coisa que precisas é de piorar a situação iniciando uma atualização. A melhor abordagem é investigar primeiro ou dar tempo para que o micro serviço recupere. Os eventos de saúde de um microserviço ajudam-nos a tomar decisões informadas e, com efeito, ajudam a criar serviços de auto-cura.

## <a name="guidance-for-designing-microservices-on-azure"></a>Orientação para a conceção de microserviços em Azure

Visite o Centro de Arquitetura Azure para orientar a [conceção e construção de microserviços em Azure.](/azure/architecture/microservices/)

## <a name="service-fabric-as-a-microservices-platform"></a>Tecido de serviço como plataforma de microserviços

A Azure Service Fabric surgiu quando a Microsoft passou de entregar produtos em caixa, que eram tipicamente monolíticos, para serviços de prestação de serviços. A experiência de construir e operar grandes serviços, como a Azure SQL Database e a Azure Cosmos DB, em forma de Tecido de Serviço. A plataforma evoluiu ao longo do tempo à medida que mais serviços a adotaram. O Service Fabric teve de funcionar não só no Azure, mas também em implementações autónomas do Windows Server.

_*_O objetivo da Service Fabric é resolver os problemas difíceis de construir e executar um serviço e utilizar eficientemente os recursos de infraestrutura, para que as equipas possam resolver problemas de negócio utilizando uma abordagem de microserviços._*_

Este vídeo breve introduz o Service Fabric e os microsserviços:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

O Service Fabric ajuda-o a construir aplicações que utilizam uma abordagem de microserviços fornecendo:

_ Uma plataforma que fornece serviços de sistema para implantar, atualizar, detetar e reiniciar serviços falhados, descobrir serviços, enviar mensagens, gerir o estado e monitorizar a saúde.
* A capacidade de implantar aplicações em contentores ou como processos. Service Fabric é um contentor e orquestrador de processos.
* APIs de programação produtiva para ajudá-lo a construir aplicações como microserviços: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Por exemplo, você pode obter informações de saúde e diagnóstico, ou você pode aproveitar a alta disponibilidade incorporada.

***O Service Fabric é agnóstico sobre como constrói o seu serviço e pode utilizar qualquer tecnologia. Mas fornece APIs de programação incorporada que facilitam a construção de microserviços.** _

### <a name="migrating-existing-applications-to-service-fabric"></a>Aplicações existentes em migração para o Tecido de Serviço

O Service Fabric permite-lhe reutilizar o código existente e modernizá-lo com novos microserviços. Existem cinco fases para modernização de aplicações, e você pode começar e parar em qualquer fase. Os palcos são:

1) Comece com uma aplicação monolítica tradicional.  
2) Migrar. Utilize recipientes ou executáveis de hóspedes para hospedar o código existente no Tecido de Serviço.  
3) Modernizar. Adicione novos microserviços juntamente com o código contentor existente.  
4) Inovar. Parta a aplicação monolítica em microserviços com base na necessidade.  
5) Transformar aplicações em microserviços. Transforme as aplicações monolíticas existentes ou construa novas aplicações de campo verde.

![Migração para microserviços][Image3]

Lembre-se, pode _start e parar em qualquer uma destas fases*. Não tens de progredir para a próxima fase. 

Vejamos os exemplos de cada uma destas fases.

**Migrar**  
Por duas razões, muitas empresas estão a migrar aplicações monolíticas existentes em contentores:

* Redução de custos, quer devido à consolidação e remoção de hardware existente, quer devido a aplicações de execução com maior densidade.
* Um contrato de implantação consistente entre desenvolvimento e operações.

As reduções de custos são simples. Na Microsoft, muitas aplicações existentes estão a ser contentorizadas, levando a milhões de dólares em poupanças. Uma implantação consistente é mais difícil de avaliar, mas igualmente importante. Significa que os desenvolvedores podem escolher as tecnologias que lhes convém, mas as operações aceitarão apenas um único método para implementar e gerir as aplicações. Alivia as operações de ter de lidar com a complexidade de apoiar diferentes tecnologias sem forçar os desenvolvedores a escolher apenas certas. Essencialmente, cada aplicação é contentorizada em imagens de implantação independentes.

Muitas organizações param aqui. Eles já têm os benefícios dos contentores, e o Service Fabric fornece toda a experiência de gestão, incluindo implementação, upgrades, versões, rollbacks e monitorização de saúde.

**Modernizar**  
A modernização é a adição de novos serviços ao lado do código contentor existente. Se vai escrever um novo código, é melhor dar pequenos passos no caminho dos microserviços. Isto pode significar adicionar um novo ponto final da API REST ou uma nova lógica de negócio. Desta forma, inicia-se o processo de construção de novos microserviços e práticas de desenvolvimento e implantação.

**Inove**  
Uma abordagem de microserviços acomoda as necessidades de negócio em mudança. Nesta fase, você precisa decidir se começa a dividir a aplicação monolítica em serviços, ou inovar. Um exemplo clássico aqui é quando uma base de dados que está a usar como uma fila de fluxo de trabalho torna-se um estrangulamento de processamento. À medida que o número de pedidos de fluxo de trabalho aumenta, a obra tem de ser distribuída em escala. Pegue na peça em particular da aplicação que não está a escalonar, ou que precisa de ser atualizada com mais frequência, e divida-a como um microserviço e inovar.

**Transformar aplicações em microserviços**  
Nesta fase, a sua aplicação é totalmente composta por microserviços (ou divididos em) micro-serviços. Para chegar a este ponto, fez a viagem de microserviços. Pode começar aqui, mas fazê-lo sem uma plataforma de microserviços para o ajudar requer um investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>Os microserviços estão certos para a minha candidatura?

Talvez sim. Na Microsoft, à medida que mais equipas começaram a construir para a nuvem por razões de negócio, muitas delas perceberam os benefícios de ter uma abordagem semelhante a um microservice. Bing, por exemplo, tem usado microserviços há anos. Para outras equipas, a abordagem dos microserviços era nova. As equipas descobriram que havia problemas difíceis de resolver fora das suas áreas centrais de força. É por isso que a Service Fabric ganhou tração como tecnologia para serviços de construção.

O objetivo da Service Fabric é reduzir as complexidades de construir aplicações de microserviços para que não tenha de passar por tantas remodelações dispendiosas. Comece pequeno, em escala quando necessário, depreca serviços, adicione novos e evolua com o uso do cliente. Sabemos também que há muitos outros problemas ainda por resolver para tornar os microserviços mais acessíveis para a maioria dos desenvolvedores. Os contentores e o modelo de programação do ator são exemplos de pequenos passos nesse sentido. Estamos certos de que surgirão mais inovações para facilitar a abordagem dos microserviços.

## <a name="next-steps"></a>Passos seguintes

* [Microserviços: Uma revolução de aplicações alimentada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centro de Arquitetura Azure: Construção de microserviços em Azure](/azure/architecture/microservices/)
* [Aplicação de tecido de serviço Azure e boas práticas de cluster](service-fabric-best-practices-overview.md)
* [Visão geral da terminologia do tecido de serviço](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
