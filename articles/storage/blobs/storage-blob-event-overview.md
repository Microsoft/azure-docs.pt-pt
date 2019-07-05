---
title: Reagir a eventos de armazenamento de Blobs do Azure | Documentos da Microsoft
description: Utilize a Azure Event Grid para subscrever a eventos de armazenamento de Blobs.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444685"
---
# <a name="reacting-to-blob-storage-events"></a>Reagir a eventos de armazenamento de BLOBs

Eventos de armazenamento do Azure permitem que os aplicativos reagir a eventos, como a criação e eliminação de blobs, usando as modernas arquiteturas sem servidor. Ele faz isso sem a necessidade de código complicado ou serviços de consulta dispendiosa e ineficiente.

Em vez disso, os eventos são enviados por meio [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para assinantes, como as funções do Azure, Azure Logic Apps, ou até mesmo seu próprio serviço de escuta de http e pague apenas aquilo que utiliza.

Eventos de armazenamento de Blobs com fiabilidade são enviados para o serviço do Event Grid que fornece serviços de entrega fiável às suas aplicações através de políticas de repetição sofisticado e entrega de mensagens não entregues.

Cenários de evento de armazenamento de BLOBs comuns incluem o processamento de vídeo ou imagem, a indexação da pesquisa ou qualquer fluxo de trabalho orientados por arquivos. Carregamentos de ficheiros assíncronos são uma excelente escolha para eventos. Quando as alterações são pouco frequentes, mas o seu cenário requer a capacidade de resposta imediata, com base em eventos arquitetura pode ser especialmente eficiente.

Se quiser experimentar isso agora, veja qualquer um dos seguintes artigos de início rápido:

|Se pretender utilizar esta ferramenta:    |Veja este artigo: |
|--|-|
|Portal do Azure    |[Quickstart: Encaminhar eventos de armazenamento de BLOBs para o ponto final da web com o portal do Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|CLI do Azure    |[Quickstart: Encaminhar eventos de armazenamento para o ponto final da web com o PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Quickstart: Encaminhar eventos de armazenamento para o ponto final da web com a CLI do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>O modelo de evento

Utiliza o Event Grid [subscrições de eventos](../../event-grid/concepts.md#event-subscriptions) encaminhar mensagens de eventos para os assinantes. Esta imagem ilustra a relação entre os publicadores de eventos, subscrições de eventos e manipuladores de eventos.

![Modelo de grade do evento](./media/storage-blob-event-overview/event-grid-functional-model.png)

Em primeiro lugar, inscreva-se um ponto final para um evento. Em seguida, quando um evento é disparado, o serviço do Event Grid enviará dados sobre esse evento para o ponto final.

Consulte a [esquema de eventos de armazenamento de BLOBs](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) artigo para ver:

> [!div class="checklist"]
> * Uma lista completa de eventos de armazenamento de BLOBs e como cada evento é acionado.
> * Enviaria-se um exemplo dos dados do Event Grid para cada um desses eventos.
> * A finalidade de cada par de chave-valor que aparece nos dados.

## <a name="filtering-events"></a>Filtragem de eventos

Subscrições de eventos de blob podem ser filtradas com base no tipo de evento e, pelo nome do contentor e o nome de blob do objeto que foi criado ou eliminado.  Pode aplicar os filtros para subscrições de eventos ou durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) a subscrição de evento ou [num momento posterior](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Filtros de requerente em projetos do Event Grid com base no "começa com" e "termina com" correspondências, para que os eventos com um assunto correspondente são entregues ao subscritor.

Para saber mais sobre como aplicar filtros, veja [filtrar eventos do Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

O assunto de eventos de armazenamento de BLOBs utiliza o formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Para fazer corresponder a todos os eventos para uma conta de armazenamento, pode deixar os filtros de requerente vazio.

Para fazer corresponder os eventos a partir de blobs criados num conjunto de contentores partilha um prefixo, utilize um `subjectBeginsWith` filtrar, como:

```
/blobServices/default/containers/containerprefix
```

Para fazer corresponder os eventos a partir de blobs criados no contentor específico, utilize um `subjectBeginsWith` filtrar, como:

```
/blobServices/default/containers/containername/
```

Para fazer corresponder os eventos a partir de blobs criados no contentor específico partilha um prefixo de nome de blob, utilize um `subjectBeginsWith` filtrar, como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para fazer corresponder os eventos a partir de blobs criados no contentor específico partilha um sufixo de blob, utilize um `subjectEndsWith` filtro como ". log" ou ". jpg". Para obter mais informações, consulte [conceitos do Event Grid](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Práticas recomendadas para o consumo de eventos

Aplicações que processam os eventos de armazenamento de BLOBs devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, é importante não partem do princípio de eventos são de uma origem específica, mas para verificar o tópico da mensagem para se certificar de que trata da conta de armazenamento que está esperando.
> * Da mesma forma, verifique se o eventType é um estão preparados para o processo e não partem do princípio de que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e, depois de algum atraso, utilize os campos de etag para saber se as informações sobre objetos são ainda atualizadas.  Além disso, utilize os campos do sequenciador para compreender a ordem dos eventos em qualquer objeto específico.
> * Utilize o campo de blobType para compreender o tipo das operações são permitidos no blob e que biblioteca de cliente tipos que devem utilizar para aceder ao blob. Os valores válidos são `BlockBlob` ou `PageBlob`. 
> * Utilize o campo de url com o `CloudBlockBlob` e `CloudAppendBlob` construtores para aceder ao blob.
> * Ignore campos que não compreende. Essa prática ajuda a manter-se resiliente aos novos recursos que podem ser adicionados no futuro.
> * Se pretender certificar-se de que o **Microsoft.Storage.BlobCreated** evento é acionado apenas quando um Blob de blocos está totalmente comprometido, filtrar o evento para o `CopyBlob`, `PutBlob`, `PutBlockList` ou `FlushWithClose` REST Chamadas de API. Estes acionador de chamadas de API a **Microsoft.Storage.BlobCreated** evento apenas depois de dados estão totalmente comprometidos com um Blob de blocos. Para saber como criar um filtro, veja [filtrar eventos do Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Event Grid e dê uma chance de eventos de armazenamento de BLOBs:

- [Sobre o Event Grid](../../event-grid/overview.md)
- [Encaminhar eventos de armazenamento de BLOBs para um ponto final web personalizado](storage-blob-event-quickstart.md)
