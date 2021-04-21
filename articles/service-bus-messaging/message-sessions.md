---
title: Sessões de mensagem de autocarro da Azure Service | Microsoft Docs
description: Este artigo explica como usar sessões para permitir o manuseamento conjunto e ordenado de sequências ilimitadas de mensagens relacionadas.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: e22dfb2aa7372a227f70fd2bfa8f72d2161cda17
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750757"
---
# <a name="message-sessions"></a>Sessões de mensagens
As sessões de ônibus de serviço da Microsoft Azure permitem o manuseamento conjunto e ordenado de sequências ilimitadas de mensagens relacionadas. As sessões podem ser usadas **em primeiro lugar, primeiro fora (FIFO)** e padrões **de resposta a pedidos.** Este artigo mostra como usar sessões para implementar estes padrões ao usar o Service Bus. 

> [!NOTE]
> O nível básico do Service Bus não suporta sessões. As sessões de suporte standard e premium. Para obter diferenças entre estes níveis, consulte [os preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Primeiro a entrar, primeiro padrão fora (FIFO)
Para realizar uma garantia FIFO em Service Bus, use sessões. O Service Bus não é prescritivo sobre a natureza da relação entre as mensagens, e também não define um modelo específico para determinar onde uma sequência de mensagens começa ou termina.

Qualquer remetente pode criar uma sessão ao enviar mensagens para um tópico ou fila, definindo a propriedade **de ID** da sessão para algum identificador definido por aplicação que seja exclusivo da sessão. Ao nível do protocolo AMQP 1.0, este valor mapeia para a propriedade *de id de grupo.*

Em filas ou subscrições conscientes da sessão, surgem sessões quando há pelo menos uma mensagem com o ID da sessão. Uma vez que uma sessão existe, não há tempo definido ou API para quando a sessão expirar ou desaparecer. Teoricamente, uma mensagem pode ser recebida para uma sessão de hoje, a próxima mensagem dentro de um ano, e se o ID da sessão corresponder, a sessão é a mesma do ponto de vista do Service Bus.

Normalmente, no entanto, uma aplicação tem uma noção clara de onde um conjunto de mensagens relacionadas começa e termina. O Service Bus não estabelece regras específicas. Por exemplo, a sua aplicação poderia definir a propriedade **Label** para a primeira mensagem **a iniciar**, para mensagens intermédias para **o conteúdo**, e para que a última mensagem **terminasse**. A posição relativa das mensagens de conteúdo pode ser calculada como a mensagem atual *SequenceNumber* delta a partir da mensagem **de início** *SequenceNumber*.

> [!IMPORTANT]
> Quando as Sessões são ativadas numa fila ou numa subscrição, as aplicações do cliente ***já não*** podem enviar/receber mensagens regulares. Todas as mensagens devem ser enviadas como parte de uma sessão (definindo o id da sessão) e recebidas aceitando a sessão.

As APIs para sessões existem em clientes de fila e subscrição. Há um modelo imperativo que controla quando se recebem sessões e mensagens, e um modelo baseado em manipuladores que esconde a complexidade da gestão do ciclo de receção. 

Para amostras, utilize links na secção [etapas seguintes.](#next-steps) 

### <a name="session-features"></a>Características da sessão

As sessões proporcionam uma des multiplexing simultânea de streams de mensagens intercaladas, preservando e garantindo a entrega ordenada.

![Um diagrama que mostra como o recurso Sessions preserva a entrega ordenada.][1]

Um recetor de sessão é criado por um cliente que aceita uma sessão. Quando a sessão é aceite e realizada por um cliente, o cliente detém um bloqueio exclusivo em todas as mensagens com o ID da **sessão** na fila ou subscrição. Também irá manter fechaduras exclusivas em todas as mensagens com o ID da **sessão** que chegará mais tarde.

O bloqueio é libertado quando se ligam para os métodos relacionados próximos no recetor ou quando a fechadura expira. Existem métodos no recetor para renovar as fechaduras também. Em vez disso, pode utilizar a função de renovação automática do bloqueio, onde pode especificar a duração do tempo para a qual pretende continuar a renovar o bloqueio. O bloqueio de sessão deve ser tratado como um bloqueio exclusivo num ficheiro, o que significa que a aplicação deve fechar a sessão assim que deixar de precisar e/ou não esperar mais mensagens.

Quando vários recetores simultâneos retiram da fila, as mensagens pertencentes a uma determinada sessão são enviadas para o recetor específico que atualmente mantém o bloqueio para essa sessão. Com esta operação, um fluxo de mensagens intercalada numa fila ou subscrição é des multiplexed de forma limpa para diferentes recetores e esses recetores também podem viver em diferentes máquinas clientes, uma vez que a gestão do bloqueio acontece do lado do serviço, dentro do Service Bus.

A ilustração anterior mostra três recetores de sessão simultânea. Uma Sessão com `SessionId` = 4 não tem cliente ativo e possuido, o que significa que nenhuma mensagem é entregue a partir desta sessão específica. Uma sessão age de muitas maneiras como uma sub-fila.

O bloqueio de sessão mantido pelo recetor da sessão é um guarda-chuva para as fechaduras de mensagem utilizadas pelo modo de assentamento *de bloqueio de espreitar.* Apenas um recetor pode ter um bloqueio numa sessão. Um recetor pode ter muitas mensagens a bordo, mas as mensagens serão recebidas por ordem. O abandono de uma mensagem faz com que a mesma mensagem seja novamente servida com a próxima operação de receção.

### <a name="message-session-state"></a>Estado da sessão de mensagens

Quando os fluxos de trabalho são processados em sistemas de nuvem de alta escala e de alta disponibilidade, o manipulador de fluxos de trabalho associado a uma determinada sessão deve ser capaz de recuperar de falhas inesperadas e pode retomar trabalhos parcialmente concluídos em um processo ou máquina diferente de onde o trabalho começou.

O estado de sessão permite uma anotação definida por aplicação de uma sessão de mensagens dentro do corretor, de modo a que o estado de processamento registado em relação a essa sessão fique imediatamente disponível quando a sessão é adquirida por um novo processador.

Do ponto de vista do Service Bus, o estado da sessão de mensagens é um objeto binário opaco que pode conter dados do tamanho de uma mensagem, que é de 256 KB para Service Bus Standard e 1 MB para Service Bus Premium. O estado de processamento relativo a uma sessão pode ser realizado dentro do estado da sessão, ou o estado da sessão pode indicar algum local de armazenamento ou registo de base de dados que detenha essa informação.

Os métodos de gestão do estado da sessão, SetState e GetState, podem ser encontrados no objeto recetor da sessão. Uma sessão que anteriormente não tinha estado de sessão retorna uma referência nula para o GetState. O estado de sessão previamente definido pode ser apurado passando nulo para o método SetState no recetor.

O estado da sessão permanece enquanto não estiver esclarecido (devolver **nulo),** mesmo que todas as mensagens numa sessão sejam consumidas.

O estado da sessão realizado em fila ou numa subscrição conta para a quota de armazenamento dessa entidade. Quando a aplicação termina com uma sessão, recomenda-se, por isso, que o pedido de limpeza do estado retido seja mantido para evitar custos de gestão externos.

### <a name="impact-of-delivery-count"></a>Impacto da contagem de entregas

A definição de contagem de entrega por mensagem no contexto das sessões varia ligeiramente em parte da definição na ausência de sessões. Aqui está uma tabela que resume quando a contagem de entrega é incrementada.

| Scenario | É a contagem de entrega da mensagem incrementada |
|----------|---------------------------------------------|
| A sessão é aceite, mas o bloqueio da sessão expira (devido ao tempo limite) | Yes |
| A sessão é aceite, as mensagens dentro da sessão não estão concluídas (mesmo que estejam bloqueadas), e a sessão está fechada | No |
| Sessão é aceite, as mensagens são completadas, e então a sessão é explicitamente fechada | N/A (É o fluxo padrão. Aqui as mensagens são removidas da sessão) |

## <a name="request-response-pattern"></a>Padrão de resposta a pedidos
O [padrão de resposta ao pedido](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) é um padrão de integração bem estabelecido que permite ao remetente enviar um pedido e fornece uma forma de o recetor enviar corretamente uma resposta ao pedido do remetente. Este padrão normalmente precisa de uma fila de curta duração ou tópico para a aplicação para enviar respostas. Neste cenário, as sessões proporcionam uma solução alternativa simples com semântica comparável. 

Várias aplicações podem enviar os seus pedidos para uma única fila de pedidos, com um parâmetro específico do cabeçalho definido para identificar exclusivamente a aplicação do remetente. A aplicação do recetor pode processar os pedidos que chegam na fila e enviar respostas na fila ativada, definindo o ID da sessão para o identificador único que o remetente tinha enviado na mensagem de pedido. O pedido que enviou o pedido pode então receber mensagens no ID de sessão específica e processar corretamente as respostas.

> [!NOTE]
> O pedido que envia os pedidos iniciais deve saber sobre o ID da sessão e usá-lo para aceitar a sessão para que a sessão em que espera a resposta esteja bloqueada. É uma boa ideia usar um GUID que identifica exclusivamente a instância da aplicação como um id de sessão. Não deve haver um manipulador de sessão ou um tempo limite especificado no recetor da sessão para a fila para garantir que as respostas estão disponíveis para serem bloqueadas e processadas por recetores específicos.

## <a name="next-steps"></a>Passos seguintes
Pode ativar sessões de mensagens enquanto cria uma fila utilizando o portal Azure, PowerShell, CLI, Resource Manager, .NET, Java, Python e JavaScript. Para obter mais informações, consulte [Ativar as sessões de mensagens](enable-message-sessions.md). 

Experimente as amostras no idioma à sua escolha para explorar as funcionalidades do Azure Service Bus. 

- [Amostras da biblioteca de clientes do Azure Service Bus para Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Amostras da biblioteca do cliente do Azure Service Bus para Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Amostras de biblioteca de clientes do Azure Service Bus para JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Amostras de biblioteca de clientes do Azure Service Bus para TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Amostras de Azure.Messaging.ServiceBus para .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Encontre amostras para as bibliotecas clientes mais antigas .NET e Java abaixo:
- [Amostras microsoft.Azure.ServiceBus para .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [amostras de azure-servicebus para Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

