---
title: Transferências, bloqueios e liquidação de mensagens do barramento de serviço do Azure | Microsoft Docs
description: Visão geral de transferências de mensagens do barramento de serviço e operações de liquidação
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
ms.date: 09/25/2018
ms.author: aschhab
ms.openlocfilehash: 9aaada1ede8912b8b70f37c628ec918eca9be9d2
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676270"
---
# <a name="message-transfers-locks-and-settlement"></a>Transferências de mensagens, bloqueios e acordo

A capacidade central de um agente de mensagens, como o barramento de serviço, é aceitar mensagens em uma fila ou tópico e mantê-las disponíveis para recuperação posterior. *Send* é o termo que é comumente usado para a transferência de uma mensagem para o agente de mensagens. *Receive* é o termo normalmente usado para a transferência de uma mensagem para um cliente de recuperação.

Quando um cliente envia uma mensagem, ele geralmente deseja saber se a mensagem foi transferida corretamente e aceita pelo agente ou se ocorreu algum tipo de erro. Essa confirmação positiva ou negativa liquida o cliente e a compreensão do agente sobre o estado de transferência da mensagem e, portanto, é conhecida como *liquidação*.

Da mesma forma, quando o agente transfere uma mensagem para um cliente, o agente e o cliente desejam estabelecer um entendimento de se a mensagem foi processada com êxito e, portanto, pode ser removida ou se a entrega ou o processamento da mensagem falhou e, portanto, o a mensagem pode ter que ser entregue novamente.

## <a name="settling-send-operations"></a>Liquidando operações de envio

Usando qualquer um dos clientes de API do barramento de serviço com suporte, as operações de envio no barramento de serviço sempre são explicitamente liquidadas, o que significa que a operação da API aguarda um resultado de aceitação do barramento de serviço chegar e, em seguida, conclui a operação de envio.

Se a mensagem for rejeitada pelo barramento de serviço, a rejeição conterá um indicador de erro e um texto com um "ID de acompanhamento" dentro dele. A rejeição também inclui informações sobre se a operação pode ser repetida com qualquer expectativa de sucesso. No cliente, essas informações são transformadas em uma exceção e geradas para o chamador da operação de envio. Se a mensagem tiver sido aceita, a operação será concluída silenciosamente.

Ao usar o protocolo AMQP, que é o protocolo exclusivo para o cliente de .NET Standard e o cliente Java e [que é uma opção para o cliente de .NET Framework](service-bus-amqp-dotnet.md), as transferências de mensagens e as liquidações são canalizadas e completamente assíncronas, e é é recomendável que você use as variantes da API do modelo de programação assíncrona.

Um remetente pode colocar várias mensagens na conexão em uma rápida sucessão sem precisar esperar que cada mensagem seja confirmada, como seria o caso com o protocolo SBMP ou com HTTP 1,1. Essas operações de envio assíncronas são concluídas, pois as respectivas mensagens são aceitas e armazenadas, em entidades particionadas ou quando a operação de envio para diferentes entidades se sobrepõem. As conclusões também podem ocorrer fora da ordem de envio original.

A estratégia para lidar com o resultado de operações de envio pode ter um impacto de desempenho imediato e significativo para seu aplicativo. Os exemplos nesta seção são gravados C# e aplicados de maneira equivalente para os futuros do Java.

Se o aplicativo produzir intermitências de mensagens, ilustradas aqui com um loop simples e aguardar a conclusão de cada operação de envio antes de enviar a próxima mensagem, formas de API assíncronas e síncronas, o envio de 10 mensagens será concluído somente após 10 viagens de ida e volta completas sequenciais para liquidação.

Com uma distância de ida e volta TCP de 70 milissegundos presumida de um site local para o barramento de serviço e fornecendo apenas 10 ms para que o barramento de serviço aceite e armazene cada mensagem, o loop a seguir ocupa pelo menos 8 segundos, não contando o tempo de transferência de carga ou o potencial efeitos de congestionamento de rota:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Se o aplicativo iniciar as 10 operações de envio assíncrono em uma sucessão imediata e aguardar sua respectiva conclusão separadamente, o tempo de ida e volta para essas 10 operações de envio se sobrepõem. As 10 mensagens são transferidas em uma sucessão imediata, potencialmente até mesmo compartilhando quadros TCP, e a duração geral da transferência depende amplamente do tempo relacionado à rede que leva para obter as mensagens transferidas para o agente.

Fazendo as mesmas suposições para o loop anterior, o tempo total de execução sobreposto para o seguinte loop pode ficar bem abaixo de um segundo:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

É importante observar que todos os modelos de programação assíncrona usam alguma forma de fila de trabalho oculta, com base na memória, que mantém operações pendentes. Quando [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) ou **Send** (Java) retornam, a tarefa de envio é enfileirada nessa fila de trabalho, mas o gesto de protocolo só começa quando é a execução da tarefa. Para o código que tende a enviar picos de mensagens e onde a confiabilidade é uma preocupação, deve-se tomar cuidado para que muitas mensagens sejam colocadas "em trânsito" ao mesmo tempo, porque todas as mensagens enviadas ocupam memória até que cuidado tenham sido colocadas na conexão.

Os semáforos, conforme mostrado no trecho de código a C#seguir no, são objetos de sincronização que permitem essa limitação no nível do aplicativo quando necessário. Esse uso de um semáforo permite que no máximo 10 mensagens estejam em trânsito ao mesmo tempo. Um dos 10 bloqueios de semáforo disponíveis é feito antes do envio e é liberado à medida que o envio é concluído. O 11 passa pelo loop aguarda até que pelo menos um dos envios anteriores tenha sido concluído e, em seguida, torne seu bloqueio disponível:

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

Os aplicativos **nunca** devem iniciar uma operação de envio assíncrona de maneira "disparar e esquecer" sem recuperar o resultado da operação. Isso pode carregar a fila de tarefas interna e invisível até o esgotamento de memória e impedir que o aplicativo detecte erros de envio:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Com um cliente AMQP de baixo nível, o barramento de serviço também aceita transferências "previamente liquidadas". Uma transferência previamente liquidada é uma operação de disparar e esquecer para a qual o resultado, de qualquer forma, não é relatado de volta ao cliente e a mensagem é considerada liquidada quando enviada. A falta de comentários para o cliente também significa que não há dados acionáveis disponíveis para diagnóstico, o que significa que esse modo não se qualifica para ajuda por meio do suporte do Azure.

## <a name="settling-receive-operations"></a>Liquidando operações Receive

Para operações de recebimento, os clientes de API do barramento de serviço habilitam dois modos explícitos diferentes: *Receive-and-Delete* e *Peek-Lock*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

O modo de [recebimento e exclusão](/dotnet/api/microsoft.servicebus.messaging.receivemode) informa ao agente para considerar todas as mensagens que ele envia para o cliente de recebimento, conforme liquidado quando enviado. Isso significa que a mensagem é considerada consumida assim que o agente a coloca na conexão. Se a transferência da mensagem falhar, a mensagem será perdida.

A vantagem desse modo é que o receptor não precisa realizar uma ação adicional na mensagem e também não é retardado aguardando o resultado da liquidação. Se os dados contidos nas mensagens individuais tiverem valor baixo e/ou forem apenas significativos por um período muito curto, esse modo será uma opção razoável.

### <a name="peeklock"></a>PeekLock

O modo [Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) informa ao agente que o cliente receptor deseja liquidar as mensagens recebidas explicitamente. A mensagem é disponibilizada para o receptor processar, enquanto mantida em um bloqueio exclusivo no serviço para que outros destinatários concorrentes não possam vê-lo. A duração do bloqueio é definida inicialmente no nível da fila ou da assinatura e pode ser estendida pelo cliente que possui o bloqueio, por meio da operação [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) .

Quando uma mensagem é bloqueada, outros clientes que recebem da mesma fila ou assinatura podem assumir os bloqueios e recuperar as próximas mensagens disponíveis que não estão sob o bloqueio ativo. Quando o bloqueio em uma mensagem é liberado explicitamente ou quando o bloqueio expira, a mensagem é exibida na frente da ordem de recuperação para entrega.

Quando a mensagem é liberada repetidamente por receptores ou elas permitem que o bloqueio decorra um número definido de vezes ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), a mensagem é automaticamente removida da fila ou da assinatura e colocada na fila de mensagens mortas associada.

O cliente de recebimento inicia a liquidação de uma mensagem recebida com uma confirmação positiva quando chama a [conclusão](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) no nível da API. Isso indica ao agente que a mensagem foi processada com êxito e a mensagem é removida da fila ou assinatura. O agente responde à tentativa de liquidação do destinatário com uma resposta que indica se a liquidação pode ser executada.

Quando o cliente de recebimento não processa uma mensagem, mas deseja que a mensagem seja entregue novamente, ela pode pedir explicitamente que a mensagem seja liberada e desbloqueada instantaneamente chamando [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) ou não pode fazer nada e permitir que o bloqueio decorra.

Se um cliente de recebimento não processar uma mensagem e souber que a entrega da mensagem e a repetição da operação não ajudará, ela poderá rejeitar a mensagem, que a move para a fila de mensagens mortas chamando [mensagens mortas](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), o que também permite definir um Propriedade que inclui um código de motivo que pode ser recuperado com a mensagem da fila de mensagens mortas.

Um caso especial de liquidação é o adiamento, que é discutido em um artigo separado.

As operações **completas** ou de **mensagens mortas** , bem como as operações **RenewLock** podem falhar devido a problemas de rede, se o bloqueio mantido tiver expirado ou se houver outras condições do lado do serviço que impeçam a liquidação. Em um dos últimos casos, o serviço envia uma confirmação negativa que se superfícies como uma exceção nos clientes da API. Se o motivo for uma conexão de rede quebrada, o bloqueio será descartado, pois o barramento de serviço não oferece suporte à recuperação de links AMQP existentes em uma conexão diferente.

Se a **conclusão** falhar, que ocorre normalmente no final da manipulação de mensagens e, em alguns casos, após minutos de trabalho de processamento, o aplicativo de recebimento pode decidir se preserva o estado do trabalho e ignora a mesma mensagem quando ela é entregue uma segunda vez, ou se ele sai do resultado de trabalho e tenta novamente à medida que a mensagem é entregue novamente.

O mecanismo típico para identificar entregas de mensagens duplicadas é verificar a ID da mensagem, que pode e deve ser definido pelo remetente para um valor exclusivo, possivelmente alinhado com um identificador do processo de origem. Um Agendador de trabalhos provavelmente definiria a ID da mensagem para o identificador do trabalho que ele está tentando atribuir a um trabalhador com o determinado trabalhador, e o trabalho ignoraria a segunda ocorrência da atribuição de trabalho se esse trabalho já tiver sido feito.

> [!IMPORTANT]
> É importante observar que o bloqueio que o PeekLock adquire na mensagem é volátil e pode ser perdido nas seguintes condições
>   * Atualização de serviço
>   * Atualização do so
>   * Alterando propriedades na entidade (fila, tópico, assinatura) enquanto mantém o bloqueio.
>
> Quando o bloqueio for perdido, o barramento de serviço do Azure irá gerar um LockLostException que será exibido no código do aplicativo cliente. Nesse caso, a lógica de repetição padrão do cliente deve iniciar automaticamente e tentar a operação novamente.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
