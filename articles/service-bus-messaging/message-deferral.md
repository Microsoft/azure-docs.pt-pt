---
title: Azure Service Bus - adiamento de mensagem
description: Este artigo explica como adiar a entrega de mensagens Azure Service Bus. A mensagem permanece na fila ou subscrição, mas está reservada.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 171fc6e1a3c779802747d34ac631d265e8c8926f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869496"
---
# <a name="message-deferral"></a>Diferimento de mensagens
Quando um cliente de fila ou subscrição recebe uma mensagem que está disposto a processar, mas o processamento não é atualmente possível devido a circunstâncias especiais, tem a opção de "adiar" a recuperação da mensagem para um ponto posterior. A mensagem permanece na fila ou subscrição, mas está reservada.

> [!NOTE]
> As mensagens diferidas não serão automaticamente transferidas para a fila da carta morta após a [sua expiração](./service-bus-dead-letter-queues.md#time-to-live). Este comportamento é por desígnio.

## <a name="sample-scenarios"></a>Cenários de exemplo
O diferimento é uma funcionalidade criada especificamente para cenários de processamento de fluxo de trabalho. Os quadros de fluxo de trabalho podem exigir que determinadas operações sejam processadas numa determinada ordem. Podem ter de adiar o processamento de algumas mensagens recebidas até que o trabalho prévio prescrito que seja informado por outras mensagens esteja concluído.

Um simples exemplo ilustrativo é uma sequência de processamento de encomendas em que uma notificação de pagamento de um prestador de pagamento externo aparece num sistema antes de a ordem de compra correspondente ter sido propagada da frente da loja para o sistema de realização. Nesse caso, o sistema de realização pode adiar o processamento da notificação de pagamento até que haja uma ordem para associá-la. Em cenários de encontro, em que mensagens de diferentes fontes impulsionam um fluxo de trabalho para a frente, a ordem de execução em tempo real pode mesmo estar correta, mas as mensagens que refletem os resultados podem chegar fora de ordem.

Em última análise, os auxiliares de diferimento na reordenação das mensagens da ordem de chegada para uma ordem em que podem ser processadas, deixando essas mensagens em segurança na loja de mensagens para as quais o processamento precisa de ser adiado.

Se uma mensagem não puder ser processada porque um recurso específico para o manuseamento dessa mensagem está temporariamente indisponível, mas o processamento de mensagens não deve ser sumariamente suspenso, uma forma de colocar essa mensagem de lado durante alguns minutos é lembrar o número de sequência numa [mensagem programada](message-sequencing.md) a ser publicada em poucos minutos, e recuperar a mensagem diferida quando a mensagem programada chegar. Se um manipulador de mensagens depender de uma base de dados para todas as operações e essa base de dados estiver temporariamente indisponível, não deve usar o adiamento, mas sim suspender a receção de mensagens completamente até que a base de dados esteja novamente disponível. 

## <a name="retrieving-deferred-messages"></a>Recuperação de mensagens diferidas
As mensagens diferidas permanecem na fila principal juntamente com todas as outras mensagens ativas (ao contrário de mensagens de letra morta que vivem num subqueue), mas já não podem ser recebidas usando as operações de receção regulares. As mensagens diferidas podem ser descobertas através da [navegação por mensagens](message-browsing.md) se uma aplicação perder o controlo das mesmos.

Para recuperar uma mensagem diferida, o seu proprietário é responsável por lembrar o número da sequência à medida que a adia. Qualquer recetor que conheça o número de sequência de uma mensagem diferida pode receber a mensagem utilizando métodos de receção que tomam o número da sequência como parâmetro. Para obter mais informações sobre os números de sequência, consulte [sequenciação de mensagens e semões de tempo](message-sequencing.md).

## <a name="next-steps"></a>Passos seguintes
Experimente as amostras no idioma à sua escolha para explorar as funcionalidades do Azure Service Bus. 

- [Amostras da biblioteca de clientes do Azure Service Bus para Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus client library samples for Python](/samples/azure/azure-sdk-for-python/servicebus-samples/) - veja a amostra **receive_deferred_message_queue.py.** 
- [Amostras da biblioteca de clientes Azure Service Bus para JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/) - consulte a amostra **avançada/deferral.js.** 
- [Amostras da biblioteca de clientes Azure Service Bus para TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/) - consulte a amostra **avançada/diferimento.ts.** 
- [Amostras Azure.Messaging.ServiceBus para .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - Consulte a amostra **de Mensagens de Assentamento.** 

Encontre amostras para as bibliotecas clientes mais antigas .NET e Java abaixo:
- [Microsoft.Azure.ServiceBus amostras para .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - Ver a amostra **de Diferimento.** 
- [amostras de azure-servicebus para Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
