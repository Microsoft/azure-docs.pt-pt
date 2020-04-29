---
title: Azure Service Bus duplica deteção de mensagens / Microsoft Docs
description: Este artigo explica como pode detetar duplicados em mensagens de ônibus de serviço Azure. A mensagem duplicada pode ser ignorada e largada.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c109b9fd310a09e5eb4c6d18cc3536e4d8069c0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760373"
---
# <a name="duplicate-detection"></a>Deteção de duplicados

Se uma aplicação falhar devido a um erro fatal imediatamente após o envio de uma mensagem, e a instância de aplicação reiniciada erroneamente acredita que a entrega de mensagens prévias não ocorreu, um envio subsequente faz com que a mesma mensagem apareça duas vezes no sistema.

Também é possível que um erro ao nível do cliente ou da rede ocorra um momento antes, e que uma mensagem enviada seja cometida na fila, com o reconhecimento não devolvido com sucesso ao cliente. Este cenário deixa o cliente em dúvida sobre o resultado da operação de envio.

A deteção duplicada tira a dúvida destas situações, permitindo que o remetente reenvie a mesma mensagem, e a fila ou tópico descarta quaisquer cópias duplicadas.

Permitir a deteção de duplicados ajuda a acompanhar o *MessageId* controlado pela aplicação de todas as mensagens enviadas para uma fila ou tópico durante uma janela de tempo especificada. Se alguma nova mensagem for enviada com *o MessageId* que foi registado durante o período de tempo, a mensagem é reportada como aceite (a operação de envio é bem sucedida), mas a mensagem recém-enviada é imediatamente ignorada e abandonada. Não são consideradas outras partes da mensagem que não o *MessageId.*

O controlo da aplicação do identificador é essencial, pois só isso permite que a aplicação ligue o *MessageId* a um contexto de processo de negócio a partir do qual pode ser previsivelmente reconstruído quando ocorre uma falha.

Para um processo de negócio em que várias mensagens são enviadas no decurso do tratamento de algum contexto de aplicação, o *MessageId* pode ser um composto do identificador de contexto de nível de aplicação, como um número de encomenda de compra, e o sujeito da mensagem, por exemplo, **12345.2017/pagamento.**

O *MessageId* pode sempre ser algum GUID, mas ancorar o identificador ao processo de negócio gera uma repetbilidade previsível, que é desejada para alavancar eficazmente a funcionalidade de deteção duplicada.

> [!NOTE]
> Se a deteção duplicada estiver ativada e a chave de identificação ou partição da sessão não estiver definida, o ID da mensagem é utilizado como chave de partição. Se o ID da mensagem também não estiver definido, as bibliotecas .NET e AMQP geram automaticamente um ID de mensagem para a mensagem. Para mais informações, consulte [A utilização de teclas de partição](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Ativar a deteção de duplicados

No portal, a funcionalidade é ativada durante a criação da entidade com a caixa de verificação de **deteção duplicada Enable,** que está desligada por defeito. O cenário para a criação de novos tópicos é equivalente.

![][1]

> [!IMPORTANT]
> Não é possível ativar/desativar a deteção duplicada após a criação da fila. Só o podes fazer no momento de criar a fila. 

Programáticamente, define a bandeira com a [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) property na estrutura completa .NET API. Com a API do Gestor de Recursos Azure, o valor é definido com a [propriedade queueProperties.requiresDuplicateDetection.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

O tempo de deteção duplicado desativa-se em 30 segundos para filas e tópicos, com um valor máximo de sete dias. Pode alterar este cenário na janela de propriedades de fila e tópicos no portal Azure.

![][2]

Programaticamente, pode configurar o tamanho da janela de deteção duplicada durante a qual os ids de mensagens são retidos, utilizando a propriedade [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) com a API de quadro completo .NET. Com a API do Gestor de Recursos Azure, o valor é definido com a propriedade [queueProperties.duplicateDetectionHistoryTimeWindow.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Permitir a deteção de duplicados e o tamanho da janela impactam diretamente a entrada da fila (e do tópico), uma vez que todos os ids de mensagem registados devem ser comparados com o identificador de mensagens recentemente apresentado.

Manter a janela pequena significa que menos ids de mensagens devem ser mantidos e combinados, e a entrada é menos impactada. Para entidades de alta sucuviação que necessitem de deteção duplicada, deve manter a janela o mais pequena possível.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Em cenários em que o código do cliente não é capaz de reenviar uma mensagem com o mesmo *MessageId* de antes, é importante desenhar mensagens que possam ser reprocessadas com segurança. Esta [publicação de blogue sobre idempotence](https://particular.net/blog/what-does-idempotent-mean) descreve várias técnicas para como fazê-lo.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
