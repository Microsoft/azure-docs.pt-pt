---
title: Usar o Video Indexer do Azure para personalizar o modelo de marcas
titleSuffix: Azure Media Services
description: Este artigo demonstra como usar o Video Indexer do Azure para personalizar o modelo de marcas.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 81ba4cc7be5f9361d21aaea2ba78d0fd6f0f8c95
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289922"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizar um modelo de marcas com a API de Video Indexer

O Video Indexer dá suporte à detecção de marcas de fala e texto visual durante a indexação e a reindexação de conteúdo de vídeo e áudio. O recurso de detecção de marca identifica as mencionações de produtos, serviços e empresas sugeridas pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft for mencionada em um conteúdo de vídeo ou áudio ou se aparecer no texto visual em um vídeo, Video Indexer o detectará como uma marca no conteúdo. Um modelo de marcas personalizadas permite que você exclua determinadas marcas de serem detectadas e inclua marcas que devem ser parte do modelo que podem não estar no banco de dados de marcas do Bing.

Para obter uma visão geral detalhada, consulte [visão geral](customize-brands-model-overview.md).

Você pode usar as APIs de Video Indexer para criar, usar e editar modelos de marcas personalizadas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar o site Video Indexer, conforme descrito em [Personalizar o modelo de marcas usando o site do video indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Criar uma marca

A API [criar uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) cria uma nova marca personalizada e a adiciona ao modelo de marcas personalizadas para a conta especificada. 

> [!NOTE]
> A configuração **habilitado** (no corpo) como true coloca a marca na lista de *inclusão* para video indexer a ser detectada. A configuração **habilitado** como false coloca a marca na lista de *exclusões* , de modo que Video indexer não a detectará.

Alguns outros parâmetros que podem ser definidos no corpo:

* O valor de **referenceUrl** pode ser qualquer site de referência para a marca, como um link para sua página da Wikipédia.
* O valor das **marcas** é uma lista de marcas para a marca. Isso aparece no campo *categoria* da marca no site video indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "nuvem".

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que você acabou de criar seguindo o formato do exemplo abaixo.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Excluir uma marca

A API [excluir uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) remove uma marca do modelo de marcas personalizadas da conta especificada. A conta é especificada no parâmetro **AccountId** . Uma vez chamada com êxito, a marca não estará mais nas listas de *inclusão* ou *exclusão* de marcas.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando a marca é excluída com êxito.

## <a name="get-a-specific-brand"></a>Obter uma marca específica

A API [obter uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) permite pesquisar os detalhes de uma marca no modelo de marcas personalizadas para a conta especificada usando a ID da marca.

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que você pesquisou (usando a ID da marca) seguindo o formato do exemplo abaixo.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> **habilitado** sendo definido como **verdadeiro** significa que a marca está na lista de *inclusão* para video indexer a ser detectada e **habilitada** como falsa significa que a marca está na lista de *exclusões* , portanto Video indexer não a detectará.

## <a name="update-a-specific-brand"></a>Atualizar uma marca específica

A API [atualizar uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) permite pesquisar os detalhes de uma marca no modelo de marcas personalizadas para a conta especificada usando a ID da marca.

### <a name="response"></a>Resposta

A resposta fornece as informações atualizadas sobre a marca que você atualizou seguindo o formato do exemplo abaixo.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Obter todas as marcas

A API [obter todas as marcas](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) retorna todas as marcas no modelo de marcas personalizadas para a conta especificada, independentemente de a marca estar destinada a estar na lista *incluir* ou *excluir* marcas.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todas as marcas em sua conta e cada um de seus detalhes seguindo o formato do exemplo abaixo.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> A marca chamada *example* está na lista *include* de video indexer a serem detectadas e a marca chamada *example2* está na lista *Exclude* , de modo que Video indexer não a detectará.

## <a name="get-brands-model-settings"></a>Obter configurações de modelo de marcas

A API [obter configurações de marcas](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) retorna as configurações do modelo de marcas na conta especificada. As configurações do modelo de marcas representam se a detecção do banco de dados de marcas do Bing está habilitada ou não. Se as marcas do Bing não estiverem habilitadas, Video Indexer detectará apenas marcas do modelo de marcas personalizadas da conta especificada.

### <a name="response"></a>Resposta

A resposta mostra se as marcas do Bing estão habilitadas seguindo o formato do exemplo abaixo.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** sendo definido como true representa que as marcas do Bing estão habilitadas. Se *useBuiltin* for false, as marcas do Bing serão desabilitadas. O valor de **estado** pode ser ignorado, pois foi preterido.

## <a name="update-brands-model-settings"></a>Configurações do modelo de marcas de atualização

As [marcas de atualização](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) atualiza as configurações do modelo de marcas na conta especificada. As configurações do modelo de marcas representam se a detecção do banco de dados de marcas do Bing está habilitada ou não. Se as marcas do Bing não estiverem habilitadas, Video Indexer detectará apenas marcas do modelo de marcas personalizadas da conta especificada.

O sinalizador **useBuiltIn** definido como true representa que as marcas do Bing estão habilitadas. Se *useBuiltin* for false, as marcas do Bing serão desabilitadas.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando a configuração do modelo de marcas é atualizada com êxito.

## <a name="next-steps"></a>Passos seguintes

[Personalizar o modelo de marcas usando o site](customize-brands-model-with-website.md)
