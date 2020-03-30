---
title: Reagindo aos eventos de armazenamento da Blob Azure Microsoft Docs
description: Utilize a Azure Event Grid para subscrever a eventos de armazenamento de Blobs.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268499"
---
# <a name="reacting-to-blob-storage-events"></a>Reagir aos eventos de armazenamento de Blobs

Os eventos de Armazenamento Azure permitem que as aplicações reajam a eventos, como a criação e eliminação de bolhas. Fá-lo sem a necessidade de código sumo ou serviços de sondagens dispendiosos e ineficientes.

Os eventos são impulsionados usando [a Grelha de Eventos Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como Funções Azure, Aplicações Lógicas Azure, ou mesmo para o seu próprio ouvinte. A melhor parte é que só pagas pelo que usas.

O armazenamento blob envia eventos para event grid que fornece entrega confiável de eventos às suas aplicações através de políticas ricas de retry e letras mortas.

Os cenários comuns de armazenamento blob incluem processamento de imagem ou vídeo, indexação de pesquisa ou qualquer fluxo de trabalho orientado para ficheiros. Uploads de ficheiros assíncronos são um ótimo ajuste para eventos. Quando as mudanças são pouco frequentes, mas o seu cenário requer capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficiente.

Se quiser experimentar isto agora, veja qualquer um destes artigos de arranque rápido:

|Se quiser utilizar esta ferramenta:    |Consulte este artigo: |
|--|-|
|Portal do Azure    |[Quickstart: Route Blob storage events to web endpoint with the Azure portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Quickstart: Eventos de armazenamento de rotas para ponto final web com PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|CLI do Azure    |[Quickstart: Route storage events to web endpoint with Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Para ver exemplos aprofundados de reagir a eventos de armazenamento blob utilizando funções Azure, consulte estes artigos:

- [Tutorial: Utilize eventos azure data lake storage Gen2 para atualizar uma tabela Delta databricks](data-lake-storage-events.md).
- [Tutorial: Automatizar imagens carregadas usando a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Apenas contas de armazenamento do tipo **StorageV2 (finalidade geral v2)** e integração de eventos de suporte **blobStorage.** **O armazenamento (propósito genral v1)** *não* suporta a integração com a Grelha de Eventos.

## <a name="the-event-model"></a>O modelo do evento

A Event Grid utiliza subscrições de [eventos](../../event-grid/concepts.md#event-subscriptions) para direcionar as mensagens de eventos para assinantes. Esta imagem ilustra a relação entre editores de eventos, subscrições de eventos e manipuladores de eventos.

![Modelo de grelha de eventos](./media/storage-blob-event-overview/event-grid-functional-model.png)

Primeiro, subscreva um ponto final para um evento. Em seguida, quando um evento é desencadeado, o serviço De Event Grid enviará dados sobre esse evento para o ponto final.

Consulte o artigo [schema](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) de eventos de armazenamento blob para ver:

> [!div class="checklist"]
> * Uma lista completa de eventos de armazenamento blob e como cada evento é desencadeado.
> * Um exemplo dos dados que a Grelha de Eventos enviaria para cada um destes eventos.
> * O propósito de cada par de valor chave que aparece nos dados.

## <a name="filtering-events"></a>Eventos de filtragem

Os [eventos](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) blob podem ser filtrados pelo tipo de evento, nome do recipiente ou nome do objeto que foi criado/eliminado. Os filtros na Grelha de Eventos correspondem ao início ou ao fim do assunto para que os eventos com um sujeito correspondente se desloque ao assinante.

Para saber mais sobre como aplicar filtros, consulte [os eventos de filtragem para a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

O tema dos eventos de armazenamento Blob utiliza o formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Para combinar com todos os eventos para uma conta de armazenamento, pode deixar os filtros do sujeito vazios.

Para combinar com os eventos de bolhas criadas num conjunto `subjectBeginsWith` de recipientes que partilham um prefixo, utilize um filtro como:

```
/blobServices/default/containers/containerprefix
```

Para combinar com os eventos de bolhas `subjectBeginsWith` criadas em recipienteespecífico, utilize um filtro como:

```
/blobServices/default/containers/containername/
```

Para combinar com os eventos de bolhas criadas em recipientes específicos que partilham um prefixo de nome blob, utilize um `subjectBeginsWith` filtro como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para combinar com os eventos de bolhas criadas em recipientes específicos que partilham um sufixo blob, utilize um `subjectEndsWith` filtro como ".log" ou ".jpg". Para mais informações, consulte [Conceitos de Grelha](../../event-grid/concepts.md#event-subscriptions)de Eventos .

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos

As aplicações que lidam com eventos de armazenamento blob devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que vem da conta de armazenamento que você está esperando.
> * Da mesma forma, verifique se o eventoType é um que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
> * Como as mensagens podem chegar após algum atraso, use os campos de etag para entender se a sua informação sobre objetos ainda está atualizada. Para aprender a usar o campo de etag, consulte [Gerir a moeda no armazenamento blob](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * Como as mensagens podem chegar fora de ordem, use os campos de sequenciadores para entender a ordem dos eventos em qualquer objeto em particular. O campo de sequênciar é um valor de cadeia que representa a sequência lógica de eventos para qualquer nome blob em particular. Pode usar a comparação padrão de cordas para entender a sequência relativa de dois eventos com o mesmo nome blob.
> * Utilize o campo blobType para entender que tipo de operações são permitidas na bolha e quais os tipos de biblioteca do cliente que deve usar para aceder à bolha. Valores válidos `PageBlob`são ou `BlockBlob` . 
> * Utilize o campo `CloudBlockBlob` de `CloudAppendBlob` url com o e os construtores para aceder à bolha.
> * Ignore campos que não entende. Esta prática ajudará a mantê-lo resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
> * Se quiser garantir que o evento **Microsoft.Storage.BlobCreated** só é acionado quando um Block `CopyBlob`Blob estiver completamente comprometido, filtre o evento para as `PutBlob`chamadas , `PutBlockList` ou `FlushWithClose` REST API. Estas chamadas API desencadeiam o evento **Microsoft.Storage.BlobCreated** apenas após os dados estartotalmente comprometidos com um Block Blob. Para aprender a criar um filtro, consulte [os eventos de filtragem para a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grelha de Eventos e dê aos eventos de armazenamento blob uma tentativa:

- [Sobre o Event Grid](../../event-grid/overview.md)
- [Route Blob storage Events para um ponto final web personalizado](storage-blob-event-quickstart.md)
