---
title: Azure Service Bus duplica a deteção de mensagens / Microsoft Docs
description: Este artigo explica como pode detetar duplicados em mensagens de autocarro da Azure Service. A mensagem duplicada pode ser ignorada e deixada.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: dbca1b4b4f894d35835e7d37e0b4e742a2d3b917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083893"
---
# <a name="duplicate-detection"></a>Deteção de duplicados

Se uma aplicação falhar devido a um erro fatal imediatamente após enviar uma mensagem, e a instância de aplicação reiniciada acredita erradamente que a entrega da mensagem anterior não ocorreu, um envio subsequente faz com que a mesma mensagem apareça duas vezes no sistema.

Também é possível que um erro a nível do cliente ou da rede ocorra um momento antes, e que uma mensagem enviada seja comprometida na fila, com o reconhecimento não devolvido com sucesso ao cliente. Este cenário deixa o cliente em dúvida sobre o resultado da operação de envio.

A deteção duplicada retira a dúvida destas situações, permitindo que o remetente reensiite a mesma mensagem, e a fila ou tópico descarta quaisquer cópias duplicadas.

Permitir a deteção duplicada ajuda a acompanhar o *MessageId* controlado pela aplicação de todas as mensagens enviadas para uma fila ou tópico durante uma janela de tempo especificada. Se alguma nova mensagem for enviada com *o MessageId* que foi registada durante a janela de tempo, a mensagem é reportada como aceite (a operação de envio tem sucesso), mas a mensagem recentemente enviada é imediatamente ignorada e deixada. Não são consideradas outras partes da mensagem que não o *MessageId.*

O controlo de aplicação do identificador é essencial, pois apenas isso permite que a aplicação ligue o *MessageId* a um contexto de processo de negócio a partir do qual pode ser previsivelmente reconstruído quando ocorre uma falha.

Para um processo de negócio em que são enviadas várias mensagens no decurso do tratamento de algum contexto de aplicação, o *MessageId* pode ser um compósito do identificador de contexto de nível de aplicação, como um número de pedido de compra, e o sujeito da mensagem, por exemplo, **12345.2017/pagamento.**

O *MessageId* pode sempre ser um pouco GUID, mas ancorar o identificador ao processo de negócio produz uma repetibilidade previsível, que é desejada para alavancar eficazmente a funcionalidade de deteção duplicada.

> [!NOTE]
> Se a deteção duplicada estiver ativada e a tecla de identificação ou partição da sessão não estiver definida, o ID da mensagem é utilizado como chave de partição. Se o ID da mensagem também não estiver definido, as bibliotecas .NET e AMQP geram automaticamente um ID de mensagem para a mensagem. Para obter mais informações, consulte [a utilização das teclas de partição.](service-bus-partitioning.md#use-of-partition-keys)

## <a name="enable-duplicate-detection"></a>Permitir a deteção de duplicação

No portal, a funcionalidade é ativada durante a criação de entidades com a caixa **de verificação de deteção de duplicação Enable,** que está desligada por padrão. A configuração para a criação de novos tópicos é equivalente.

![Screenshot da caixa de diálogo de fila Create com a opção de deteção de duplicação de ativação selecionada e delineada a vermelho.][1]

> [!IMPORTANT]
> Não é possível ativar/desativar a deteção de duplicados após a criação da fila. Só pode fazê-lo no momento de criar a fila. 

Programáticamente, você definiu a bandeira com a [FilaDescription.requerDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) property on the full framework .NET API. Com a AZure Resource Manager API, o valor é definido com as [propriedades de substituição de filas.requer propriedade DeduplicateDetection.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

O histórico de tempo de deteção duplicado é de 30 segundos para filas e tópicos, com um valor máximo de sete dias. Pode alterar esta definição na janela de propriedades de fila e tópico no portal Azure.

![Screenshot da funcionalidade Service Bus com a definição de Propriedades realçada e a opção de histórico de deteção duplicado delineada a vermelho.][2]

Programáticamente, pode configurar o tamanho da janela de deteção duplicada durante a qual os ids de mensagens são retidos, utilizando a propriedade [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) com a API completa .NET Framework. Com a Azure Resource Manager API, o valor é definido com a [propriedade de queueProperties.duplicateDetectionHistoryTimeWindow.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Permitir a deteção duplicada e o tamanho da janela impactam diretamente a entrada da fila (e do tópico), uma vez que todos os ids de mensagens gravados devem ser compatíveis com o identificador de mensagens recentemente enviado.

Manter a janela pequena significa que menos ids de mensagens devem ser mantidos e combinados, e a produção é menos impactada. Para entidades de alta produção que necessitem de deteção duplicada, deve manter a janela o mais pequena possível.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Em cenários em que o código do cliente não é capaz de reenviar uma mensagem com o mesmo *MessageId* de antes, é importante desenhar mensagens que possam ser re-processadas com segurança. Esta [publicação de blogue sobre idempotência](https://particular.net/blog/what-does-idempotent-mean) descreve várias técnicas para como fazê-lo.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
