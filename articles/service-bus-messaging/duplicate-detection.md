---
title: Detecção de mensagens duplicadas do barramento de serviço do Azure | Microsoft Docs
description: Detectar mensagens duplicadas do barramento de serviço
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 4cd5fc50c35f4c4adb63c9d91af05dcf8b2dda40
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924645"
---
# <a name="duplicate-detection"></a>Deteção de duplicados

Se um aplicativo falhar devido a um erro fatal imediatamente após o envio de uma mensagem e a instância do aplicativo reiniciada erroneamente acreditar que a entrega da mensagem anterior não ocorreu, um envio subsequente faz com que a mesma mensagem apareça no sistema duas vezes.

Também é possível que um erro no nível do cliente ou da rede ocorra um momento antes e que uma mensagem enviada seja confirmada na fila, com a confirmação não retornada com êxito ao cliente. Esse cenário deixa o cliente em dúvida sobre o resultado da operação de envio.

A detecção de duplicidades elimina as dúvidas dessas situações, permitindo que o remetente reenvie a mesma mensagem e a fila ou o tópico descartará as cópias duplicadas.

Habilitar a detecção de duplicidades ajuda a manter o controle da *MessageId* controlada por aplicativo de todas as mensagens enviadas para uma fila ou tópico durante uma janela de tempo especificada. Se qualquer mensagem nova for enviada com *MessageId* que foi registrada durante a janela de tempo, a mensagem será relatada como aceita (a operação de envio é realizada com êxito), mas a mensagem recém enviada é imediatamente ignorada e descartada. Nenhuma outra parte da mensagem que não seja a *MessageId* é considerada.

O controle de aplicativo do identificador é essencial, pois apenas isso permite que o aplicativo vincule *MessageId* a um contexto de processo de negócios do qual ele pode ser reconstruído de forma previsível quando ocorre uma falha.

Para um processo de negócios no qual várias mensagens são enviadas no decorrer da manipulação de algum contexto de aplicativo, a *MessageId* pode ser uma composição do identificador de contexto no nível do aplicativo, como um número de ordem de compra, e o assunto da mensagem, por exemplo, **12345.2017/Payment**.

A *MessageId* sempre pode ser um GUID, mas a ancoragem do identificador para o processo de negócios gera uma repetição previsível, o que é desejado para aproveitar o recurso de detecção de duplicidades com eficiência.

> [!NOTE]
> Se a detecção de duplicidades estiver habilitada e a ID da sessão ou a chave de partição não estiver definida, a ID da mensagem será usada como a chave de partição. Se a ID da mensagem também não for definida, as bibliotecas .NET e AMQP gerarão automaticamente uma ID de mensagem para a mensagem. Para obter mais informações, consulte [uso de chaves de partição](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Ativar deteção de duplicados

No portal, o recurso é ativado durante a criação da entidade com a caixa de seleção **habilitar detecção de duplicidades** , que está desativada por padrão. A configuração para criar novos tópicos é equivalente.

![][1]

> [!IMPORTANT]
> Não é possível habilitar/desabilitar a detecção de duplicidades depois que a fila é criada. Você só pode fazer isso no momento da criação da fila. 

Programaticamente, você define o sinalizador com a propriedade [QueueDescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) na API .NET da estrutura completa. Com a API Azure Resource Manager, o valor é definido com a propriedade [queueproperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

O histórico de tempo de detecção de duplicidades tem como padrão 30 segundos para filas e tópicos, com um valor máximo de sete dias. Você pode alterar essa configuração na janela de propriedades da fila e do tópico na portal do Azure.

![][2]

Programaticamente, você pode configurar o tamanho da janela de detecção de duplicidades durante a qual as IDs de mensagem são mantidas, usando a propriedade [QueueDescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) com a API de .NET Framework completa. Com a API Azure Resource Manager, o valor é definido com a propriedade [queueproperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Habilitar a detecção de duplicidades e o tamanho da janela afetam diretamente a taxa de transferência da fila (e do tópico), já que todas as IDs de mensagem registradas devem ser comparadas com o identificador de mensagem recentemente enviado.

Manter a janela pequena significa que menos IDs de mensagem devem ser retidos e correspondidos, e a taxa de transferência é afetada menos. Para entidades de alta produtividade que exigem detecção de duplicidades, você deve manter a janela o mais pequeno possível.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Em cenários em que o código do cliente não consegue reenviar uma mensagem com a mesma *MessageId* que antes, é importante criar mensagens que podem ser reprocessadas com segurança. Esta [postagem de blog sobre Idempotência](https://particular.net/blog/what-does-idempotent-mean) descreve várias técnicas para fazer isso.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
