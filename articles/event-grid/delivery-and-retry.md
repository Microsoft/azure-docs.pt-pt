---
title: Entrega e reagem à grelha de eventos Azure
description: Descreve como a Azure Event Grid fornece eventos e como lida com mensagens não entregues.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e7fa627464ddb85ebded3ae99229b7fe8dd3fde3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629279"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega e redação de mensagem da Grelha de Eventos

Este artigo descreve como a Azure Event Grid lida com eventos quando a entrega não é reconhecida.

A Grade de Eventos proporciona uma entrega duradoura. Entrega cada mensagem **pelo menos uma vez** por cada subscrição. Os eventos são enviados para o ponto final registado de cada subscrição imediatamente. Se um ponto final não reconhecer a receção de um evento, a Event Grid retira a entrega do evento.

> [!NOTE]
> A Grade de Eventos não garante a encomenda para entrega de eventos, pelo que o assinante pode recebê-los fora de encomenda. 

## <a name="batched-event-delivery"></a>Entrega de eventos em lote

A Grelha de Eventos não permite enviar cada evento individualmente aos assinantes. O assinante recebe uma matriz com um único evento. Pode configurar a Grade de Eventos para eventos de lote para entrega para melhor desempenho HTTP em cenários de alta produção.

A entrega em lote tem duas definições:

* **Eventos max por lote** - Número máximo de eventos A Grade de Eventos entregará por lote. Este número nunca será ultrapassado, no entanto, menos eventos poderão ser entregues se não houver outros eventos disponíveis no momento da publicação. O Event Grid não atrasa os eventos para criar um lote se houver menos eventos disponíveis. Deve estar entre 1 e 5.000.
* **Tamanho do lote preferido em quilobytes** - Teto-alvo para tamanho do lote em quilobytes. Semelhante a eventos max, o tamanho do lote pode ser menor se mais eventos não estiverem disponíveis no momento da publicação. É possível que um lote seja maior do que o tamanho do lote preferido *se* um único evento for maior do que o tamanho preferido. Por exemplo, se o tamanho preferido for 4 KB e um evento de 10 KB for empurrado para a Grade de Eventos, o evento de 10 KB continuará a ser entregue no seu próprio lote em vez de ser abandonado.

Entrega em lote em configurado numa base de subscrição por evento através do portal, CLI, PowerShell ou SDKs.

### <a name="azure-portal"></a>Portal Azure: 
![Definições de entrega de lote](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>CLI do Azure
Ao criar uma subscrição de eventos, utilize os seguintes parâmetros: 

- **max-events-per-batch** - Número máximo de eventos num lote. Deve ser um número entre 1 e 5000.
- **tamanho preferido do lote em kilobytes** - Tamanho do lote preferido em quilobytes. Deve haver um número entre 1 e 1024.

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

Para obter mais informações sobre a utilização do Azure CLI com a Grade de [Eventos, consulte os eventos de armazenamento da Rota para o ponto final da Web com o Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Horário e duração de redominação

Quando o EventGrid recebe um erro para uma tentativa de entrega de eventos, a EventGrid decide se deve voltar a tentar a entrega ou a carta morta ou largar o evento com base no tipo de erro. 

Se o erro devolvido pelo ponto final subscrito for um erro relacionado com a configuração que não pode ser corrigido com retornas (por exemplo, se o ponto final for eliminado), o EventGrid realizará a letra morta do evento ou abandonará o evento se a letra morta não estiver configurada.

Seguem-se os tipos de pontos finais para os quais não acontece novamente:

| Tipo de ponto final | Códigos de erro |
| --------------| -----------|
| Recursos do Azure | 400 Mau Pedido, 413 Entidade de Pedido Demasiado Grande, 403 Proibido | 
| Webhook | 400 Mau Pedido, 413 Entidade de Pedido Demasiado Grande, 403 Proibido, 404 Não Encontrado, 401 Não Autorizado |
 
> [!NOTE]
> Se Dead-Letter não estiver configurado para o ponto final, os eventos serão eliminados quando ocorrerem erros acima. Considere configurar a Carta Morta, se não quiser que este tipo de eventos sejam eliminados.

Se o erro devolvido pelo ponto final subscrito não estiver entre a lista acima, a EventGrid executa a repetição utilizando as políticas descritas abaixo:

A Grade de Eventos espera 30 segundos para uma resposta depois de entregar uma mensagem. Após 30 segundos, se o ponto final não tiver respondido, a mensagem é reda proibida para tentar novamente. A Grade de Eventos utiliza uma política de recuo exponencial para a entrega do evento. Entrega de retries de grelha de eventos no seguinte horário com uma melhor base de esforço:

- 10 segundos
- 30 segundos
- 1 minuto
- 5 minutos
- 10 minutos
- 30 minutos
- Uma hora
- 3 horas
- 6 horas
- A cada 12 horas até 24 horas


Se o ponto final responder dentro de 3 minutos, a Grade de Eventos tentará remover o evento da fila de re-tentativas com a melhor base de esforço, mas as duplicações ainda podem ser recebidas.

A Grade de Eventos adiciona uma pequena aleatoriedade a todos os passos de rejularia e pode oportunisticamente saltar certas recaídas se um ponto final for consistentemente insalubre, para baixo por um longo período, ou parece estar sobrecarregado.

Para um comportamento determinístico, desa esta altura do evento para as tentativas de entrega ao vivo e máximo nas políticas de [relemis de subscrição](manage-event-delivery.md).

Por predefinição, a Grade de Eventos expira todos os eventos que não são entregues dentro de 24 horas. Pode [personalizar a política de repetição](manage-event-delivery.md) ao criar uma subscrição de eventos. Fornece o número máximo de tentativas de entrega (o padrão é de 30) e o tempo de vida do evento (o padrão é de 1440 minutos).

## <a name="delayed-delivery"></a>Entrega atrasada

À medida que um ponto final experimenta falhas de entrega, a Event Grid começará a atrasar a entrega e a repetição dos eventos para esse ponto final. Por exemplo, se os primeiros 10 eventos publicados num ponto final falharem, a Event Grid assumirá que o ponto final está a ter problemas e irá atrasar todas as recauchutagens *e novas* entregas subsequentes durante algum tempo - em alguns casos até várias horas.

O objetivo funcional da entrega atrasada é proteger os pontos finais insalubres e o sistema de Grade de Eventos. Sem recuos e atrasos na entrega a pontos finais pouco saudáveis, a política de retíria da Event Grid e as capacidades de volume podem facilmente sobrecarregar um sistema.

## <a name="dead-letter-events"></a>Eventos de cartas mortas
Quando a Grade de Eventos não consegue entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um certo número de vezes, pode enviar o evento não entregue para uma conta de armazenamento. Este processo é conhecido como **letra morta.** Event Grid dead-letters um evento quando **uma das seguintes** condições é cumprida. 

- O evento não é entregue dentro do período **de vida.** 
- O **número de tentativas** para entregar o evento excedeu o limite.

Se uma das condições for cumprida, o evento é abandonado ou sem carta.  Por defeito, a Grade de Eventos não liga letras mortas. Para o ativar, tem de especificar uma conta de armazenamento para realizar eventos não entregues ao criar a subscrição do evento. Você puxa eventos desta conta de armazenamento para resolver entregas.

A Grade de Eventos envia um evento para o local da carta morta quando tentou todas as suas tentativas de repetição. Se a Grade de Evento receber um código de resposta de 400 (Mau Pedido) ou 413 (Entidade de Pedido Demasiado Grande), ele imediatamente agenda o evento para a inscrição morta. Estes códigos de resposta indicam que a entrega do evento nunca será bem sucedida.

A expiração do tempo de vida é verificada apenas na próxima tentativa de entrega programada. Assim, mesmo que o tempo de vida expire antes da próxima tentativa de entrega programada, o prazo de validade do evento é verificado apenas no momento da próxima entrega e, em seguida, letra morta. 

Há um atraso de cinco minutos entre a última tentativa de entrega de um evento e quando é entregue no local da carta morta. Este atraso destina-se a reduzir o número de operações de armazenamento blob. Se o local da carta morta não estiver disponível durante quatro horas, o evento será retirado.

Antes de definir o local da letra morta, deve ter uma conta de armazenamento com um recipiente. Você fornece o ponto final para este recipiente ao criar a subscrição do evento. O ponto final está no formato de: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Talvez queira ser notificado quando um evento for enviado para o local da carta morta. Para utilizar a Grade de Eventos para responder a eventos não entregues, [crie uma subscrição](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) de evento para o armazenamento de bolhas de letra morta. Sempre que o seu armazenamento de bolhas de letra morta recebe um evento não entregue, a Grade de Eventos notifica o seu manipulador. O manipulador responde com ações que deseja tomar para conciliar eventos não entregues. Para um exemplo de criação de uma localização de carta morta e políticas de relícuro, consulte [as políticas de carta morta e relagem](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Formatos de eventos de entrega
Esta secção dá-lhe exemplos de eventos e eventos com letras mortas em diferentes formatos de esquema de entrega (esquema de Grelha de Eventos, esquema cloudEvents 1.0 e esquema personalizado). Para obter mais informações sobre estes formatos, consulte [esquemas de Grelha de Eventos](event-schema.md) e artigos [de esquemas cloud 1.0.](cloud-event-schema.md) 

### <a name="event-grid-schema"></a>Esquema do Event Grid

#### <a name="event"></a>Evento 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Evento de carta morta

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1.0 schema

#### <a name="event"></a>Evento

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Evento de carta morta

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Esquema personalizado

#### <a name="event"></a>Evento

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Evento de carta morta
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Estado de entrega de mensagens

A Grade de Eventos utiliza códigos de resposta HTTP para reconhecer a receção de eventos. 

### <a name="success-codes"></a>Códigos de sucesso

A Event Grid considera **apenas** os seguintes códigos de resposta HTTP como entregas bem sucedidas. Todos os outros códigos de estado são considerados entregas falhadas e serão novamente julgados ou mortos, conforme apropriado. Ao receber um código de estado de sucesso, a Grade de Eventos considera a entrega completa.

- 200 OK
- 201 Criado
- 202 Aceito
- 203 Informação Não Autorizada
- 204 Sem Conteúdo

### <a name="failure-codes"></a>Códigos de falha

Todos os outros códigos não no conjunto acima (200-204) são considerados falhas e serão novamente julgados (se necessário). Alguns têm políticas específicas de relícula ligadas a eles descritas abaixo, todas as outras seguem o modelo exponencial padrão de back-off. É importante ter em mente que, devido à natureza altamente paralela da arquitetura do Event Grid, o comportamento relemcrónico não é determinístico. 

| Código de estado | Relemgar o comportamento |
| ------------|----------------|
| 400 Pedido Incorreto | Não julgado novamente |
| 401 Não Autorizado | Reagem após 5 minutos ou mais para os pontos finais dos Recursos Azure |
| 403 Proibido | Não julgado novamente |
| 404 Não Encontrado | Reagem após 5 minutos ou mais para os pontos finais dos Recursos Azure |
| 408 Tempo Limite do Pedido | Relemissar após 2 minutos ou mais |
| 413 Entidade de Pedido Demasiado Grande | Não julgado novamente |
| 503 Serviço Indisponível | Relemissar após 30 segundos ou mais |
| Todos os outros | Relemissar após 10 segundos ou mais |

## <a name="delivery-with-custom-headers"></a>Entrega com cabeçalhos personalizados
As subscrições de eventos permitem-lhe configurar cabeçalhos http que estão incluídos em eventos entregues. Esta capacidade permite-lhe definir cabeçalhos personalizados que são necessários por um destino. Pode configurar até 10 cabeçalhos ao criar uma subscrição de eventos. Cada valor do cabeçalho não deve ser superior a 4.096 bytes (4K). Pode definir cabeçalhos personalizados sobre os eventos que são entregues nos seguintes destinos:

- Webhooks
- Tópicos e filas de autocarros da Azure Service
- Azure Event Hubs
- Conexões híbridas de retransmissão

Para obter mais informações, consulte [a Entrega com cabeçalhos personalizados.](delivery-properties.md) 

## <a name="next-steps"></a>Passos seguintes

* Para visualizar o estado das entregas de eventos, consulte [a entrega de mensagens monitor de eventos](monitor-event-delivery.md).
* Para personalizar opções de entrega de eventos, consulte [as políticas de letra morta e de repetição.](manage-event-delivery.md)
