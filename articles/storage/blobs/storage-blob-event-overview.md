---
title: Reagir aos eventos de armazenamento da Azure Blob | Microsoft Docs
description: Utilize a Grelha de Eventos Azure para subscrever e reagir a eventos de armazenamento blob. Compreenda o modelo do evento, filtrando eventos e práticas para consumir eventos.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: dineshm
ms.openlocfilehash: f07c249e3b7cb54283959df410d51ca18998f2cf
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181521"
---
# <a name="reacting-to-blob-storage-events"></a>Reagir aos eventos de armazenamento de Blobs

Os eventos de Armazenamento Azure permitem que as aplicações reajam a eventos, como a criação e eliminação de bolhas. Fá-lo sem a necessidade de um código complicado ou de serviços de sondagens dispendiosos e ineficientes. A melhor parte é que só pagas pelo que usas.

Os eventos de armazenamento blob são empurrados usando [a Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para assinantes como Azure Functions, Azure Logic Apps, ou mesmo para o seu próprio ouvinte http. A Event Grid fornece uma entrega fiável de eventos às suas aplicações através de políticas de relagem ricas e letras mortas.

Consulte o artigo [de esquema de eventos de armazenamento Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver a lista completa dos eventos que o Blob suporta.

Os cenários comuns do evento de armazenamento blob incluem processamento de imagem ou vídeo, indexação de pesquisa ou qualquer fluxo de trabalho orientado para ficheiros. Os uploads de ficheiros assíncronos são um ótimo ajuste para eventos. Quando as mudanças são pouco frequentes, mas o seu cenário requer capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficiente.

Se quiser experimentar eventos de armazenamento de bolhas, consulte qualquer um destes artigos de arranque rápido:

|Se quiser utilizar esta ferramenta:    |Consulte este artigo: |
|--|-|
|Portal do Azure    |[Quickstart: Route Blob storage events to web endpoint with the Azure portal](../../event-grid/blob-event-quickstart-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Quickstart: Route storage events to web endpoint with PowerShell](./storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|CLI do Azure    |[Quickstart: Route storage events to web endpoint with Azure CLI](./storage-blob-event-quickstart.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Para ver exemplos aprofundados de reação a eventos de armazenamento blob utilizando funções Azure, consulte estes artigos:

- [Tutorial: Use eventos Azure Data Lake Storage Gen2 para atualizar uma tabela Delta databricks](data-lake-storage-events.md).
- [Tutorial: Automatizar redimensionar imagens carregadas usando a Grade de Eventos](../../event-grid/resize-images-on-storage-blob-upload-event.md?tabs=dotnet)

>[!NOTE]
> Apenas contas de armazenamento do tipo **StorageV2 (finalidade geral v2),** **BlockBlobStorage** e integração de eventos de suporte **BlobStorage.** **O armazenamento (propósito geral v1)** *não* suporta a integração com a Grade de Eventos.

## <a name="the-event-model"></a>O modelo do evento

O Event Grid utiliza [subscrições de eventos](../../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de eventos para assinantes. Esta imagem ilustra a relação entre editores de eventos, subscrições de eventos e manipuladores de eventos.

![Modelo de grelha de evento](./media/storage-blob-event-overview/event-grid-functional-model.png)

Primeiro, subscreva um ponto final para um evento. Em seguida, quando um evento é desencadeado, o serviço de Grade de Eventos enviará dados sobre esse evento para o ponto final.

Consulte o artigo [de esquema de eventos de armazenamento Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver:

> [!div class="checklist"]
> * Uma lista completa de eventos de armazenamento Blob e como cada evento é desencadeado.
> * Um exemplo dos dados que a Grade de Eventos enviaria para cada um destes eventos.
> * O propósito de cada par de valor chave que aparece nos dados.

## <a name="filtering-events"></a>Eventos de filtragem

Os [eventos blob podem ser filtrados](/cli/azure/eventgrid/event-subscription) pelo tipo de evento, nome do recipiente ou nome do objeto que foi criado/eliminado. Os filtros em Grade de Evento correspondem ao início ou ao fim do assunto para que os eventos com um sujeito correspondente vão para o assinante.

Para saber mais sobre como aplicar filtros, consulte [eventos de filtro para grelha de eventos.](../../event-grid/how-to-filter-events.md)

O tema dos eventos de armazenamento Blob utiliza o formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Para combinar todos os eventos com uma conta de armazenamento, pode deixar os filtros do assunto vazios.

Para combinar eventos de bolhas criadas num conjunto de recipientes que partilham um prefixo, utilize um `subjectBeginsWith` filtro como:

```
/blobServices/default/containers/containerprefix
```

Para combinar eventos a partir de bolhas criadas em recipiente específico, utilize um `subjectBeginsWith` filtro como:

```
/blobServices/default/containers/containername/
```

Para combinar eventos a partir de bolhas criadas em recipiente específico que partilham um prefixo de nome de bolha, utilize um `subjectBeginsWith` filtro como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para combinar eventos de bolhas criadas em recipientes específicos que partilham um sufixo de bolha, utilize um `subjectEndsWith` filtro como ".log" ou ".jpg". Para mais informações, consulte [os Conceitos de Grelha de Eventos.](../../event-grid/concepts.md#event-subscriptions)

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos

As aplicações que lidam com eventos de armazenamento Blob devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que ele vem da conta de armazenamento que você está esperando.
> * Da mesma forma, verifique se o eventType é aquele que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
> * Como as mensagens podem chegar após algum atraso, use os campos etag para entender se as suas informações sobre objetos ainda estão atualizadas. Para aprender a utilizar o campo etag, consulte [a Gestão da concordância no armazenamento blob](./concurrency-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage).
> * Como as mensagens podem chegar fora de ordem, use os campos de sequenciador para entender a ordem dos eventos em qualquer objeto em particular. O campo do sequenciador é um valor de corda que representa a sequência lógica de eventos para qualquer nome blob em particular. Pode utilizar a comparação de cordas padrão para entender a sequência relativa de dois eventos no mesmo nome blob.
> * Os eventos de armazenamento garantem pelo menos uma vez a entrega aos assinantes, o que garante que todas as mensagens são outputadas. No entanto, devido a retréis entre nós de backend e serviços ou disponibilidade de subscrições, podem ocorrer mensagens duplicadas. Para saber mais sobre a entrega de mensagens e retentou, consulte [a entrega de mensagens de Event Grid e retentou.](../../event-grid/delivery-and-retry.md)
> * Utilize o campo blobType para entender que tipo de operações são permitidas na bolha e quais os tipos de biblioteca de clientes que deve usar para aceder à bolha. Valores válidos são `BlockBlob` `PageBlob` ou. 
> * Utilize o campo url com os `CloudBlockBlob` `CloudAppendBlob` construtores e para aceder à bolha.
> * Ignore campos que não entende. Esta prática irá ajudá-lo a manter-se resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
> * Se pretender garantir que o evento **Microsoft.Storage.BlobCreated** só é acionado quando um Block Blob estiver completamente comprometido, filtre o evento para chamadas `CopyBlob` , ou REST `PutBlob` `PutBlockList` `FlushWithClose` API. Estas chamadas API desencadeiam o evento **Microsoft.Storage.BlobCreated** apenas depois de os dados estiverem totalmente comprometidos com uma Blob de Bloco. Para aprender a criar um filtro, consulte [eventos de filtro para grelha de eventos.](../../event-grid/how-to-filter-events.md)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grade de Eventos e dê uma oportunidade aos eventos de armazenamento blob:

- [Sobre o Event Grid](../../event-grid/overview.md)
- [Esquema de eventos de armazenamento de blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Rota Blob Eventos de armazenamento para um ponto final web personalizado](storage-blob-event-quickstart.md)
