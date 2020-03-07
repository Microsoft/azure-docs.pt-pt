---
title: Transferências, fechaduras e liquidação de mensagens de ônibus de serviço Azure
description: Este artigo fornece uma visão geral das transferências de mensagens, fechaduras e operações de liquidação de mensagens do Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: aschhab
ms.openlocfilehash: a2c353d612280981a83b32463d34efdc70878495
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396671"
---
# <a name="message-transfers-locks-and-settlement"></a>Transferências de mensagens, bloqueios e acordo

A capacidade central de um corretor de mensagens como o Service Bus é aceitar mensagens numa fila ou tópico e mantê-las disponíveis para posterior recuperação. *Enviar* é o termo que é comumente usado para a transferência de uma mensagem para o corretor de mensagens. *Receber* é o termo comumente usado para a transferência de uma mensagem para um cliente em recuperação.

Quando um cliente envia uma mensagem, normalmente quer saber se a mensagem foi devidamente transferida e aceite pelo corretor ou se ocorreu algum tipo de erro. Este reconhecimento positivo ou negativo liquida o cliente e o corretor compreensão sobre o estado de transferência da mensagem e é assim referido como *liquidação*.

Da mesma forma, quando o corretor transfere uma mensagem para um cliente, o corretor e o cliente querem estabelecer uma compreensão sobre se a mensagem foi processada com sucesso e pode, portanto, ser removida, ou se a entrega ou processamento da mensagem falhou, e assim o mensagem pode ter que ser entregue novamente.

## <a name="settling-send-operations"></a>Regularizar as operações de envio

Utilizando qualquer um dos clientes da Service Bus API suportados, o envio de operações para o Service Bus são sempre explicitamente liquidados, o que significa que a operação API aguarda a chegada de um resultado de aceitação do Service Bus e, em seguida, completa a operação de envio.

Se a mensagem for rejeitada pela Service Bus, a rejeição contém um indicador de erro e texto com um "tracking-id" no seu interior. A rejeição também inclui informações sobre se a operação pode ser novamente julgada com qualquer expectativa de sucesso. No cliente, esta informação é transformada em exceção e levantada ao chamador da operação de envio. Se a mensagem tiver sido aceite, a operação completa silenciosamente.

Ao utilizar o protocolo AMQP, que é o protocolo exclusivo para o cliente .NET Standard e o cliente Java e [que é uma opção para o cliente .NET Framework,](service-bus-amqp-dotnet.md)as transferências de mensagens e liquidações são pipelinee completamente assíncronas, e recomenda-se que utilize as variantes asynchronous do modelo de programação API.

Um remetente pode colocar várias mensagens no fio em rápida sucessão sem ter que esperar que cada mensagem seja reconhecida, como seria o caso do protocolo SBMP ou com HTTP 1.1. Estas operações de envio assíncronas completam-se à medida que as respetivas mensagens são aceites e armazenadas, em entidades divididas ou quando enviam operações a diferentes entidades sobrepostas. As conclusões também podem ocorrer fora da ordem de envio original.

A estratégia para lidar com o resultado das operações de envio pode ter um impacto de desempenho imediato e significativo para a sua aplicação. Os exemplos desta secção são C# escritos e aplicados de forma equivalente para a Java Futures.

Se a aplicação produzir explosões de mensagens, ilustradas aqui com um loop simples, e se aguardasse a conclusão de cada operação de envio antes de enviar a próxima mensagem, formas de API sincronizadas ou assíncronas, enviando 10 mensagens apenas após 10 sequencial de ida e volta completa para liquidação.

Com uma distância de ida e volta de 70 milisegundos de 70 milisegundos de ida e volta de um local para o Service Bus e dando apenas 10 ms para o Service Bus aceitar e armazenar cada mensagem, o seguinte loop demora pelo menos 8 segundos, sem contar o tempo de transferência de carga útil ou o potencial efeitos de congestionamento da rota:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Se o pedido iniciar as 10 operações de envio assíncronos em sucessão imediata e aguarda a sua respetiva conclusão separadamente, o tempo de ida e volta para essas 10 operações de envio sobrepõe-se. As 10 mensagens são transferidas em sucessão imediata, potencialmente até partilhando quadros de TCP, e a duração global da transferência depende em grande parte do tempo relacionado com a rede que leva para transferir as mensagens para o corretor.

Fazendo os mesmos pressupostos que para o ciclo anterior, o tempo total de execução sobreposto para o seguinte ciclo pode permanecer bem abaixo de um segundo:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

É importante notar que todos os modelos de programação assíncronos utilizam alguma forma de fila de trabalho escondida baseada na memória que mantém as operações pendentes. Quando [sendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) ()C#ou **Enviar** (Java) regressar, a tarefa de envio é em fila na fila de trabalho, mas o gesto de protocolo só começa quando é a vez da tarefa ser executada. Para um código que tende a empurrar explosões de mensagens e onde a fiabilidade é uma preocupação, há que ter cuidado para que não muitas mensagens sejam colocadas "em fuga" de uma só vez, porque todas as mensagens enviadas retiram a memória até que tenham sido factualmente colocadas no fio.

Os semáforos, como mostram os seguintes códigos, C#são objetos de sincronização que permitem tal estrangulamento ao nível da aplicação quando necessário. Este uso de um semáforo permite que, no máximo, 10 mensagens estejam em voo ao mesmo tempo. Um dos 10 cadeados de semáforo disponíveis é tomado antes do envio e é libertado à medida que o envio completa. O 11º passe através do loop aguarda até que pelo menos um dos envios anteriores esteja concluído, e depois disponibiliza o seu cadeado:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

As aplicações **nunca** devem iniciar uma operação de envio assíncrono de forma "fogo e esquecimento" sem recuperar o resultado da operação. Ao fazê-lo pode carregar a fila de tarefas interna e invisível até à exaustão da memória e impedir que a aplicação detete erros de envio:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Com um cliente AMQP de baixo nível, a Service Bus também aceita transferências "pré-liquidadas". Uma transferência pré-resolvida é uma operação de fogo e esquecimento para a qual o resultado, de qualquer forma, não é comunicado ao cliente e a mensagem é considerada resolvida quando enviada. A falta de feedback para o cliente também significa que não existem dados atol disponíveis para diagnósticos, o que significa que este modo não se qualifica para ajuda através do suporte do Azure.

## <a name="settling-receive-operations"></a>Regularização de operações de receção

Para as operações de receção, os clientes DaA do Ônibus de serviço permitem dois modos explícitos diferentes: *Receber e Eliminar* e Espreitar *.*

### <a name="receiveanddelete"></a>Receber EApagar

O modo [Receber e Eliminar](/dotnet/api/microsoft.servicebus.messaging.receivemode) diz ao corretor para considerar todas as mensagens que envia ao cliente recetor conforme resolvido quando enviado. Isto significa que a mensagem é considerada consumida assim que o corretor a colocar no fio. Se a transferência de mensagens falhar, a mensagem perde-se.

O lado positivo deste modo é que o recetor não precisa de tomar mais medidas sobre a mensagem e também não é abrandado à espera do resultado da liquidação. Se os dados contidos nas mensagens individuais tiverem baixo valor e/ou apenas significativos por um curto período de tempo, este modo é uma escolha razoável.

### <a name="peeklock"></a>PeekLock

O modo [Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) diz ao corretor que o cliente recetor quer resolver as mensagens recebidas explicitamente. A mensagem é disponibilizada para o recetor processar, enquanto está sob um bloqueio exclusivo no serviço, de modo a que outros recetores concorrentes não possam vê-la. A duração do bloqueio é inicialmente definida ao nível da fila ou da subscrição e pode ser prolongada pelo cliente que possui o bloqueio, através da operação [RenewLock.](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Quando uma mensagem está bloqueada, outros clientes que recebem da mesma fila ou subscrição podem pegar em fechaduras e recuperar as próximas mensagens disponíveis que não estão sob bloqueio ativo. Quando o bloqueio de uma mensagem é explicitamente libertado ou quando o bloqueio expira, a mensagem volta a aparecer na parte frontal da ordem de recuperação para reentrega.

Quando a mensagem é repetidamente divulgada pelos recetores ou deixam o bloqueio decorrer durante um número definido de vezes[(maxDeliveryCount),](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)a mensagem é automaticamente removida da fila ou subscrição e colocada na fila de letras mortas associada.

O cliente recetor inicia a liquidação de uma mensagem recebida com um reconhecimento positivo quando chama [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) ao nível da API. Isto indica ao corretor que a mensagem foi processada com sucesso e que a mensagem é removida da fila ou subscrição. O corretor responde à intenção de liquidação do recetor com uma resposta que indica se a liquidação poderia ser realizada.

Quando o cliente recetor não processa uma mensagem mas quer que a mensagem seja retransmitida, pode explicitamente pedir que a mensagem seja libertada e desbloqueada instantaneamente, ligando para [abandonar](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) ou não pode fazer nada e deixar o bloqueio decorrer.

Se um cliente recetor não processar uma mensagem e souber que reentregar a mensagem e retentar a operação não ajudará, pode rejeitar a mensagem, que a move para a fila da letra morta, chamando [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), que também permite definir uma propriedade personalizada, incluindo um código de razão que pode ser recuperado com a mensagem da fila da letra morta.

Um caso especial de liquidação é o adiamento, que é discutido num artigo separado.

As operações **Complete** ou **Deadletter,** bem como as operações **RenewLock** podem falhar devido a problemas de rede, se o bloqueio detido tiver expirado, ou se houver outras condições do lado do serviço que impedem a liquidação. Num dos últimos casos, o serviço envia um reconhecimento negativo que surge como uma exceção nos clientes da API. Se a razão for uma ligação de rede quebrada, o bloqueio é desativado uma vez que o Service Bus não suporta a recuperação das ligações AMQP existentes numa ligação diferente.

Se **a Complete** falhar, o que ocorre tipicamente no final do manuseamento de mensagens e em alguns casos após minutos de trabalho de processamento, o pedido recetor pode decidir se preserva o estado do trabalho e ignora a mesma mensagem quando é entregue uma segunda vez, ou se deita fora o resultado do trabalho e tenta novamente à medida que a mensagem é retransmitida.

O mecanismo típico de identificação de entregas de mensagens duplicadas é verificando o id de mensagem, que pode e deve ser definido pelo remetente para um valor único, possivelmente alinhado com um identificador do processo de origem. Um programador de trabalho provavelmente definiria o id de mensagem para o identificador do trabalho que está a tentar atribuir a um trabalhador com o trabalhador dado, e o trabalhador ignoraria a segunda ocorrência da atribuição de trabalho se esse trabalho já estivesse feito.

> [!IMPORTANT]
> É importante notar que o bloqueio que peekLock adquire na mensagem é volátil e pode ser perdido nas seguintes condições
>   * Atualização do serviço
>   * Atualização do OS
>   * Alterar propriedades na entidade (Fila, Tópico, Subscrição) enquanto segura o bloqueio.
>
> Quando o bloqueio for perdido, o Azure Service Bus gerará uma LockLostException que será divulgada no código de aplicação do cliente. Neste caso, a lógica de retry padrão do cliente deve entrar automaticamente e voltar a tentar a operação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
