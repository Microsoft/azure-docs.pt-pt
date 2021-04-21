---
title: Filas de cartas mortas do Autocarro de Serviço | Microsoft Docs
description: Descreve filas de cartas mortas no Azure Service Bus. As filas de autocarros de serviço e as subscrições de tópicos fornecem um subqueue secundário, chamado de fila de letras mortas.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: cb791982b50d7afff7b74d70adfd285bb5e0a11c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773230"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Visão geral das filas de cartas mortas do Service Bus

As filas de autocarros da Azure Service e as subscrições de tópicos fornecem um subqueue secundário, chamado de *fila de letras mortas* (DLQ). A fila de cartas mortas não precisa de ser explicitamente criada e não pode ser eliminada ou gerida independentemente da entidade principal.

Este artigo descreve filas de cartas mortas no Service Bus. Grande parte da discussão é ilustrada pela amostra das filas da [Carta Morta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) no GitHub.
 
## <a name="the-dead-letter-queue"></a>A fila da carta morta

O objetivo da fila da carta morta é guardar mensagens que não podem ser entregues a nenhum recetor, ou mensagens que não possam ser processadas. As mensagens podem então ser removidas do DLQ e inspecionadas. Uma aplicação pode, com a ajuda de um operador, corrigir problemas e reenviar a mensagem, registar o facto de que houve um erro e tomar medidas corretivas. 

Do ponto de vista da API e do protocolo, o DLQ é maioritariamente semelhante a qualquer outra fila, exceto que as mensagens só podem ser enviadas através da operação de letra morta da entidade-mãe. Além disso, o tempo de viver não é observado, e não se pode escrever uma mensagem de um DLQ. A fila da carta morta suporta totalmente a entrega de espremiações e operações transacionais.

Não há limpeza automática do DLQ. As mensagens permanecem no DLQ até que as recupere explicitamente do DLQ e complete a mensagem da letra morta.


## <a name="dlq-message-count"></a>Contagem de mensagens DLQ
Não é possível obter a contagem de mensagens na fila das letras mortas ao nível do tópico. Isso é porque as mensagens não se sentam ao nível do tópico a menos que o Service Bus atire um erro interno. Em vez disso, quando um remetente envia uma mensagem para um tópico, a mensagem é reencaminhada para subscrições para o tema dentro de milissegundos e, portanto, já não reside ao nível do tópico. Assim, pode ver mensagens no DLQ associadas à subscrição do tópico. No exemplo seguinte, o **Service Bus Explorer** mostra que existem atualmente 62 mensagens no DLQ para a subscrição "test1". 

![Contagem de mensagens DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Também pode obter a contagem de mensagens DLQ utilizando o comando Azure CLI: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) . 

## <a name="moving-messages-to-the-dlq"></a>Mensagens de deslocação para o DLQ
Existem várias atividades no Service Bus que fazem com que as mensagens sejam empurradas para o DLQ a partir do próprio motor de mensagens. Uma aplicação também pode mover explicitamente mensagens para o DLQ. As duas propriedades seguintes (razão de letra morta e descrição da letra morta) são adicionadas a mensagens com letras mortas. As aplicações podem definir os seus próprios códigos para a propriedade reason da letra morta, mas o sistema define os seguintes valores.

| Razão da letra morta | Descrição do erro da letra morta |
| --- | --- |
|HeaderSizeExceed |A quota de tamanho deste fluxo foi excedida. |
|TTLExpiredException |A mensagem expirou e foi classificada como não entregue. Consulte [a secção "Hora" para mais](#time-to-live) detalhes. |
|A identificação da sessão é nula. |A entidade com sessão ativada não permite uma mensagem cujo identificador de sessão seja nulo. |
|MaxTransferHopCountExceed | O número máximo de lúpulo permitido ao encaminhar entre filas. O valor está definido para 4. |
| MaxDeliveryCountExceedededEdEdExceptionMessage | A mensagem não podia ser consumida após as tentativas máximas de entrega. Consulte a secção [contagem máxima de entrega](#maximum-delivery-count) para mais detalhes. |

## <a name="maximum-delivery-count"></a>Contagem máxima de entrega
Existe um limite para o número de tentativas de entrega de mensagens para filas e subscrições do Service Bus. O valor predefinido é 10. Sempre que uma mensagem foi entregue sob um bloqueio de espreitar, mas foi explicitamente abandonada ou o bloqueio expirou, a contagem de entrega da mensagem é incrementada. Quando a contagem de entregas exceder o limite, a mensagem é transferida para o DLQ. A razão da letra morta para a mensagem em DLQ está definida para: MaxDeliveryCountExceededed. Este comportamento não pode ser desativado, mas pode definir a contagem máxima de entrega para um grande número.

## <a name="time-to-live"></a>Time to live
Quando ativa a inscrição em filas ou subscrições, todas as mensagens caducadas são transferidas para o DLQ. O código de razão da letra morta está definido para: TTLExpiredException.

As mensagens expiradas são apenas purgadas e transferidas para o DLQ quando há pelo menos um recetor ativo a puxar da fila principal ou subscrição. As mensagens diferidas também não serão expurgadas e transferidas para a fila da carta morta após o seu termo. Este comportamento é propositado.

## <a name="errors-while-processing-subscription-rules"></a>Erros durante o processamento das regras de subscrição
Se ativar a inscrição em exceções de avaliação de filtros, quaisquer erros que ocorram enquanto a regra do filtro SQL de uma subscrição executa são capturados no DLQ juntamente com a mensagem ofensiva. Não utilize esta opção num ambiente de produção em que nem todos os tipos de mensagens tenham assinantes.

## <a name="application-level-dead-lettering"></a>Letras mortas ao nível da aplicação
Além das funcionalidades de letras mortas fornecidas pelo sistema, as aplicações podem usar o DLQ para rejeitar explicitamente mensagens inaceitáveis. Podem incluir mensagens que não podem ser processadas corretamente devido a qualquer tipo de problema do sistema, mensagens que possuam cargas mal formadas ou mensagens que falham na autenticação quando é utilizado um esquema de segurança ao nível da mensagem.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Letras mortas em cenários ForwardTo ou SendVia
As mensagens serão enviadas para a fila de transferência de cartas mortas nas seguintes condições:

- Uma mensagem passa por mais de quatro filas ou tópicos que estão [acorrentados em conjunto.](service-bus-auto-forwarding.md)
- A fila de destino ou tópico é desativado ou eliminado.
- A fila de destino ou tópico excede o tamanho máximo da entidade.

## <a name="path-to-the-dead-letter-queue"></a>Caminho para a fila da letra morta
Pode aceder à fila da letra morta utilizando a seguinte sintaxe:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as filas do Service Bus, consulte os seguintes artigos:

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Filas de Azure e filas de autocarros de serviço comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
