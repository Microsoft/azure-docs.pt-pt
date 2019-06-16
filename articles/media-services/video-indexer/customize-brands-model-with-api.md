---
title: Utilize o indexador de vídeo do Azure para personalizar o modelo de marcas
titlesuffix: Azure Media Services
description: Este artigo mostra como utilizar o Video Indexer do Azure para personalizar o modelo de marcas.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8d0806bc0262cd45a49e4f97ea629683ac239aa8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799646"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizar um modelo de marcas com a API do Video Indexer

O Video Indexer suporta a deteção de marca de voz e texto visual durante a indexação e a reindexação de conteúdo de áudio e vídeo. A funcionalidade de deteção de marca identifica menções de produtos, serviços, e as empresas sugerido pela base de dados de marcas do Bing. Por exemplo, se a Microsoft é mencionado num conteúdo de vídeo ou áudio, ou se ele aparece no texto visual num vídeo, o indexador de vídeos Deteta-o como uma marca no conteúdo. Um modelo de marcas personalizado permite-lhe excluir determinadas marcas da que está a ser detetado e incluem marcas que devem fazer parte de seu modelo que pode não estar na base de dados de marcas do Bing.

Para obter uma descrição detalhada, consulte [descrição geral](customize-brands-model-overview.md).

Pode utilizar as APIs do indexador de vídeo para criar, usar e editar os modelos de marcas personalizados, detetados num vídeo, conforme descrito neste tópico. Também pode utilizar o site do Video Indexer, conforme descrito em [modelo de marcas de personalizar através do site do Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Criar uma marca

Isso cria uma nova marca personalizada e adiciona-o para o modelo de marcas personalizado para a conta especificada.

### <a name="request-url"></a>URL do Pedido

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Além desses parâmetros, tem de fornecer um objeto JSON de corpo de pedido que fornece informações sobre a nova marca seguindo o formato de exemplo abaixo.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

A definição **ativada** para true coloca a marca *inclusão* lista para o indexador de vídeo detetar. A definição **ativada** como false coloca a marca *excluir* listar, para que o indexador de vídeo não irá detetá-lo.

O **referenceUrl** valor pode ser nenhum site de referência para a marca como um link para sua página da Wikipédia.

O **etiquetas** valor é uma lista de etiquetas para a marca. Isto aparece a marca *categoria* campo no Web site do Video Indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que acabou de criar ao seguir o formato de exemplo abaixo.

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

## <a name="delete-a-brand"></a>Eliminar uma marca

Remove uma marca do modelo de marcas personalizado para a conta especificada. A conta é especificada na **accountId** parâmetro. Assim que a chamada com êxito, a marca deixará de estar no *inclusão* ou *excluir* marcas de listas.

### <a name="request-url"></a>URL do Pedido

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|id|inteiro|Sim|O id da marca (gerado quando a marca foi criada)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

Não há nenhum conteúdo devolvido quando a marca é eliminada com êxito.

## <a name="get-a-specific-brand"></a>Obtenha uma marca específica

Isto permite-lhe procurar os detalhes de uma marca no modelo de marcas personalizada para a conta especificada com o id de marca.

### <a name="request-url"></a>URL do Pedido

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|id|inteiro|Sim|O ID da marca (gerado quando a marca foi criada)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que procurou (usando o ID da marca) seguindo o formato de exemplo abaixo.

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
> **ativada** sendo definido como **verdadeira** significa que a marca está no *incluir* lista para o indexador de vídeo detetar, e **ativada** a ser false significa que o está a marca a *excluir* listar, para que o indexador de vídeo não irá detetá-lo.

## <a name="update-a-specific-brand"></a>Atualizar uma marca específica

Isto permite-lhe procurar os detalhes de uma marca no modelo de marcas personalizada para a conta especificada com o ID da marca.

### <a name="request-url"></a>URL do Pedido

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|id|inteiro|Sim|O ID da marca (gerado quando a marca foi criada)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Além desses parâmetros, tem de fornecer um objeto JSON de corpo de pedido que fornece informações atualizadas sobre a marca que deseja atualizar seguindo o formato de exemplo abaixo.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> Neste exemplo, a marca que foi criada no corpo do pedido de exemplo no **criar uma marca** secção está a ser atualizada aqui com uma nova etiqueta e descrição de novo. O **habilitado** valor também foi alterado para false colocá-lo na *excluir* lista.

### <a name="response"></a>Resposta

A resposta fornece as informações atualizadas sobre a marca que atualizado seguindo o formato de exemplo abaixo.

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

Esta ação devolve todas as marcas no modelo de marcas personalizado para a conta especificada, independentemente se a marca deve ser na *inclusão* ou *excluir* lista de marcas.

### <a name="request-url"></a>URL do Pedido

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todas as marcas na sua conta e cada um dos respetivos detalhes seguindo o formato de exemplo abaixo.

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
> A marca com o nome *exemplo* está no *inclusão* lista para o indexador de vídeo para detetar e a marca com o nome *Example2* está no *excluir* lista , para que o indexador de vídeo não irá detetá-lo.

## <a name="get-brands-model-settings"></a>Obter definições do modelo de marcas

Esta ação devolve as definições de modelo de marcas na conta especificada. As definições de modelo de marcas representam se a deteção da base de dados de marcas do Bing está ativada ou não. Se as marcas do Bing não estiverem ativadas, o Video Indexer irá detetar apenas marcas do modelo de marcas personalizada da conta especificada.

### <a name="request-url"></a>URL do Pedido

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta mostra se marcas do Bing estão ativadas, seguindo o formato de exemplo abaixo.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** a ser definido como verdadeira representa esse Bing marcas estão ativadas. Se *useBuiltin* é false, marcas de Bing estão desativadas. O **estado** valor pode ser ignorado como ele foi preterido.

## <a name="update-brands-model-settings"></a>Atualizar as definições do modelo de marcas

Este procedimento atualiza as definições de modelo de marcas na conta especificada. As definições de modelo de marcas representam se a deteção da base de dados de marcas do Bing está ativada ou não. Se as marcas do Bing não estiverem ativadas, o Video Indexer irá detetar apenas marcas do modelo de marcas personalizada da conta especificada.

### <a name="request-url"></a>URL do pedido:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Além desses parâmetros, tem de fornecer um objeto JSON de corpo de pedido que fornece informações sobre a nova marca seguindo o formato de exemplo abaixo.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** a ser definido como verdadeira representa esse Bing marcas estão ativadas. Se *useBuiltin* é false, marcas de Bing estão desativadas.

### <a name="response"></a>Resposta

Não existe nenhum conteúdo devolvido quando a definição de modelo de marcas é atualizada com êxito.

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o modelo de marcas usando o Web site](customize-brands-model-with-website.md)
