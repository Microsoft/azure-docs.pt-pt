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
ms.openlocfilehash: 78ec5b6d330f03d78dcb4e798b23d588fd93398e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387182"
---
# <a name="reacting-to-blob-storage-events"></a>Reagindo a eventos de armazenamento blob

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

Se a sua conta tiver um espaço de nome hierárquico, este tutorial irá mostrar-lhe como ligar uma subscrição da Rede de Eventos, uma Função Azure e um [Trabalho](https://docs.azuredatabricks.net/user-guide/jobs.html) em Tijolos de Dados Azure: [Tutorial: Use eventos azure Data Lake Storage Gen2 para atualizar uma tabela Databricks Delta](data-lake-storage-events.md).

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

Para combinar com os eventos de bolhas criadas num conjunto de recipientes que partilham um prefixo, utilize um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containerprefix
```

Para combinar com os eventos de bolhas criadas em recipienteespecífico, utilize um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containername/
```

Para combinar com os eventos de bolhas criadas em recipientes específicos que partilham um prefixo de nome blob, utilize um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para combinar com os eventos de bolhas criadas em recipientes específicos que partilham um sufixo blob, utilize um filtro `subjectEndsWith` como ".log" ou ".jpg". Para mais informações, consulte [Conceitos de Grelha](../../event-grid/concepts.md#event-subscriptions)de Eventos .

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos

As aplicações que lidam com eventos de armazenamento blob devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que vem da conta de armazenamento que você está esperando.
> * Da mesma forma, verifique se o eventoType é um que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
> * Como as mensagens podem chegar fora de ordem e depois de algum atraso, use os campos de etag para entender se a sua informação sobre objetos ainda está atualizada.  Além disso, utilize os campos de sequenciadores para entender a ordem dos acontecimentos em qualquer objeto em particular.
> * Utilize o campo blobType para entender que tipo de operações são permitidas na bolha e quais os tipos de biblioteca do cliente que deve usar para aceder à bolha. Os valores válidos são `BlockBlob` ou `PageBlob`. 
> * Utilize o campo de url com os construtores `CloudBlockBlob` e `CloudAppendBlob` para aceder à bolha.
> * Ignore campos que não entende. Esta prática ajudará a mantê-lo resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
> * Se pretender garantir que o evento **Microsoft.Storage.BlobCreated** só é acionado quando um Block Blob estiver completamente comprometido, filtre o evento para o `CopyBlob`, `PutBlob`, `PutBlockList` ou `FlushWithClose` chamadas API REST. Estas chamadas API desencadeiam o evento **Microsoft.Storage.BlobCreated** apenas após os dados estartotalmente comprometidos com um Block Blob. Para aprender a criar um filtro, consulte [os eventos de filtragem para a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grelha de Eventos e dê aos eventos de armazenamento blob uma tentativa:

- [Sobre o Event Grid](../../event-grid/overview.md)
- [Route Blob storage Events para um ponto final web personalizado](storage-blob-event-quickstart.md)
