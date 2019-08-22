---
title: Reagindo aos eventos do armazenamento de BLOBs do Azure | Microsoft Docs
description: Utilize a Azure Event Grid para subscrever a eventos de armazenamento de Blobs.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: b813ef89bb1a55f769d0ea2391855ba5d671c140
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648802"
---
# <a name="reacting-to-blob-storage-events"></a>Reagindo a eventos de armazenamento de BLOBs

Os eventos de armazenamento do Azure permitem que os aplicativos reajam a eventos, como a criação e a exclusão de BLOBs, usando arquiteturas modernas sem servidor. Ele faz isso sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes.

Em vez disso, os eventos são enviados por Push por meio da [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como Azure functions, aplicativos lógicos do Azure ou até mesmo para seu próprio ouvinte HTTP personalizado e você paga apenas pelo que usar.

Os eventos de armazenamento de BLOBs são enviados de forma confiável para o serviço de grade de eventos, que fornece serviços de entrega confiáveis para seus aplicativos por meio de políticas de repetição avançadas e entrega de mensagens mortas.

Cenários comuns de eventos de armazenamento de BLOBs incluem processamento de imagem ou vídeo, indexação de pesquisa ou qualquer fluxo de trabalho orientado a arquivo. Os carregamentos de arquivos assíncronos são uma ótima opção para eventos. Quando as alterações não são frequentes, mas seu cenário requer capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficiente.

Se você quiser experimentar isso agora, consulte qualquer um destes artigos de início rápido:

|Se você quiser usar essa ferramenta:    |Consulte este artigo: |
|--|-|
|Portal do Azure    |[Quickstart: Rotear eventos de armazenamento de BLOBs para o ponto de extremidade da Web com o portal do Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Quickstart: Rotear eventos de armazenamento para o ponto de extremidade da Web com o PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|CLI do Azure    |[Quickstart: Rotear eventos de armazenamento para o ponto de extremidade da Web com CLI do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Se sua conta tiver um namespace hierárquico, este tutorial mostrará como se conectar a uma assinatura de grade de eventos, uma função do Azure e um [trabalho](https://docs.azuredatabricks.net/user-guide/jobs.html) no Azure Databricks: [Destina Use Azure Data Lake Storage Gen2 eventos para atualizar uma tabela](data-lake-storage-events.md)Delta do databricks.

## <a name="the-event-model"></a>O modelo de evento

A grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Essa imagem ilustra a relação entre os editores de eventos, as assinaturas de evento e os manipuladores de eventos.

![Modelo de grade de eventos](./media/storage-blob-event-overview/event-grid-functional-model.png)

Primeiro, assine um ponto de extremidade para um evento. Em seguida, quando um evento for disparado, o serviço de grade de eventos enviará dados sobre esse evento para o ponto de extremidade.

Consulte o artigo [esquema de eventos de armazenamento](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) de BLOBs para exibir:

> [!div class="checklist"]
> * Uma lista completa de eventos de armazenamento de BLOBs e como cada evento é disparado.
> * Um exemplo dos dados que a grade de eventos enviaria para cada um desses eventos.
> * A finalidade de cada par chave-valor que aparece nos dados.

## <a name="filtering-events"></a>Filtrando eventos

As assinaturas de evento de blob podem ser filtradas com base no tipo de evento e pelo nome do contêiner e pelo nome do blob do objeto que foi criado ou excluído.  Os filtros podem ser aplicados a assinaturas de evento durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) da assinatura de evento ou [em um momento posterior](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Os filtros de assunto na grade de eventos funcionam com base nas correspondências "começa com" e "termina com", para que os eventos com um assunto correspondente sejam entregues ao Assinante.

Para saber mais sobre como aplicar filtros, consulte [filtrar eventos para a grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

O assunto dos eventos de armazenamento de BLOBs usa o formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Para corresponder a todos os eventos de uma conta de armazenamento, você pode deixar os filtros de assunto vazios.

Para fazer a correspondência de eventos de BLOBs criados em um conjunto de contêineres que compartilham `subjectBeginsWith` um prefixo, use um filtro como:

```
/blobServices/default/containers/containerprefix
```

Para corresponder eventos de BLOBs criados em um contêiner específico, use `subjectBeginsWith` um filtro como:

```
/blobServices/default/containers/containername/
```

Para fazer a correspondência de eventos de BLOBs criados em um contêiner específico que compartilha um prefixo `subjectBeginsWith` de nome de BLOB, use um filtro como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para fazer a correspondência de eventos de BLOBs criados em um contêiner específico que compartilha um `subjectEndsWith` sufixo de BLOB, use um filtro como ". log" ou ". jpg". Para obter mais informações, consulte [conceitos da grade de eventos](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos

Os aplicativos que tratam eventos de armazenamento de BLOBs devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, é importante não pressupor que os eventos sejam de uma fonte específica, mas para verificar o tópico da mensagem a fim de garantir que ela venha da conta de armazenamento que você espera.
> * Da mesma forma, verifique se o eventType é um que você está preparado para processar e não presuma que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e depois de algum atraso, use os campos ETag para entender se suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos do Sequencer para entender a ordem dos eventos em qualquer objeto específico.
> * Use o campo BlobType para entender que tipo de operações são permitidas no BLOB e quais tipos de biblioteca de cliente você deve usar para acessar o blob. Os valores válidos são `BlockBlob` ou `PageBlob`. 
> * Use o campo URL com os `CloudBlockBlob` construtores `CloudAppendBlob` e para acessar o blob.
> * Ignore os campos que você não entende. Essa prática ajudará a mantê-lo resiliente a novos recursos que podem ser adicionados no futuro.
> * Se você quiser garantir que o evento **Microsoft. Storage. BlobCreated** seja disparado somente quando um blob de blocos for completamente confirmado, filtre o evento `CopyBlob`para as `PutBlockList` chamadas `FlushWithClose` da API REST, `PutBlob`ou. Essas chamadas de API disparam o evento **Microsoft. Storage. BlobCreated** somente depois que os dados são totalmente confirmados em um blob de blocos. Para saber como criar um filtro, consulte [filtrar eventos para a grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a grade de eventos e forneça eventos de armazenamento de BLOBs uma tentativa:

- [Sobre o Event Grid](../../event-grid/overview.md)
- [Rotear eventos de armazenamento de BLOBs para um ponto de extremidade da Web personalizado](storage-blob-event-quickstart.md)
