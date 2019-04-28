---
title: Deteção de duplicados de mensagem do Service Bus do Azure | Documentos da Microsoft
description: Detetar mensagens duplicadas do Service Bus
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
ms.openlocfilehash: c419ee1eec9e451cad835d8b4a56818101dc853a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120862"
---
# <a name="duplicate-detection"></a>Deteção de duplicados

Se uma aplicação falhar devido a um erro fatal imediatamente após ele envia uma mensagem e a instância da aplicação reiniciadas erroneamente acredita que a entrega de mensagens anteriores não ocorreu, o envio subsequente faz com que a mesma mensagem sejam apresentadas duas vezes no sistema.

Também é possível que um erro ao nível da rede ou de cliente para um momento antes de ocorrer e, para uma mensagem enviada ser consolidado para a fila, com a confirmação não com êxito, retornados ao cliente. Este cenário deixa o cliente em caso de dúvida sobre o resultado da operação de envio.

Deteção de duplicados usa a dúvida fora essas situações, permitindo a reenviar remetente a mesma mensagem, e a fila ou tópico descarta quaisquer cópias duplicadas.

Ativar a deteção de duplicados ajuda a manter o controle de controlada pelo aplicativo *MessageId* de todas as mensagens enviadas para uma fila ou tópico durante uma janela de tempo especificado. Se qualquer nova mensagem for enviada com *MessageId* que foram registada durante a janela de tempo, a mensagem é reportada como aceites (a operação de envio for concluída com êxito), mas a mensagem enviada recentemente instantaneamente é ignorada e removida. Não existem outras partes da mensagem que o *MessageId* são considerados.

Controlo de aplicações do identificador é essencial, porque é só isso permite que o aplicativo para se ligarem a *MessageId* ao contexto de processo comercial a partir do qual ele pode ser previsível reconstruído quando ocorre uma falha.

Para um processo comercial em que várias mensagens são enviadas no decorrer de lidar com algum contexto do aplicativo, o *MessageId* pode ser uma composição do identificador de contexto de nível de aplicativo, como um número de ordem de compra e o assunto da mensagem, por exemplo, **12345.2017/pagamento**.

O *MessageId* sempre pode ser um GUID, mas ancorar o identificador para o processo de negócios produz repetibilidade previsível, o que for o pretendido para tirar partido da funcionalidade de deteção de duplicados com eficiência.

## <a name="enable-duplicate-detection"></a>Ativar a deteção de duplicados

No portal, a funcionalidade é ativada durante a criação da entidade com o **ativar a deteção de duplicados** caixa de verificação, o que está desativado por predefinição. A configuração para a criação de novos tópicos é equivalente.

![][1]

> [!IMPORTANT]
> Não pode ativar/desativar de deteção de duplicados depois da fila é criada. Só pode fazer isso no momento da criação da fila. 

Programaticamente, definir o sinalizador com o [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) propriedade na API do .NET framework completo. Com a API do Azure Resource Manager, o valor é definido com o [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) propriedade.

O histórico de tempo de deteção de duplicados é predefinido para 30 segundos para filas e tópicos, com um valor máximo de sete dias. Pode alterar esta definição na janela de propriedades da fila e tópico no portal do Azure.

![][2]

Por meio de programação, pode configurar o tamanho da janela de deteção de duplicados durante o qual os ids de mensagem são mantidas, utilizando o [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) propriedade com a API do .NET Framework completo . Com a API do Azure Resource Manager, o valor é definido com o [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) propriedade.

Ativar deteção de duplicados e o tamanho da janela afetam diretamente o débito de fila (e tópico), uma vez que todos os ids de mensagem gravados devem ser comparados com o identificador de mensagem submetidas recentemente.

Manter o meio de pequena janela que menos de ids de mensagem tem de ser mantidos e correspondentes e o débito é afetado menor. Para entidades de alto débito que necessitam de deteção de duplicados, deve manter a janela o menor possível.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
