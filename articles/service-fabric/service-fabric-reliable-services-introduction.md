---
title: Descrição geral do Service Fabric Reliable Services do modelo de programação | Documentos da Microsoft
description: Saiba mais sobre o modelo de programação do Service Fabric Reliable Service e começar a escrever seus próprios serviços.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 68128a36c2ebd60f4ea84ace21aefd7e2fcc2557
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732998"
---
# <a name="reliable-services-overview"></a>Descrição geral do Reliable Services
O Azure Service Fabric simplifica a escrita e gestão de serviços fiáveis com e sem estado. Este tópico abrange:

* O modelo de programação Reliable Services para serviços com e sem estado.
* As opções disponíveis para a tomar ao escrever um serviço fiável.
* Alguns cenários e exemplos de quando utilizar a Reliable Services e como elas são gravadas.

Reliable Services é um dos modelos de programação disponíveis no Service Fabric. O outro é o modelo de programação dos Reliable Actors, que fornece um modelo de programação de Ator virtual por cima do modelo do Reliable Services. Para obter mais informações sobre o modelo de programação Reliable Actors, consulte [introdução ao Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

O Service Fabric gere o ciclo de vida dos serviços, desde o aprovisionamento e implementação através da atualização e eliminação, via [gestão de aplicações do Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Quais são os Reliable Services?
Reliable Services-lhe um modelo de programação simple, poderoso, de nível superior para o ajudar a expressar o que é importante para a sua aplicação. Com o modelo de programação de serviços fiáveis, obtém:

* Acesso ao restante dos APIs de programação do Service Fabric. Ao contrário dos serviços do Service Fabric, modelado como [executáveis convidados](service-fabric-guest-executables-introduction.md), Reliable Services possa usar o resto das APIs de recursos de infraestrutura do serviço diretamente. Isso permite que os serviços:
  * consultar o sistema
  * Estado de funcionamento do relatório sobre entidades do cluster
  * receber notificações sobre alterações de configuração e de código
  * localizar e se comunicar com outros serviços,
  * (opcionalmente) a utilizar o [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
  * ...e. conceder acesso à muitas outras funcionalidades, tudo a partir de um modelo de programação de primeira classe em várias linguagens de programação.
* Um modelo simples para executar seu próprio código que parece que já conhece de modelos de programação. Seu código tem um ponto de entrada bem-definida e o ciclo de vida facilmente gerenciado.
* Um modelo de comunicação conectáveis. Utilizar o transporte de sua escolha, tal como HTTP com [Web API](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados, ou qualquer outra coisa. Reliable Services fornecer algumas ótimas opções de out-of-the-box pode utilizar, ou pode fornecer seu próprio.
* Para serviços com estado, o modelo de programação Reliable Services permite-lhe consistente e fiável armazenar seu estado diretamente dentro do seu serviço, utilizando [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). As coleções fiáveis são um conjunto simples de classes de coleção de elevada disponibilidade e fiáveis que lhe será familiar para qualquer pessoa que tenha utilizado C# coleções. Tradicionalmente, os serviços necessários sistemas externos para gerenciamento de estado fiável. Com as Reliable Collections, pode armazenar seu estado junto a computação com o mesmo elevada disponibilidade e fiabilidade de que poderia esperar de elevada disponibilidade arquivos externos. Este modelo também melhora a latência porque alocar conjuntamente a computação e o estado para funcionar.

## <a name="what-makes-reliable-services-different"></a>O que diferencia Reliable Services?
Serviços fiáveis no Service Fabric são diferentes dos serviços que possa ter escrito antes. O Service Fabric fornece confiabilidade, disponibilidade, consistência e escalabilidade.

* **Confiabilidade** – os serviço permanece até mesmo em ambientes não confiáveis em que as máquinas falharam ou pressionar problemas de rede ou em casos em que os serviços se encontram erros e falhas ou falharam. Para serviços com estado, o seu estado é preservado mesmo na ocorrência de rede ou outras falhas.
* **Disponibilidade** -seu serviço está acessível e a capacidade de resposta. Service Fabric mantém o número pretendido da execução de cópias.
* **Escalabilidade** - serviços estão desassociados dos hardware específico e podem aumentar ou diminuir conforme necessário através da adição ou remoção de hardware ou outros recursos. Os serviços são facilmente particionados (especialmente no caso de com monitoração de estado) para se certificar de que o serviço pode ser dimensionado e lidar com falhas parciais. Os serviços podem ser criados e eliminado dinamicamente por meio do código, permitindo que mais instâncias ser rotacionado conforme necessário, digamos em resposta aos pedidos dos clientes. Por fim, o Service Fabric incentiva serviços para ser leve. Service Fabric permite milhares de serviços a serem aprovisionados dentro de um único processo, em vez de exigir ou dedicar instâncias de sistema operacional inteiras ou processos para uma única instância de um serviço.
* **Consistência** -todas as informações armazenadas neste serviço podem ser garantidas como estando consistente. Isso vale mesmo entre várias coleções fiáveis dentro de um serviço. Podem ser feitas alterações nos conjuntos de dentro de um serviço de forma ao nível das transações atômica.

## <a name="service-lifecycle"></a>Ciclo de vida do serviço
Se o seu serviço estiver com monitoração de estado ou sem estado, o Reliable Services fornecem um ciclo de vida simple que lhe permitem conectar seu código rapidamente e começar a utilizar.  Há apenas um ou dois métodos que precisa implementar para obter o seu serviço em funcionamento.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** -esse método é onde o serviço define o stack(s) de comunicação que ele deseja usar. A comunicação de pilha, como [Web API](service-fabric-reliable-services-communication-webapi.md), é o que define o ponto final de escuta ou a pontos de extremidade do serviço (como os clientes chegarem ao serviço). Também define como as mensagens que são apresentados interagem com o restante do código de serviço.
* **RunAsync** -esse método é onde o seu serviço é executada a sua lógica de negócios e, em que ele seria disparar todas as tarefas em segundo plano que devem ser executados durante o ciclo de vida do serviço. O token de cancelamento é fornecido é um sinal para quando esse trabalho deverá ser interrompida. Por exemplo, se o serviço precisar extraem as mensagens fora de uma fila do Reliable e processá-las, isso é onde acontece esse trabalho.

Se estiver aprendendo sobre serviços fiáveis pela primeira vez, continue a ler! Se procura-se para obter instruções detalhadas do ciclo de vida dos reliable services, pode ir para o [este artigo](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Serviços de exemplo
Vamos saber esse modelo de programação, dar uma olhada rápida em dois serviços diferentes para ver como essas partes se encaixam.

### <a name="stateless-reliable-services"></a>Sem monitoração de estado do Reliable Services
Um serviço sem estado é o local uma onde não existe nenhum estado mantido no serviço em chamadas. Qualquer Estado que está presente é inteiramente descartável e não requer sincronização, replicação, persistência ou elevada disponibilidade.

Por exemplo, considere uma calculadora de que não tem memória e recebe todos os termos e operações a serem executadas ao mesmo tempo.

Neste caso, o `RunAsync()` (C#) ou `runAsync()` (Java) do serviço pode estar vazio, uma vez que não existe nenhuma tarefa-processamento de em segundo plano que o serviço precisa para fazer. Quando o serviço da Calculadora é criado, ele retorna um `ICommunicationListener` (C#) ou `CommunicationListener` (Java) (por exemplo [Web API](service-fabric-reliable-services-communication-webapi.md)) que abre um ponto final de escuta em alguma porta. Este ponto final de escuta conecta-se para os métodos de cálculo diferentes (exemplo: "Adicionar (n1, n2)") que definem de API pública a Calculadora.

Quando é efetuada uma chamada a partir de um cliente, o método apropriado é invocado e o serviço da Calculadora realiza as operações nos dados fornecidos e devolve o resultado. Não armazena qualquer Estado.

Não armazenar qualquer Estado interno torna esta Calculadora de exemplo simples. Mas a maioria dos serviços não são verdadeiramente sem monitoração de estado. Em vez disso, eles externalizar seu estado para algum outro armazenamento. (Por exemplo, qualquer aplicação web que se baseia em manter o estado da sessão num arquivo de backup ou uma cache não é sem monitoração de estado.)

Um exemplo comum da forma como os serviços sem monitoração de estado são utilizados no Service Fabric é como um front-end que expõe a API de destinado ao público para um aplicativo web. O serviço de front-end, em seguida, conversa com os serviços com estado para concluir um pedido de utilizador. Neste caso, as chamadas dos clientes são direcionadas para uma porta conhecida, como 80, onde o serviço sem estado está a escutar. Este serviço sem estado recebe a chamada e determina se a chamada é de um confiável e que mantê-lo se destinar a.  Em seguida, o serviço sem estado reencaminha a chamada para a partição correta do serviço com monitorização de estado e aguarda uma resposta. Quando o serviço sem estado recebe uma resposta, ele responde ao cliente original. Um exemplo de um serviço como esse é em nossos exemplos [ C# ](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). Este é apenas um exemplo desse padrão nos exemplos, há outros nos outros exemplos também.

### <a name="stateful-reliable-services"></a>Reliable Services com estado
Um serviço com estado é aquele que tem de ter uma parte do estado mantido consistentes e presentes para que o serviço de função. Considere um serviço que constantemente computa uma média móvel de um valor com base nas atualizações que recebe. Para tal, tem de ter o conjunto atual de pedidos de entrada tem de processo e a média de atual. Qualquer serviço que obtém, processa e armazena informações num repositório externo (por exemplo, um blob ou tabela loja Azure hoje mesmo) é com monitoração de estado. Apenas mantém seu estado no arquivo de estado externo.

A maioria dos serviços hoje armazenar seus Estados externamente, uma vez que o arquivo externo é o que fornece a fiabilidade, disponibilidade, escalabilidade e consistência para esse Estado. No Service Fabric, os serviços não são necessários para armazenar seus Estados externamente. Service Fabric se encarrega dos requisitos seguintes para o código de serviço e o estado do serviço.

Vamos supor que Desejamos escrever um serviço que processa imagens. Para fazer isso, o serviço aceita uma imagem e a série de conversões para executar na mesma. Este serviço retorna um serviço de escuta de comunicação (vamos suponha que é uma API Web) que expõe uma API como `ConvertImage(Image i, IList<Conversion> conversions)`. Quando recebe um pedido, o serviço armazena-a num `IReliableQueue`e retorna algumas id para o cliente para que possa acompanhar o pedido.

Neste serviço, `RunAsync()` poderia ser mais complexa. O serviço tem um loop dentro do respetivo `RunAsync()` que recebe pedidos de `IReliableQueue` e realiza as conversões de pedido. Os resultados são armazenados num `IReliableDictionary` , de modo que quando o cliente volta e eles podem obter as suas imagens convertidas. Para garantir que, mesmo se algo falhar a imagem não é perdido, este serviço fiável seria puxar para fora da fila, execute as conversões e Armazeno o resultado numa única transação. Neste caso, a mensagem é removida da fila e os resultados são armazenados no dicionário de resultado, apenas quando as conversões estiverem concluídas. Em alternativa, o serviço foi possível extrair a imagem para fora da fila e armazená-los imediatamente num armazenamento remoto. Isso reduz a quantidade de estado que do serviço tem de gerir, mas a complexidade aumenta, desde que o serviço tem de manter os metadados necessários para gerir o armazenamento remoto. Com qualquer uma das abordagens, se algo falhou no meio, o pedido permanece na fila de espera para serem processados.

Uma coisa a ser observado sobre este serviço é que ele pode parecer um serviço .NET normal! A única diferença é que os dados de estrutura a ser utilizado (`IReliableQueue` e `IReliableDictionary`) são fornecidos pelo Service Fabric e são altamente confiável, disponível e consistente.

## <a name="when-to-use-reliable-services-apis"></a>Quando utilizar APIs de serviços fiáveis
Se qualquer um dos seguintes caracterizam as necessidades do seu serviço de aplicação, avalie a possibilidade de APIs de serviços fiáveis:

* Quer o código do seu serviço (e, opcionalmente, de estado) para ser altamente disponível e fiável
* Terá de garantias transacionais em várias unidades de estado (por exemplo, orders e itens de linha da ordem).
* Estado da sua aplicação pode ser modelado naturalmente como dicionários fiável e filas.
* O código de aplicativos ou estado tem de ser altamente disponível com baixa latência leituras e gravações.
* Seu aplicativo precisa para controlar a simultaneidade ou a granularidade de operações transacionadas numa ou mais coleções fiáveis.
* Pretende gerir as informações ou controlar o esquema de partição para o seu serviço.
* O código precisa de um ambiente de tempo de execução possuem threads.
* A aplicação tem de criar e destruir dicionários fiável ou nas filas e serviços todos no tempo de execução de forma dinâmica.
* Precisa controlar fornecido com recursos de infraestrutura do serviço de cópia de segurança e restaurar funcionalidades para o estado do seu serviço através de programação.
* Seu aplicativo precisa para manter o histórico de alterações de suas unidades de estado.
* Pretende desenvolver ou fornecedores de estado personalizado, third party desenvolvidas de consumir.

## <a name="next-steps"></a>Passos Seguintes
* [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
* [Coleções fiáveis](service-fabric-reliable-services-reliable-collections.md)
* [O modelo de programação Reliable Actors](service-fabric-reliable-actors-introduction.md)
