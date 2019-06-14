---
title: Transferências de mensagem, bloqueios e acordo do Service Bus do Azure | Documentos da Microsoft
description: Descrição geral das transferências de mensagem do Service Bus e as operações de liquidação
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
ms.openlocfilehash: a78409a15acb4e60fc4200778d0f33b3fb566e85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60403946"
---
# <a name="message-transfers-locks-and-settlement"></a>Transferências de mensagens, bloqueios e acordo

A função central do Mediador de mensagens, como o Service Bus é aceite mensagens numa fila ou tópico e armazená-los disponíveis para recuperação posterior. *Enviar* é o termo que é geralmente utilizado para a transferência de uma mensagem para o Mediador de mensagens. *Receber* é o termo geralmente utilizado para a transferência de uma mensagem para um cliente ao obter.

Quando um cliente envia uma mensagem, geralmente quer saber se a mensagem foi corretamente transferida para e aceite pelo broker ou se algum tipo de erro ocorreu. Esta confirmação positiva ou negativa liquida o cliente e o Mediador de compreender sobre o estado de transferência da mensagem e, portanto, é referida como *liquidação*.

Da mesma forma, quando o agente de transferência de uma mensagem a um cliente, o agente e o cliente desejam compreender se a mensagem foi processada com êxito e, portanto, pode ser removida, ou se o processamento ou de entrega de mensagens falha e, portanto, o mensagem poderá ter de ser entregues novamente.

## <a name="settling-send-operations"></a>Liquidação de operações de envio

Utilizar qualquer um dos clientes de API do Service Bus suportados, enviar operações para o Service Bus sempre explicitamente são liquidadas, o que significa que a operação de API aguarda um resultado de aceitação do Service Bus chegam e, em seguida, concluir a operação de envio.

Se a mensagem será rejeitada pelo Service Bus, a rejeição contém um indicador de erro e o texto com um "-id de controlo" dentro dela. A rejeição também inclui informações sobre se a operação pode ser repetida com qualquer expectativa de sucesso. No cliente, esta informação é transformada numa exceção e gerada para o chamador da operação de envio. Se o ter sido aceite a mensagem, a operação for concluída silenciosamente.

Ao utilizar o protocolo AMQP, que é o protocolo exclusivo para o cliente .NET Standard e o cliente de Java e [que é uma opção para o cliente do .NET Framework](service-bus-amqp-dotnet.md), transferências de mensagem e liquidações são pipelined e completamente assíncrona, e é recomendado que utilize as variantes de API de modelo programação assíncronas.

Um remetente pode colocar várias mensagens na conexão numa rápida sucessão sem ter de esperar por cada mensagem ser confirmada, como seria o caso com o protocolo SBMP ou com HTTP 1.1. Essas operações de envio assíncrono concluir uma vez que as mensagens respectivas são aceites e armazenadas em entidades particionadas ou quando enviam a operação para a sobreposição de entidades diferentes. As conclusões também poderão ocorrer fora de ordem de envio original.

A estratégia para processar o resultado de operações de envio pode ter impacto no desempenho de imediato e significativa para a sua aplicação. Os exemplos nesta secção são escritos em c# e aplicam de maneira equivalente para Java futura.

Se o aplicativo produz picos de mensagens, ilustrado aqui com um simples loop e tivesse de aguardar a conclusão de cada enviar operação antes de enviar a mensagem seguinte, síncrona ou assíncronas formas de API semelhantes, apenas 10 mensagens a enviar é concluída após 10 sequencial completa ida e volta para liquidação.

Com um milissegundo de 70 presumível distance de latência de ida e volta do TCP partir de um site no local para o Service Bus e dando apenas 10 ms para o Service Bus para aceitar e armazenar cada mensagem, o seguinte loop ocupa, pelo menos, 8 segundos, sem contar o tempo de transferência de payload ou potencial efeitos de congestionamento de rota:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Se o aplicativo é iniciado 10 operações de envio assíncrono em sucessão imediata e awaits seus respectivo conclusão separadamente, sobrepõe-se o tempo de ida e volta para essas operações de envio de 10. As 10 mensagens são transferidas sucessivamente imediata, potencialmente, até partilha quadros TCP, e a duração de transferência geral depende em grande parte do tempo de relacionada com a rede de mensagens em fila que demora a obter as mensagens transferidas para o mediador.

Fazer as mesmo suposições quanto o loop anterior, o tempo de execução sobreposto total para o seguinte loop poderão permanecer menos de um segundo:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

É importante ter em atenção que todos os modelos de programação assíncrona usar algum tipo de fila de trabalho baseados em memória e oculto que mantém operações pendentes. Quando [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (c#) ou **enviar** retorno (Java), a tarefa de envio é colocado na fila trabalho, mas o gesto de protocolo começa apenas uma vez que é a vez da tarefa para ser executado. Para o código que tende a picos de mensagens e onde a confiabilidade é uma preocupação de push, deve ter cuidado que não demasiadas mensagens são colocadas "em trânsito", ao mesmo tempo, uma vez que todas as mensagens enviadas ocupam memória até que eles factually foi colocados em trânsito.

Os semáforos, conforme mostrado no seguinte fragmento de código em c#, são objetos de sincronização que permitem essa limitação de nível de aplicativo quando necessário. Permite que esta utilização de um semáforo para um máximo de 10 mensagens estejam em trânsito ao mesmo tempo. Um dos bloqueios semáforo disponíveis 10 está a ser utilizado antes da enviar e lançado como o envio for concluída. O 11 pass-through as esperas de loop até que pelo menos uma do antes envia foi concluída e, em seguida, disponibiliza seu bloqueio:

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

Aplicativos devem **nunca** iniciar uma operação de envio assíncrono de uma forma de "disparar e esquecer" sem recuperar o resultado da operação. Se o fizer, pode carregar a fila de tarefas internas e invisível até esgotamento da memória e impedir que a aplicação detetar erros de envio:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Com um cliente AMQP nível baixo, do Service Bus também aceita transferências "previamente liquidadas". Uma transferência previamente liquidada é uma operação de disparar e esquecer de mensagens em fila para o qual o resultado, de qualquer forma, não é informado de volta ao cliente e a mensagem é considerada liquidados quando enviada. A falta de comentários para o cliente também significa que existe sem dados passíveis de ação para obter um diagnóstico, que significa que este modo não se qualifica para obter ajuda através do suporte do Azure.

## <a name="settling-receive-operations"></a>Liquidação de receber operações

Para receber operações, os clientes de API do Service Bus permitem dois modos diferentes de explícitos: *Receber-e-Delete* e *bloqueio de pré-visualização*.

O [Receive e eliminação](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo informa o mediador a ter em consideração todas as mensagens que envia para o cliente de recebimento como liquidados quando enviada. Isso significa que a mensagem é considerada consumidos assim que o mediador tem colocá-la em trânsito. Se falhar a transferência de mensagem, esta será perdida.

A vantagem desse modo é que o recetor não precisa de realizar mais nenhuma ação na mensagem e também não diminuiu ao aguardar o resultado da liquidação. Se os dados contidos nas mensagens individuais têm valor baixo e/ou apenas são significativos para muito pouco tempo, esse modo é uma opção razoável.

O [bloqueio de pré-visualização](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo informa o Mediador de que o cliente de recebimento deseja liquidar as mensagens recebidas explicitamente. A mensagem é disponibilizada para o recetor processar, enquanto mantido num bloqueio exclusivo no serviço para que outras, concorrentes recetores não é possível vê-lo. A duração do bloqueio inicialmente é definida ao nível da fila ou subscrição e podem ser estendida pelo cliente ao proprietário do bloqueio, através da [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) operação.

Quando uma mensagem está bloqueada, outros clientes receber a partir da mesma fila ou subscrição podem assumir bloqueios e obter as mensagens disponíveis seguintes não está sob o bloqueio de Active Directory. Quando o bloqueio numa mensagem explicitamente for lançado, ou quando o bloqueio expira, a mensagem será exibida uma cópia de segurança em ou próximo início da ordem de obtenção de reentrega.

Quando a mensagem repetidamente é lançada pela recetores ou permitem que o bloqueio de decorrer para um determinado número de vezes ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), a mensagem é automaticamente removida a partir da fila ou subscrição e colocada em associada fila de mensagens não entregues.

O cliente de recebimento inicia liquidação de uma mensagem recebida com uma confirmação positiva, ao chamar [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) no nível da API. Isso indica para o Mediador de que a mensagem foi processada com êxito e a mensagem é removida a partir da fila ou subscrição. O mediador responde a intenção de liquidação do receptor com uma resposta que indica se foi possível efetuar a liquidação.

Quando o cliente recetora não consegue processar uma mensagem, mas quer que a mensagem a ser reenviada, ele pode explicitamente solicitar a mensagem a ser lançado e desbloqueado instantaneamente chamando [abandonar](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) ou ele pode fazer nada e deixar que o bloqueio de decorrer.

Se um cliente recetora não consegue processar uma mensagem e sabe que entregando-se novamente a mensagem e repetir a operação não ajudará, ele pode rejeitar a mensagem, que move-o na fila de mensagens não entregues ao chamar [mensagens não entregues](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), que também permite definir uma propriedade personalizada, incluindo um código de razão que pode ser obtido com a mensagem da fila de mensagens não entregues.

Um caso especial de liquidação é diferimento, que é abordado num artigo separado.

O **concluída** ou **mensagens não entregues** operações, bem como o **RenewLock** operações poderão falhar devido a problemas de rede, se o bloqueio de retenção tiver expirado, ou se houver outras condições de lado do serviço que impedem a liquidação. Em um dos casos esse último, o serviço envia uma confirmação negativa que analisa como uma exceção nos clientes de API. Se o motivo pelo qual uma ligação de rede quebrada, o bloqueio foi removido uma vez que o barramento de serviço não suporta a recuperação de ligações AMQP existentes numa conexão diferente.

Se **Complete** falhar, o que ocorre normalmente no final de processamento de mensagens e, em alguns casos depois de minutos de trabalho de processamento, o aplicativo de recebimento pode decidir se preserva o estado do trabalho e ignora o mesmo mensagem quando ela é entregue uma segunda vez, ou se partir o resultado do trabalho e repete como a mensagem é reenviada.

O mecanismo típico para identificar mensagens duplicadas entregas é verificando o id da mensagem, que pode e deve ser definido pelo remetente para um valor exclusivo, possivelmente alinhado com um identificador do processo de origem. Um programador de tarefas provavelmente iria definir o id da mensagem para o identificador da tarefa está a tentar atribuir a uma função de trabalho com a função de trabalho especificada e a função de trabalho ignoraria a segunda ocorrência da atribuição de tarefa se essa tarefa já foi feita.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
