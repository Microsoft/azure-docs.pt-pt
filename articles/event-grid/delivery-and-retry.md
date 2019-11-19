---
title: Entrega e repetição da grade de eventos do Azure
description: Descreve como a grade de eventos do Azure fornece eventos e como ele lida com mensagens não entregues.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 483b8251bf17eaa5fe7aa7cbd86299575535725d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170054"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega e repetição de mensagem da grade de eventos

Este artigo descreve como a grade de eventos do Azure manipula eventos quando a entrega não é confirmada.

A grade de eventos fornece entrega durável. Ele entrega cada mensagem pelo menos uma vez para cada assinatura. Os eventos são enviados para o ponto de extremidade registrado de cada assinatura imediatamente. Se um ponto de extremidade não reconhecer o recebimento de um evento, a grade de eventos tentará entregar o evento novamente.

## <a name="batched-event-delivery"></a>Entrega de eventos em lote

A grade de eventos usa como padrão o envio de cada evento individualmente aos assinantes. O assinante recebe uma matriz com um único evento. Você pode configurar a grade de eventos para eventos em lote para entrega para melhorar o desempenho de HTTP em cenários de alta taxa de transferência.

A entrega em lote tem duas configurações:

* **Máximo de eventos por lote** é o número máximo de eventos que a grade de eventos fornecerá por lote. Esse número nunca será excedido, mas menos eventos poderão ser entregues se nenhum outro evento estiver disponível no momento da publicação. A grade de eventos não atrasa eventos para criar um lote se menos eventos estiverem disponíveis. Deve estar entre 1 e 5.000.
* O **tamanho de lote preferencial em quilobytes** é o teto de destino para o tamanho do lote em kilobytes. Semelhante a Max Events, o tamanho do lote pode ser menor se mais eventos não estiverem disponíveis no momento da publicação. É possível que um lote seja maior do que o tamanho de lote preferencial *se* um único evento for maior do que o tamanho preferencial. Por exemplo, se o tamanho preferencial for de 4 KB e um evento de 10 KB for enviado para a grade de eventos, o evento de 10 KB ainda será entregue em seu próprio lote, em vez de ser Descartado.

Entrega em lote em configurada por assinatura por evento por meio do portal, da CLI, do PowerShell ou de SDKs.

![Configurações de entrega em lotes](./media/delivery-and-retry/batch-settings.png)

## <a name="retry-schedule-and-duration"></a>Agenda e duração de repetição

A grade de eventos aguarda 30 segundos por uma resposta depois de entregar uma mensagem. Após 30 segundos, se o ponto de extremidade não tiver respondido, a mensagem será enfileirada para tentar novamente. A grade de eventos usa uma política de repetição de retirada exponencial para a entrega de eventos. A grade de eventos repete a entrega na seguinte agenda com base no melhor esforço:

- 10 segundos
- 30 segundos
- 1 minuto
- 5 minutos
- 10 minutos
- 30 minutos
- 1 hora
- Por hora por até 24 horas

Se o ponto de extremidade responder em 3 minutos, a grade de eventos tentará remover o evento da fila de repetição em uma base de melhor esforço, mas as duplicatas ainda poderão ser recebidas.

A grade de eventos adiciona uma pequena aleatoriedade a todas as etapas de repetição e pode ignorar oportunamente determinadas tentativas se um ponto de extremidade estiver consistentemente não íntegro, inativo por um longo período ou parecer sobrecarregado.

Para comportamento determinístico, defina a vida útil do evento e as tentativas de entrega máxima nas [políticas de repetição de assinatura](manage-event-delivery.md).

Por padrão, a grade de eventos expira todos os eventos que não são entregues dentro de 24 horas. Você pode [Personalizar a política de repetição](manage-event-delivery.md) ao criar uma assinatura de evento. Você fornece o número máximo de tentativas de entrega (o padrão é 30) e a vida útil do evento (o padrão é de 1440 minutos).

## <a name="delayed-delivery"></a>Entrega atrasada

Como um ponto de extremidade apresenta falhas de entrega, a grade de eventos começará a atrasar a entrega e a repetição de eventos para esse ponto de extremidade. Por exemplo, se os 10 primeiros eventos publicados em um ponto de extremidade falharem, a grade de eventos presumirá que o ponto de extremidade está apresentando problemas e atrasará todas as novas tentativas subsequentes *e novos* entregas por algum tempo, em alguns casos, até várias horas.

A finalidade funcional da entrega atrasada é proteger pontos de extremidade não íntegros, bem como o sistema de grade de eventos. Sem retirada e atraso de entrega para pontos de extremidade não íntegros, a política de repetição da grade de eventos e os recursos de volume podem facilmente sobrecarregar um sistema.

## <a name="dead-letter-events"></a>Eventos de mensagens mortas

Quando a grade de eventos não consegue entregar um evento, ela pode enviar o evento não entregue para uma conta de armazenamento. Esse processo é conhecido como mensagens mortas. Por padrão, a grade de eventos não ativa mensagens mortas. Para habilitá-lo, você deve especificar uma conta de armazenamento para manter os eventos não entregues ao criar a assinatura de evento. Você efetua pull de eventos dessa conta de armazenamento para resolver as entregas.

A grade de eventos envia um evento para o local da carta de inatividade quando ele tentou todas as suas tentativas de repetição. Se a grade de eventos receber um código de resposta 400 (solicitação inadequada) ou 413 (entidade de solicitação muito grande), ele enviará imediatamente o evento para o ponto de extremidade de mensagens mortas. Esses códigos de resposta indicam que a entrega do evento nunca terá sucesso.

Há um atraso de cinco minutos entre a última tentativa de entregar um evento e quando ele é entregue ao local da letra de inatividade. Esse atraso destina-se a reduzir o número de operações de armazenamento de BLOBs. Se o local de mensagens mortas não estiver disponível por quatro horas, o evento será Descartado.

Antes de definir o local de mensagens mortas, você deve ter uma conta de armazenamento com um contêiner. Você fornece o ponto de extremidade para esse contêiner ao criar a assinatura de evento. O ponto de extremidade está no formato de: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Talvez você queira ser notificado quando um evento for enviado para o local da carta de inatividade. Para usar a grade de eventos para responder a eventos não entregues, [crie uma assinatura de evento](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) para o armazenamento de blobs de mensagens mortas. Toda vez que seu armazenamento de blobs de mensagens mortas recebe um evento não entregue, a grade de eventos notifica seu manipulador. O manipulador responde com as ações que você deseja executar para reconciliar eventos não entregues.

Para obter um exemplo de como configurar um local de mensagens mortas, consulte [políticas de inatividade e repetição](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Status de entrega de mensagem

A grade de eventos usa códigos de resposta HTTP para confirmar o recebimento de eventos. 

### <a name="success-codes"></a>Códigos de êxito

A grade de eventos considera **apenas** os seguintes códigos de resposta http como entregas bem-sucedidas. Todos os outros códigos de status são considerados entregas com falha e serão repetidos ou mortodos conforme apropriado. Após receber um código de status bem-sucedido, a grade de eventos considera a entrega concluída.

- 200 OK
- 201 criado
- 202 aceito
- 203 informações não autoritativas
- 204 sem conteúdo

### <a name="failure-codes"></a>Códigos de falha

Todos os outros códigos que não estão no conjunto acima (200-204) são considerados falhas e serão repetidos. Alguns têm políticas de repetição específicas ligadas a eles descritos abaixo, todos os outros seguem o modelo de retirada exponencial padrão. É importante ter em mente que, devido à natureza altamente paralelizada da arquitetura da grade de eventos, o comportamento de repetição é não determinístico. 

| Código de estado | Comportamento de repetição |
| ------------|----------------|
| 400 solicitação inadequada | Tente novamente após 5 minutos ou mais (mensagens mortas imediatamente se a instalação de mensagens mortas) |
| 401 não autorizado | Tente novamente após 5 minutos ou mais |
| 403 Proibido | Tente novamente após 5 minutos ou mais |
| 404 Não Encontrado | Tente novamente após 5 minutos ou mais |
| 408 Tempo Limite do Pedido | Tentar novamente após 2 minutos ou mais |
| 413 entidade de solicitação muito grande | Tente novamente após 10 segundos ou mais (mensagens mortas imediatamente se a instalação de mensagens mortas) |
| 503 Serviço Indisponível | Tentar novamente após 30 segundos ou mais |
| Todos os outros | Tentar novamente após 10 segundos ou mais |


## <a name="next-steps"></a>Passos seguintes

* Para exibir o status de entregas de eventos, consulte [monitorar a entrega de mensagens na grade de eventos](monitor-event-delivery.md).
* Para personalizar as opções de entrega de eventos, consulte [políticas de inatividade e repetição](manage-event-delivery.md).
