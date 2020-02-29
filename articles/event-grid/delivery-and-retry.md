---
title: Entrega e retentativa da Rede de Eventos Azure
description: Descreve como a Azure Event Grid fornece eventos e como lida com mensagens não entregues.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921067"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega e retentativa de mensagem da Grelha de Eventos

Este artigo descreve como a Azure Event Grid lida com eventos quando a entrega não é reconhecida.

A Rede de Eventos proporciona uma entrega duradoura. Entrega cada mensagem pelo menos uma vez para cada subscrição. Os eventos são enviados para o ponto final registado de cada subscrição imediatamente. Se um ponto final não reconhecer a receção de um evento, a Event Grid retenta a entrega do evento.

## <a name="batched-event-delivery"></a>Entrega de eventos lotados

A Grelha de Eventos não se aplica ao envio individual de cada evento aos assinantes. O assinante recebe um matriz com um único evento. Pode configurar eventos da Rede de Eventos para entrega para um melhor desempenho em HTTP em cenários de alta produção.

A entrega lotada tem duas definições:

* **Eventos max por lote** - O número máximo de eventos Que a Grelha de Eventos irá entregar por lote. Este número nunca será ultrapassado, no entanto, menos eventos poderão ser entregues se não houver outros eventos disponíveis no momento da publicação. A Grelha de Eventos não atrasa os eventos para criar um lote se houver menos eventos disponíveis. Deve ser entre 1 e 5.000.
* **Tamanho do lote preferido em quilobytes** - Teto-alvo para o tamanho do lote em quilobytes. Semelhante a eventos max, o tamanho do lote pode ser menor se não houver mais eventos disponíveis no momento da publicação. É possível que um lote seja maior do que o tamanho do lote preferido *se* um único evento for maior do que o tamanho preferido. Por exemplo, se o tamanho preferido for de 4 KB e um evento de 10 KB for empurrado para event grid, o evento de 10 KB ainda será entregue no seu próprio lote em vez de ser abandonado.

Entrega em lotada configurada numa base de subscrição por evento através do portal, CLI, PowerShell ou SDKs.

### <a name="azure-portal"></a>Portal Azure: 
![Definições de entrega de lotes](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>CLI do Azure
Ao criar uma subscrição de evento, utilize os seguintes parâmetros: 

- **max-events-per-batch** - Número máximo de eventos num lote. Deve ser um número entre 1 e 5000.
- **preferencial-lote-tamanho-em-kilobytes** - Tamanho preferido do lote em quilobytes. Deve ser um número entre 1 e 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Para obter mais informações sobre a utilização do Azure CLI com a Rede de Eventos, consulte os eventos de [armazenamento da Route para o ponto final da web com o Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Horário e duração de retry

A Grelha de Eventos aguarda 30 segundos por uma resposta depois de entregar uma mensagem. Após 30 segundos, se o ponto final não tiver respondido, a mensagem está na fila para ser novamente tentada. Event Grid usa uma política exponencial de retry backoff para entrega de eventos. Event Grid retenta entrega no seguinte horário com o melhor esforço:

- 10 segundos
- 30 segundos
- 1 minuto
- 5 minutos
- 10 minutos
- 30 minutos
- 1 hora
- Hora por hora até 24 horas

Se o ponto final responder dentro de 3 minutos, a Grelha de Eventos tentará remover o evento da fila de retry com o melhor esforço, mas ainda podem ser recebidos duplicados.

A Grelha de Eventos adiciona uma pequena aleatoriedade a todos os passos de retry e pode oportunisticamente saltar certas tentativas se um ponto final for consistentemente insalubre, para baixo por um longo período, ou parece estar sobrecarregado.

Para um comportamento determinístico, detete o tempo do evento para as tentativas de entrega ao vivo e max nas políticas de retry de [subscrição](manage-event-delivery.md).

Por padrão, a Grelha de Eventos expira todos os eventos que não são entregues dentro de 24 horas. Pode [personalizar a política](manage-event-delivery.md) de retry ao criar uma subscrição de evento. Fornece o número máximo de tentativas de entrega (o padrão é de 30) e o tempo de vida do evento (o padrão é de 1440 minutos).

## <a name="delayed-delivery"></a>Entrega atrasada

Como um ponto final experimenta falhas de entrega, a Rede de Eventos começará a atrasar a entrega e a retry de eventos para esse ponto final. Por exemplo, se os primeiros 10 eventos publicados para um ponto final falharem, o Event Grid assumirá que o ponto final está a passar por problemas e atrasará todas as repetições subsequentes *e novas* entregas durante algum tempo - em alguns casos até várias horas.

O objetivo funcional da entrega atrasada é proteger pontos finais pouco saudáveis, bem como o sistema de Rede de Eventos. Sem recuo e atraso na entrega em pontos finais pouco saudáveis, a política de retry e capacidades de volume da Event Grid podem facilmente sobrecarregar um sistema.

## <a name="dead-letter-events"></a>Eventos de cartas mortas

Quando a Rede de Eventos não pode entregar um evento, pode enviar o evento não entregue para uma conta de armazenamento. Este processo é conhecido como letra morta. Por defeito, a Grelha de Eventos não liga as letras mortas. Para o permitir, deve especificar uma conta de armazenamento para realizar eventos não entregues ao criar a subscrição do evento. Você retira eventos desta conta de armazenamento para resolver entregas.

Event Grid envia um evento para o local da carta morta quando tentou todas as suas tentativas de reprovação. Se a Grelha de Eventos receber um código de resposta 400 (Pedido Mau) ou 413 (Entidade de Pedido Demasiado Grande), envia imediatamente o evento para o ponto final da letra morta. Estes códigos de resposta indicam que a entrega do evento nunca terá sucesso.

Há um atraso de cinco minutos entre a última tentativa de entregar um evento e quando é entregue no local da carta morta. Este atraso destina-se a reduzir o número de operações de armazenamento blob. Se o local da carta morta não estiver disponível por quatro horas, o evento será abandonado.

Antes de definir a localização da letra morta, deve ter uma conta de armazenamento com um recipiente. Fornece o ponto final para este recipiente ao criar a subscrição do evento. O ponto final está no formato: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Talvez queira ser notificado quando um evento tiver sido enviado para o local da carta morta. Para utilizar a Grelha de Eventos para responder a eventos não entregues, [crie uma subscrição](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) de evento para o armazenamento de blob de letra morta. Sempre que o seu armazenamento de blob de letra morta recebe um evento não entregue, a Event Grid notifica o seu manipulador. O manipulador responde com ações que deseja tomar para conciliar eventos não entregues.

Para um exemplo de criação de um local de carta morta, consulte as políticas de [carta morta e de retry.](manage-event-delivery.md)

## <a name="message-delivery-status"></a>Estado de entrega de mensagens

A Event Grid utiliza códigos de resposta HTTP para reconhecer a receção de eventos. 

### <a name="success-codes"></a>Códigos de sucesso

A Event Grid considera **apenas** os seguintes códigos de resposta HTTP como entregas bem sucedidas. Todos os outros códigos de estado são considerados entregas falhadas e serão novamente julgados ou re-inscritos conforme apropriado. Ao receber um código de estado bem sucedido, a Event Grid considera a entrega completa.

- 200 OK
- 201 Criado
- 202 Aceite
- 203 Informação Não Autorizada
- 204 Sem Conteúdo

### <a name="failure-codes"></a>Códigos de falha

Todos os outros códigos que não estão no conjunto acima (200-204) são considerados falhas e serão novamente julgados. Alguns têm políticas específicas de retry ligadas a eles delineadas abaixo, todas as outras seguem o modelo de back-off exponencial padrão. É importante ter em mente que, devido à natureza altamente paralelizada da arquitetura de Event Grid, o comportamento de retry é não determinista. 

| Código de estado | Comportamento de retry |
| ------------|----------------|
| 400 Mau Pedido | Retry após 5 minutos ou mais (Deadletter imediatamente se configurar a carta morta) |
| 401 Não autorizado | Voltar a tentar depois de 5 minutos ou mais |
| 403 Proibido | Voltar a tentar depois de 5 minutos ou mais |
| 404 não encontrados | Voltar a tentar depois de 5 minutos ou mais |
| 408 Tempo Limite do Pedido | Voltar a tentar depois de 2 minutos ou mais |
| 413 Entidade de Pedido Demasiado Grande | Voltar a tentar após 10 segundos ou mais (Deadletter imediatamente se configurar a carta morta) |
| 503 Serviço Indisponível | Voltar a tentar após 30 segundos ou mais |
| Todos os outros | Voltar a tentar após 10 segundos ou mais |


## <a name="next-steps"></a>Passos seguintes

* Para ver o estado das entregas de eventos, consulte a entrega da mensagem da Grelha de [Eventos monitor .](monitor-event-delivery.md)
* Para personalizar as opções de entrega de eventos, consulte [políticas de carta morta e de retry.](manage-event-delivery.md)
