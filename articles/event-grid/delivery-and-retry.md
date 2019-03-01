---
title: Entrega do Event Grid e repetição do Azure
description: Descreve como o Azure Event Grid fornece eventos e como ele lida com as mensagens não serão entregues.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: 6dfa84eff8dcc104ae6f9c16262f3b1c697df6c1
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991211"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega de mensagens do Event Grid e tente novamente

Este artigo descreve como o Azure Event Grid processa eventos quando entrega não é reconhecida.

Event Grid fornece entrega durável. Ele fornece a cada mensagem, pelo menos, uma vez para cada subscrição. Eventos são enviados para o ponto de extremidade registado de cada subscrição imediatamente. Se um ponto final não confirmar a receção de um evento, o Event Grid repete a entrega do evento.

Atualmente, Event Grid envia individualmente cada evento para os subscritores. O subscritor recebe uma matriz com um único evento.

## <a name="retry-schedule-and-duration"></a>Agenda de repetição e a duração

Grelha de eventos utiliza uma política de repetição de término exponencial para a entrega de eventos. Se um ponto final não responder ou retorna um código de falha, o Event Grid repete a entrega na agenda seguinte na base de melhor esforço:

1. 10 segundos
1. 30 segundos
1. 1 minuto
1. 5 minutos
1. 10 minutos
1. 30 minutos
1. 1 hora
1. Hora a hora para até 24 horas

Grelha de eventos adiciona uma pequeno aleatoriedade para todos os passos de repetição e oportunisticamente pode ignorar determinadas repetições se um ponto final for consistentemente mau estado de funcionamento, inativo por um longo período, ou parece estar sobrecarregado.

Para comportamento determinístico, defina a hora do evento ao vivo e tentativas de entregas máxima a [as políticas de repetição de subscrição](manage-event-delivery.md).

Por predefinição, o Event Grid expira todos os eventos que não são entregues no prazo de 24 horas. Pode [personalizar a política de repetição](manage-event-delivery.md) durante a criação de uma subscrição de evento. Fornecer o número máximo de tentativas de entrega (a predefinição é 30) e o evento time-to-live (a predefinição é 1440 minutos).

## <a name="dead-letter-events"></a>Eventos de mensagens não entregues

Quando o Event Grid não é possível entregar um evento, ele pode enviar o evento não serão entregues para uma conta de armazenamento. Este processo é conhecido como mensagens não entregues. Por predefinição, não ative Event Grid mensagens não entregues. Para ativá-la, tem de especificar uma conta de armazenamento para armazenar eventos não serão entregues ao criar a subscrição de evento. Obter eventos a partir desta conta de armazenamento para resolver entregas.

Grelha de eventos envia um evento para a localização de mensagens não entregues quando atingir todos os seus de tentativas de repetição. Se a grelha de eventos receber um 400 (pedido incorreto) ou o código de resposta 413 (pedir entidade demasiado grande), envia imediatamente o evento para o ponto final de mensagens não entregues. Esses códigos de resposta indicam a entrega do evento nunca será concluída com êxito.

Existe um atraso de cinco minutos entre a última tentativa de fornecer um evento e quando ela é entregue para a localização de mensagens não entregues. Este atraso destina-se para reduzir o número de operações de armazenamento Blob. Se a localização de mensagens não entregues não está disponível para quatro horas, o evento foi removido.

Antes de definir a localização de mensagens não entregues, tem de ter uma conta de armazenamento com um contentor. Forneça o ponto final para esse contentor ao criar a subscrição de evento. O ponto final está no formato de: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Poderá ser notificado quando um evento tiver sido enviado para a localização entregues. Para utilizar o Event Grid para responder a eventos não serão entregues, [criar uma subscrição de evento](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) para o armazenamento de BLOBs de mensagens não entregues. Sempre que o seu armazenamento de BLOBs de mensagens não entregues recebe um evento não serão entregues, o Event Grid notifica o manipulador. O manipulador responde com as ações que deseja assumir de reconciliar os eventos não serão entregues.

Para obter um exemplo de configuração de uma localização entregues, consulte [inutilizado letras e políticas de repetição](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Estado de entrega da mensagem

Grelha de eventos utiliza códigos de resposta HTTP para confirmar a receção de eventos. 

### <a name="success-codes"></a>Códigos de êxito

Os seguintes códigos de resposta HTTP indicam que um evento tem foi entregue com êxito para o webhook. Grelha de eventos considera a entrega completo.

- 200 OK
- Aceite 202

### <a name="failure-codes"></a>Códigos de falha

Os seguintes códigos de resposta HTTP indicam que não foi uma tentativa de entrega de eventos.

- 400 pedido inválido
- 401 não autorizado
- 404 Não Encontrado
- 408 tempo limite do pedido
- Entidade do pedido 413 demasiado grande
- 414 URI demasiado longo
- 429 demasiados pedidos
- 500 Erro de Servidor Interno
- 503 Serviço Indisponível
- 504 Tempo Limite do Gateway

## <a name="next-steps"></a>Passos Seguintes

* Para ver o estado de entregas de eventos, consulte [entrega de mensagens do Monitor Event Grid](monitor-event-delivery.md).
* Para personalizar as opções de entrega de eventos, veja [inutilizado letras e políticas de repetição](manage-event-delivery.md).