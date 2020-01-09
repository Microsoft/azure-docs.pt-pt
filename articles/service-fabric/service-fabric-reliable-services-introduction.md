---
title: Visão geral do modelo de programação de Reliable Service
description: Saiba mais sobre o modelo de programação de serviço confiável do Service Fabric e comece a escrever seus próprios serviços.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 88c8e4411c0bec23790b4f4c52fc4a3d1570edc6
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614252"
---
# <a name="reliable-services-overview"></a>Descrição geral dos Reliable Services
O Azure Service Fabric simplifica a gravação e o gerenciamento de Reliable Services com e sem estado. Este tópico abrange:

* O modelo de programação de Reliable Services para serviços com e sem estado.
* As opções que você precisa fazer ao escrever um serviço confiável.
* Alguns cenários e exemplos de quando usar Reliable Services e como eles são gravados.

Reliable Services é um dos modelos de programação disponíveis no Service Fabric. O outro é o modelo de programação de ator confiável, que fornece um modelo de programação de ator virtual sobre o modelo de Reliable Services. Para obter mais informações sobre o modelo de programação Reliable Actors, consulte [Introduction to Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

O Service Fabric gerencia o tempo de vida dos serviços, desde o provisionamento e a implantação até a atualização e exclusão, por meio do [Gerenciamento de aplicativos Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>O que são Reliable Services?
Reliable Services fornece um modelo de programação simples, poderoso e de nível superior para ajudá-lo a expressar o que é importante para seu aplicativo. Com o modelo de programação Reliable Services, você obtém:

* Acesso ao restante das APIs de programação de Service Fabric. Ao contrário dos serviços de Service Fabric modelados como [executáveis convidados](service-fabric-guest-executables-introduction.md), Reliable Services obter o uso do restante das APIs do Service Fabric diretamente. Isso permite que os serviços:
  * consultar o sistema
  * relatar a integridade sobre entidades no cluster
  * receber notificações sobre alterações de configuração e código
  * Localize e comunique-se com outros serviços,
  * (opcionalmente) usar as [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md)
  * ...e. conceder acesso à muitas outras funcionalidades, tudo a partir de um modelo de programação de primeira classe em várias linguagens de programação.
* Um modelo simples para executar seu próprio código que se parece com modelos de programação para os quais você está acostumado. Seu código tem um ponto de entrada bem definido e um ciclo de vida gerenciado com facilidade.
* Um modelo de comunicação conectável. Use o transporte de sua escolha, como HTTP com [API Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados ou qualquer outra coisa. Reliable Services fornecem algumas opções excelentes que você pode usar, ou você pode fornecer a sua própria.
* Para serviços com estado, o modelo de programação Reliable Services permite que você armazene de forma consistente e confiável seu estado no seu serviço usando [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md). As coleções confiáveis são um conjunto simples de classes de coleções altamente disponíveis e confiáveis que serão familiares para qualquer pessoa que C# tenha usado coleções. Tradicionalmente, os serviços precisavam de sistemas externos para o gerenciamento confiável de estado. Com as coleções confiáveis, você pode armazenar seu estado ao lado de sua computação com a mesma alta disponibilidade e confiabilidade que espera de armazenamentos externos altamente disponíveis. Esse modelo também melhora a latência porque você está Colocalizando a computação e o estado de que precisa para funcionar.

## <a name="what-makes-reliable-services-different"></a>O que torna Reliable Services diferente?
Os Reliable Services em Service Fabric são diferentes dos serviços que você pode ter escrito anteriormente. Service Fabric fornece confiabilidade, disponibilidade, consistência e escalabilidade.

* **Confiabilidade** – seu serviço permanece até mesmo em ambientes não confiáveis em que seus computadores falham ou atingem problemas de rede, ou em casos em que os próprios serviços encontram erros e falham ou falham. Para serviços com estado, seu estado é preservado mesmo na presença de rede ou outras falhas.
* **Disponibilidade** -seu serviço está acessível e respondendo. Service Fabric mantém o número desejado de cópias em execução.
* **Escalabilidade** -os serviços são dissociados de um hardware específico e podem crescer ou reduzir conforme necessário por meio da adição ou remoção de hardware ou outros recursos. Os serviços são particionados facilmente (especialmente no caso com estado) para garantir que o serviço possa ser dimensionado e lidar com falhas parciais. Os serviços podem ser criados e excluídos dinamicamente por meio de código, permitindo que mais instâncias sejam giradas conforme o necessário, digamos em resposta às solicitações do cliente. Por fim, Service Fabric incentiva os serviços a serem leves. Service Fabric permite que milhares de serviços sejam provisionados em um único processo, em vez de exigir ou dedicar processos ou instâncias de sistema operacional inteiras a uma única instância de um serviço.
* **Consistência** -todas as informações armazenadas nesse serviço podem ter a garantia de serem consistentes. Isso é verdadeiro mesmo em várias coleções confiáveis dentro de um serviço. As alterações nas coleções de um serviço podem ser feitas de forma transacional atômica.

## <a name="service-lifecycle"></a>Ciclo de vida do serviço
Se o seu serviço tem monitoração de estado ou sem estado, Reliable Services fornecer um ciclo de vida simples que permita que você conecte seu código rapidamente e comece a usar.  Há apenas um ou dois métodos que você precisa implementar para colocar seu serviço em funcionamento.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** -esse método é onde o serviço define as pilhas de comunicação que deseja usar. A pilha de comunicação, como a [API Web](service-fabric-reliable-services-communication-webapi.md), é o que define o ponto de extremidade de escuta ou pontos de extremidades para o serviço (como os clientes atingem o serviço). Ele também define como as mensagens que aparecem interagem com o restante do código do serviço.
* **RunAsync** -esse método é onde o serviço executa sua lógica de negócios e onde ele iniciaria todas as tarefas em segundo plano que devem ser executadas durante o tempo de vida do serviço. O token de cancelamento fornecido é um sinal para quando o trabalho deve parar. Por exemplo, se o serviço precisar extrair mensagens de uma fila confiável e processá-las, é aí que esse trabalho acontece.

Se você estiver aprendendo sobre os Reliable Services pela primeira vez, continue lendo! Se você estiver procurando uma explicação detalhada do ciclo de vida dos Reliable Services, poderá ir até [Este artigo](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Serviços de exemplo
Sabendo esse modelo de programação, vamos dar uma olhada rápida em dois serviços diferentes para ver como essas peças se encaixam.

### <a name="stateless-reliable-services"></a>Reliable Services sem estado
Um serviço sem estado é aquele em que não há nenhum estado mantido dentro do serviço entre chamadas. Qualquer estado presente é totalmente descartável e não requer sincronização, replicação, persistência ou alta disponibilidade.

Por exemplo, considere uma calculadora que não tem memória e receba todos os termos e operações a serem executados ao mesmo tempo.

Nesse caso, o `RunAsync()` (C#) ou `runAsync()` (Java) do serviço pode estar vazio, já que não há nenhum processamento de tarefa em segundo plano que o serviço precisa fazer. Quando o serviço de calculadora é criado, ele retorna um `ICommunicationListener`C#() ou `CommunicationListener` (Java) (por exemplo, [API Web](service-fabric-reliable-services-communication-webapi.md)) que abre um ponto de extremidade de escuta em alguma porta. Esse ponto de extremidade de escuta se conecta aos diferentes métodos de cálculo (exemplo: "Add (N1, N2)") que definem a API pública da calculadora.

Quando uma chamada é feita de um cliente, o método apropriado é invocado e o serviço de calculadora executa as operações nos dados fornecidos e retorna o resultado. Ele não armazena nenhum estado.

Não armazenar nenhum estado interno torna essa calculadora de exemplo simples. Mas a maioria dos serviços não é realmente sem estado. Em vez disso, eles externamente o estado para algum outro repositório. (Por exemplo, qualquer aplicativo Web que dependa da manutenção do estado de sessão em um repositório de backup ou cache não está sem estado.)

Um exemplo comum de como os serviços sem estado são usados no Service Fabric é como um front-end que expõe a API voltada para o público para um aplicativo Web. Em seguida, o serviço de front-end se comunica com serviços com estado para concluir uma solicitação de usuário. Nesse caso, as chamadas de clientes são direcionadas para uma porta conhecida, como 80, em que o serviço sem estado está escutando. Esse serviço sem estado recebe a chamada e determina se a chamada é de uma parte confiável e para qual serviço ela está destinada.  Em seguida, o serviço sem estado encaminha a chamada para a partição correta do serviço com estado e aguarda uma resposta. Quando o serviço sem estado recebe uma resposta, ele responde ao cliente original. Um exemplo de tal serviço está em nossos exemplos [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). Esse é apenas um exemplo desse padrão nos exemplos; também há outros em outros exemplos.

### <a name="stateful-reliable-services"></a>Reliable Services com estado
Um serviço com estado é aquele que deve ter uma parte do estado mantida consistente e presente para que o serviço funcione. Considere um serviço que computa constantemente uma média móvel de algum valor com base nas atualizações recebidas. Para fazer isso, ele deve ter o conjunto atual de solicitações de entrada que precisa processar e a média atual. Qualquer serviço que recupere, processe e armazene informações em um repositório externo (como um blob do Azure ou repositório de tabelas hoje) é com estado. Ele apenas mantém seu estado no armazenamento de estado externo.

A maioria dos serviços atualmente armazena seu estado externamente, já que o repositório externo é o que fornece confiabilidade, disponibilidade, escalabilidade e consistência para esse estado. No Service Fabric, os serviços não são necessários para armazenar seu estado externamente. Service Fabric cuida desses requisitos para o código do serviço e o estado do serviço.

Digamos que desejamos escrever um serviço que processa imagens. Para fazer isso, o serviço usa uma imagem e a série de conversões a serem executadas nessa imagem. Esse serviço retorna um ouvinte de comunicação (vamos supor que se trata de um WebAPI) que expõe uma API como `ConvertImage(Image i, IList<Conversion> conversions)`. Quando recebe uma solicitação, o serviço a armazena em uma `IReliableQueue`e retorna alguma ID ao cliente para que possa rastrear a solicitação.

Nesse serviço, `RunAsync()` pode ser mais complexo. O serviço tem um loop dentro de seu `RunAsync()` que efetua pull das solicitações de `IReliableQueue` e executa as conversões solicitadas. Os resultados são armazenados em um `IReliableDictionary` para que, quando o cliente voltar, eles possam obter suas imagens convertidas. Para garantir que, mesmo que algo falhe, a imagem não seja perdida, esse serviço confiável extrairá a fila, executará as conversões e armazenaria o resultado tudo em uma única transação. Nesse caso, a mensagem é removida da fila e os resultados são armazenados no dicionário de resultados somente quando as conversões são concluídas. Como alternativa, o serviço pode extrair a imagem da fila e armazená-la imediatamente em um repositório remoto. Isso reduz a quantidade de estado que o serviço precisa gerenciar, mas aumenta a complexidade, pois o serviço precisa manter os metadados necessários para gerenciar o armazenamento remoto. Com qualquer abordagem, se algo falhar no meio, a solicitação permanecerá na fila aguardando para ser processada.

Uma coisa a ser observada sobre esse serviço é que ele parece um serviço .NET normal! A única diferença é que as estruturas de dados que estão sendo usadas (`IReliableQueue` e `IReliableDictionary`) são fornecidas por Service Fabric e são altamente confiáveis, disponíveis e consistentes.

## <a name="when-to-use-reliable-services-apis"></a>Quando usar Reliable Services APIs
Se qualquer um dos seguintes caracterizar suas necessidades de serviço de aplicativo, você deve considerar Reliable Services APIs:

* Você deseja que o código do serviço (e, opcionalmente, o estado) seja altamente disponível e confiável
* Você precisa de garantias transacionais em várias unidades de estado (por exemplo, pedidos e itens de linha de ordem).
* O estado do seu aplicativo pode ser modelado naturalmente como dicionários e filas confiáveis.
* O código ou o estado de seus aplicativos precisa estar altamente disponível com leituras e gravações de baixa latência.
* Seu aplicativo precisa controlar a simultaneidade ou a granularidade de operações transacionadas em uma ou mais coleções confiáveis.
* Você deseja gerenciar as comunicações ou controlar o esquema de particionamento para seu serviço.
* Seu código precisa de um ambiente de tempo de execução de thread livre.
* Seu aplicativo precisa criar ou destruir dinamicamente dicionários confiáveis ou filas ou serviços inteiros em tempo de execução.
* Você precisa controlar programaticamente os recursos de backup e restauração fornecidos pelo Service Fabric para o estado do seu serviço.
* Seu aplicativo precisa manter o histórico de alterações para suas unidades de estado.
* Você deseja desenvolver ou consumir provedores de estado personalizado desenvolvidos por terceiros.

## <a name="next-steps"></a>Passos seguintes
* [Início rápido de Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Coleções confiáveis](service-fabric-reliable-services-reliable-collections.md)
* [O modelo de programação Reliable Actors](service-fabric-reliable-actors-introduction.md)
