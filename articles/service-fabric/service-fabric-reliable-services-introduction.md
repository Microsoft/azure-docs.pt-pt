---
title: Visão geral do modelo de programação de serviço fiável
description: Conheça o modelo de programação de serviço fiável da Service Fabric e começar a escrever os seus próprios serviços.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: 58259b0d19d68c468779a579bd9c86e77106c18d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083513"
---
# <a name="reliable-services-overview"></a>Descrição geral do Reliable Services

A Azure Service Fabric simplifica a escrita e a gestão de serviços apátridas e apátridas. Este tópico abrange:

* O modelo de programação de Serviços Fiáveis para serviços apátridas e apátridas.
* As escolhas que tens de fazer ao escrever um Serviço Fiável.
* Alguns cenários e exemplos de quando usar Serviços Fiáveis e como são escritos.

*A Reliable Services* é um dos modelos de programação disponíveis no Service Fabric. Outro é o modelo de programação *do Ator Fiável,* que fornece um quadro de aplicação [de Ator Virtual](https://research.microsoft.com/en-us/projects/orleans/) para além do modelo De serviços fiáveis. Para obter mais informações sobre atores fiáveis, consulte [Introdução a Atores Fiáveis](service-fabric-reliable-actors-introduction.md)de Tecido de Serviço.

Service Fabric gere a vida útil dos serviços, desde o fornecimento e implantação através de upgrade e eliminação, através da gestão de [aplicações do Service Fabric.](service-fabric-deploy-remove-applications.md)

## <a name="what-are-reliable-services"></a>O que são Serviços Fiáveis

A Reliable Services dá-lhe um modelo de programação simples, poderoso e de alto nível para o ajudar a expressar o que é importante para a sua aplicação. Com o modelo de programação de Serviços Fiáveis, obtém-se:

* Acesso a APIs de tecido de serviço. Ao contrário dos serviços de tecido de serviço modelados como [Executáveis de Hóspedes,](service-fabric-guest-executables-introduction.md)os Serviços Fiáveis podem utilizar as APIs de Tecido de Serviço diretamente. Isto permite serviços para:
  * Consultar o sistema
  * Reportar saúde sobre entidades do cluster
  * Receba notificações sobre configuração e alterações de código
  * Encontre e comunique com outros serviços,
  * Use as [Coleções Fiáveis](service-fabric-reliable-services-reliable-collections.md)
  * Aceda a muitas outras capacidades, todas a partir de um modelo de programação de primeira classe em várias linguagens de programação.
* Um modelo simples para executar o seu próprio código que se sente como outros modelos de programação familiares. O seu código tem um ponto de entrada bem definido e um ciclo de vida facilmente gerido.
* Um modelo de comunicação pluggable. Utilize o transporte à sua escolha, como HTTP com [Web API,](service-fabric-reliable-services-communication-webapi.md)WebSockets, protocolos TCP personalizados ou qualquer outra coisa. Os Serviços Fiáveis fornecem algumas ótimas opções fora da caixa que você pode usar, ou você pode fornecer as suas próprias.
* Para serviços estatais, o modelo de programação de Serviços Fiáveis permite-lhe armazenar de forma consistente e fiável o seu estado dentro do seu serviço utilizando [Coleções Fiáveis](service-fabric-reliable-services-reliable-collections.md). As Coleções Fiáveis são um conjunto simples de aulas de recolha C# altamente disponíveis e fiáveis que serão familiares a qualquer pessoa que tenha usado coleções. Tradicionalmente, os serviços precisavam de sistemas externos para a gestão fiável do Estado. Com Coleções Fiáveis, pode armazenar o seu estado junto à sua computação com a mesma elevada disponibilidade e fiabilidade que você veio esperar de lojas externas altamente disponíveis. Este modelo também melhora a latência porque está a co-localizar a computação e a afirmar que precisa de funcionar.

## <a name="what-makes-reliable-services-different"></a>O que torna os Serviços Fiáveis diferentes

Os Serviços Fiáveis são diferentes dos serviços que pode ter escrito antes, porque o Service Fabric fornece:

* **Fiabilidade** - O seu serviço mantém-se mesmo em ambientes pouco fiáveis onde as suas máquinas falham ou atingem problemas de rede, ou nos casos em que os próprios serviços encontram erros e falham ou falham. Para serviços estatais, o seu estado é preservado mesmo na presença de redes ou outras falhas.
* **Disponibilidade** - O seu serviço é acessível e reativo. O Serviço Fabric mantém o número desejado de cópias em execução.
* **Escalabilidade** - Os serviços são dissociados de hardware específico, e podem crescer ou encolher conforme necessário através da adição ou remoção de hardware ou outros recursos. Os serviços são facilmente divididos (especialmente no caso de estado) para garantir que o serviço pode escalar e lidar com falhas parciais. Os serviços podem ser criados e eliminados dinamicamente através do código, permitindo que mais instâncias sejam fiadas se necessário, por exemplo, em resposta aos pedidos dos clientes. Finalmente, a Service Fabric encoraja os serviços a serem leves. O Service Fabric permite que milhares de serviços sejam prestados num único processo, em vez de exigir ou dedicar instâncias ou processos inteiros a um único serviço.
* **Consistência** - Qualquer informação armazenada num Serviço Fiável pode ser garantidamente consistente. Isto é verdade mesmo em várias Coleções Fiáveis dentro de um serviço. As alterações entre as coleções dentro de um serviço podem ser feitas de forma transaccionalmente atómica.

## <a name="service-lifecycle"></a>Ciclo de vida de serviço

Quer o seu serviço seja apátrida ou apátrida, a Reliable Services fornece um ciclo de vida simples que permite ligar rapidamente o seu código e começar.  Obter um novo serviço em funcionamento requer que implemente dois métodos:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - Este método é onde o serviço define a(s) pilha de comunicação que quer usar. A pilha de comunicações, como a [Web API,](service-fabric-reliable-services-communication-webapi.md)é o que define o ponto final de audição ou pontos finais para o serviço (como os clientes chegam ao serviço). Também define como as mensagens que aparecem interagem com o resto do código de serviço.
* **RunAsync** - Este método é onde o seu serviço executa a sua lógica de negócio, e onde iniciaria quaisquer tarefas de fundo que deverão funcionar durante toda a vida do serviço. O sinal de cancelamento que é fornecido é um sinal para quando esse trabalho deve parar. Por exemplo, se o serviço precisa de retirar mensagens de uma Fila Fiável e processá-las, é aqui que esse trabalho acontece.

Se está a aprender sobre serviços fiáveis pela primeira vez, leia! Se procura uma passagem detalhada do ciclo de vida de serviços fiáveis, consulte a visão geral do ciclo de [vida dos Serviços Fiáveis.](service-fabric-reliable-services-lifecycle.md)

## <a name="example-services"></a>Serviços de exemplo

Vamos ver mais de perto como funciona o modelo de Serviços Fiáveis com serviços apátridas e apátridas.

### <a name="stateless-reliable-services"></a>Serviços Fiáveis Apátridas

Um *serviço apátrida* é aquele em que não há estado mantido dentro do serviço através de chamadas. Qualquer estado presente é totalmente descartável e não requer sincronização, replicação, persistência ou alta disponibilidade.

Por exemplo, considere uma calculadora que não tenha memória e receba todos os termos e operações para executar de uma só vez.

Neste caso, oC#`RunAsync()` ( ) ou `runAsync()` (Java) do serviço pode estar vazio, uma vez que não existe um processamento de tarefas de fundo que o serviço precise de fazer. Quando o serviço de calculadora éC#criado, devolve uma `ICommunicationListener` ( ou `CommunicationListener` (Java) (por [exemplo, Web API](service-fabric-reliable-services-communication-webapi.md)) que abre um ponto final de audição em alguma porta. Este ponto final de audição liga-se aos diferentes métodos de cálculo (exemplo: "Adicionar (n1, n2)") que definem a API pública da calculadora.

Quando uma chamada é feita a partir de um cliente, o método apropriado é invocado, e o serviço de calculadora realiza as operações nos dados fornecidos e devolve o resultado. Não armazena nenhum estado.

Não armazenar nenhum estado interno torna esta calculadora de exemplo simples. Mas a maioria dos serviços não são verdadeiramente apátridas. Em vez disso, externalizam o seu estado para outra loja. (Por exemplo, qualquer aplicação web que dependa de manter o estado de sessão numa loja de apoio ou cache não é apátrida.)

Um exemplo comum de como os serviços apátridas são usados no Tecido de Serviço é como uma fachada que expõe a API virada para o público para uma aplicação web. O serviço front-end fala então com serviços estatais para completar um pedido de utilizador. Neste caso, as chamadas de clientes são direcionadas para um porto conhecido, como 80, onde o serviço apátrida está a ouvir. Este serviço apátrida recebe a chamada e determina se a chamada é de uma parte de confiança e para que serviço está destinado.  Em seguida, o serviço apátrida reencaminha a chamada para a correta partição do serviço e aguarda uma resposta. Quando o serviço apátrida recebe uma resposta, responde ao cliente original. Um exemplo de tal serviço é a[C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) amostra *de Fabricação de Serviço Iniciando -se* ( / [Java),](https://github.com/Azure-Samples/service-fabric-java-getting-started)entre outras amostras de Tecido de Serviço nesse repo.

### <a name="stateful-reliable-services"></a>Serviços Fiáveis e estatais

Um *serviço imponente* é aquele que deve ter alguma parte do Estado mantida consistente e presente para que o serviço funcione. Considere um serviço que calcula constantemente uma média de rolo de algum valor com base em atualizações que recebe. Para isso, deve ter o conjunto atual de pedidos de entrada que precisa de processar e a média atual. Qualquer serviço que recupere, processe e armazene informações numa loja externa (como uma bolha Azure ou uma loja de mesa seletiva) é imponente. Mantém o seu estado na loja externa do estado.

A maioria dos serviços hoje armazenam o seu estado externamente, uma vez que a loja externa é o que proporciona fiabilidade, disponibilidade, escalabilidade e consistência para esse estado. Na Service Fabric, os serviços não são necessários para armazenar o seu estado externamente. A Service Fabric cuida destes requisitos tanto para o código de serviço como para o estado de serviço.

Digamos que queremos escrever um serviço que processe as imagens. Para isso, o serviço leva uma imagem e a série de conversões para realizar nessa imagem. Este serviço devolve um ouvinte de comunicação (suponhamos que seja um WebAPI) que expõe uma API como `ConvertImage(Image i, IList<Conversion> conversions)`. Quando recebe um pedido, o serviço armazena-o num `IReliableQueue`, e devolve alguma identificação ao cliente para que possa acompanhar o pedido.

Neste serviço, `RunAsync()` poderia ser mais complexo. O serviço tem um loop dentro do seu `RunAsync()` que retira pedidos de `IReliableQueue` e realiza as conversões solicitadas. Os resultados são armazenados numa `IReliableDictionary` para que quando o cliente voltar possam obter as suas imagens convertidas. Para garantir que, mesmo que algo falhe, a imagem não se perde, este Serviço Fiável sairia da fila, realizaria as conversões e armazenaria o resultado numa única transação. Neste caso, a mensagem é removida da fila e os resultados são armazenados no dicionário de resultados apenas quando as conversões estiverem completas. Em alternativa, o serviço poderia retirar a imagem da fila e armazená-la imediatamente numa loja remota. Isto reduz a quantidade de estado que o serviço tem de gerir, mas aumenta a complexidade, uma vez que o serviço tem de manter os metadados necessários para gerir a loja remota. Com qualquer uma das abordagems, se algo falhou no meio, o pedido permanece na fila à espera de ser processado.

Embora este serviço pareça um serviço típico .NET, a diferença é que as estruturas de dados que estão a ser utilizadas (`IReliableQueue` e `IReliableDictionary`) são fornecidas pela Service Fabric, e são altamente fiáveis, disponíveis e consistentes.

## <a name="when-to-use-reliable-services-apis"></a>Quando utilizar APIs de Serviços Fiáveis

Considere APIs de Serviços Fiáveis se:

* Pretende que o código do seu serviço (e opcionalmente estado) seja altamente disponível e fiável.
* Necessita de garantias transacionais em várias unidades de Estado (por exemplo, encomendas e itens de linha de encomenda).
* O estado da sua aplicação pode ser naturalmente modelado como Dicionários e Filas Fiáveis.
* O código ou estado das suas aplicações tem de estar altamente disponível com leituras e escritos de baixa latência.
* A sua aplicação necessita de controlar a conmoeda ou granularidade das operações transacionadas através de uma ou mais Coleções Fiáveis.
* Pretende gerir as comunicações ou controlar o esquema de partição do seu serviço.
* O seu código precisa de um ambiente de tempo de execução com fios livres.
* A sua aplicação necessita de criar ou destruir dinamicamente Dicionários ou Filas Fiáveis ou Serviços Inteiros em tempo de execução.
* Você precisa controlar programáticamente serviço de cópia de segurança fornecida pelo tecido e restaurar funcionalidades para o estado do seu serviço.
* A sua aplicação precisa de manter o histórico de mudanças para as suas unidades de estado.
* Pretende desenvolver ou consumir fornecedores estatais de terceiros, personalizados.

## <a name="next-steps"></a>Passos seguintes

* [Serviços fiáveis arranque rápido](service-fabric-reliable-services-quick-start.md)
* [Coleções fiáveis](service-fabric-reliable-services-reliable-collections.md)
* [O modelo de programação de Atores Fiáveis](service-fabric-reliable-actors-introduction.md)
