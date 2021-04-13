---
title: Personalize um modelo de Marcas com API indexante de vídeo
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo de Marcas com a API do Indexante de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 03c74e9108b27856259eb4f17d7ba521007c4fa3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312965"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalize um modelo de Marcas com a API indexante de vídeo

O Video Indexer suporta a deteção de marcas a partir de texto sonoro e visual durante a indexação e reindexação de conteúdos de vídeo e áudio. A funcionalidade de deteção de marcas identifica menções de produtos, serviços e empresas sugeridas pela base de dados de marcas da Bing. Por exemplo, se a Microsoft for mencionada em conteúdo sonoro ou vídeo ou se aparecer em texto visual num vídeo, o Video Indexer deteta-o como uma marca no conteúdo. Um modelo personalizado da Brands permite-lhe excluir certas marcas de serem detetadas e incluir marcas que devam fazer parte do seu modelo que pode não estar na base de dados de marcas da Bing. Para obter mais informações, veja [Descrição geral](customize-brands-model-overview.md).

> [!NOTE]
> Se o seu vídeo foi indexado antes de adicionar uma marca, precisa reindexá-lo.

Pode utilizar as APIs do Indexante de Vídeo para criar, utilizar e editar modelos de Marcas personalizadas detetados num vídeo, conforme descrito neste tópico. Também pode utilizar o website do Índice de Vídeo, conforme descrito no [modelo Customize Brands utilizando o website do Índice de Vídeo.](customize-brands-model-with-api.md)

## <a name="create-a-brand"></a>Criar uma marca

A [criação de uma marca](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Brand) API cria uma nova marca personalizada e adiciona-a ao modelo personalizado marcas para a conta especificada.

> [!NOTE]
> Configurar `enabled` (no corpo) para verdadeiro coloca a marca na lista *incluir* para o Indexer de Vídeo detetar. Configurar `enabled` para falso coloca a marca na lista De *excluir,* para que o Indexante de Vídeo não a detete.

Alguns outros parâmetros que pode definir no corpo:

* O `referenceUrl` valor pode ser qualquer website de referência para a marca, como um link para a sua página da Wikipédia.
* O `tags` valor é uma lista de tags para a marca. Esta etiqueta aparece no campo *categoria* da marca no site do Video Indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Cloud".

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que acaba de criar seguindo o formato do exemplo abaixo.

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

A [API de exclusão de uma marca](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Brand) remove uma marca do modelo de Marcas personalizadas para a conta especificada. A conta está especificada no `accountId` parâmetro. Uma vez chamada com sucesso, a marca deixará de estar na lista de marcas *Incluir* ou *Excluir.*

### <a name="response"></a>Resposta

Não há conteúdo devolvido quando a marca é eliminada com sucesso.

## <a name="get-a-specific-brand"></a>Obtenha uma marca específica

A [API obtém uma marca](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brand) que permite pesquisar os detalhes de uma marca no modelo de Marcas personalizadas para a conta especificada usando o ID da marca.

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que procurou (utilizando o ID da marca) seguindo o formato do exemplo abaixo.

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
> `enabled` ser definido para `true` significar que a marca está na lista de *Incluir* para Indexer de Vídeo para detetar, e ser falso significa que a marca está na lista `enabled` De *excluir,* para que o Video Indexer não a detete.

## <a name="update-a-specific-brand"></a>Atualizar uma marca específica

A [atualização de uma marca](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brand) API permite-lhe pesquisar os detalhes de uma marca no modelo de Marcas personalizadas para a conta especificada usando o ID da marca.

### <a name="response"></a>Resposta

A resposta fornece as informações atualizadas sobre a marca que atualizou seguindo o formato do exemplo abaixo.

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

## <a name="get-all-of-the-brands"></a>Obtenha todas as marcas

A [API recebe todas as marcas](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API devolve todas as marcas no modelo de Marcas personalizadas para a conta especificada, independentemente de a marca estar na lista de marcas *Incluir* ou *Excluir.*

### <a name="response"></a>Resposta

A resposta fornece uma lista de todas as marcas na sua conta e cada um dos seus detalhes seguindo o formato do exemplo abaixo.

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
> A marca chamada *Exemplo* está na lista *de Incluir* para O Indexer de Vídeo detetar, e a marca chamada *Exemplo2* está na lista *De Excluir,* pelo que o Video Indexer não o detetará.

## <a name="get-brands-model-settings"></a>Obtenha configurações de modelos de marcas

As [definições de marcas get](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API devolvem as definições de modelos de Marcas na conta especificada. As definições de modelos marcas representam se a deteção da base de dados das marcas Bing está ativada ou não. Se as marcas Bing não estiverem ativadas, o Video Indexer apenas detetará marcas a partir do modelo personalizado de Marcas da conta especificada.

### <a name="response"></a>Resposta

A resposta mostra se as marcas Bing estão habilitadas seguindo o formato do exemplo abaixo.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` ser definido para verdadeiro representa que as marcas Bing estão habilitadas. Se `useBuiltin` for falso, as marcas Bing são desativadas. O `state` valor pode ser ignorado porque foi depreciado.

## <a name="update-brands-model-settings"></a>Atualizar configurações de modelos de Marcas

A [atualização das marcas](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brands-Model-Settings) API atualiza as definições de modelos de Marcas na conta especificada. As definições de modelos marcas representam se a deteção da base de dados das marcas Bing está ativada ou não. Se as marcas Bing não estiverem ativadas, o Video Indexer apenas detetará marcas a partir do modelo personalizado de Marcas da conta especificada.

A `useBuiltIn` bandeira definida para a verdade significa que as marcas Bing estão ativadas. Se `useBuiltin` for falso, as marcas Bing são desativadas.

### <a name="response"></a>Resposta

Não há conteúdo devolvido quando a definição do modelo Brands é atualizada com sucesso.

## <a name="next-steps"></a>Passos seguintes

[Personalize o modelo de Marcas usando o site](customize-brands-model-with-website.md)
