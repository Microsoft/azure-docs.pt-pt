---
title: Utilize a API do Video Indexer para personalizar um modelo de pessoa - Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de pessoa com a API do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 6c4980536eddd0226fac422ae17ddb717e34630d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799476"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalizar um modelo de pessoa com a API do Video Indexer

O Video Indexer suporta a deteção de rostos e reconhecimento de celebridade para conteúdo de vídeo. O recurso de reconhecimento de celebridade abrange aproximadamente os rostos de um milhão com base na origem de dados frequentemente pedidos como IMDB, Wikipedia e influenciadores principais do LinkedIn. Rostos que não são reconhecidos pela funcionalidade de reconhecimento de celebridade são detetados; No entanto, eles são deixados sem nome. Depois de carregar vídeo para o indexador de vídeos e obtenha os resultados, pode voltar atrás e nomear os rostos que não foram reconhecidos. Depois de identificar um rosto com um nome, o mostrador e o nome seja adicionados ao modelo de pessoa da sua conta. Indexador de vídeos, em seguida, irá reconhecer este rostos nos vídeos futuros e vídeos nos últimos.

Pode utilizar a API do indexador de vídeo para editar rostos que foram detetados um vídeo, conforme descrito neste tópico. Também pode utilizar o site do Video Indexer, conforme descrito em [modelo de personalizar pessoa através do site do Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gerir vários modelos de pessoa 

O Video Indexer suporta vários modelos de pessoa por conta. Esta funcionalidade está atualmente disponível apenas através das APIs do indexador de vídeo.

Se a sua conta atende a diferentes cenários de casos de utilização, pode querer criar vários modelos de pessoa por conta. Por exemplo, se seu conteúdo está relacionada com eventos desportivos, em seguida, pode criar um modelo de pessoa separado para cada desporto (futebol americano, basquete, futebol, etc.). 

Depois de criar um modelo, pode usá-lo ao fornecer o ID de modelo de um modelo de pessoa específico quando carregar/indexar ou reindexação um vídeo. Um novo rosto para um vídeo de formação atualiza o modelo personalizado específico que o vídeo foi associado.

Cada conta tem um limite de 50 modelos de pessoa. Se não for necessário o suporte a vários modelo pessoa, não atribua uma pessoa ID de modelo para o seu vídeo quando carregar/indexar ou reindexação. Neste caso, o Video Indexer utiliza o modelo de pessoa personalizado de predefinição na sua conta.

## <a name="create-a-new-person-model"></a>Criar um novo modelo de pessoa

Crie um novo modelo de pessoa na conta especificada. 

### <a name="request-url"></a>URL do Pedido

Este é um pedido POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parâmetros do pedido 

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|name|string|Sim|O nome para o modelo de pessoa|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece que o nome e ID de modelo gerado da pessoa de modelo que acabou de criar seguindo o formato de exemplo abaixo.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Em seguida, deve utilizar o **id** valor para o **personModelId** parâmetro quando [carregar um vídeo para o índice](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexação um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Eliminar um modelo de pessoa

Elimine um modelo de pessoa personalizado da conta especificada. 

Assim que o modelo de pessoa é eliminado com êxito, o índice dos seus vídeos atuais que estava a utilizar o modelo eliminado permanecerá inalterado, até que reindexá-los. Após a reindexação, os rostos que eram nomeados no modelo eliminado não serão reconhecidos pelo indexador de vídeo nos seus vídeos atuais que foram indexados com esse modelo; No entanto, esses rostos ainda serão detetados. Seus vídeos atuais que foram indexados com o modelo eliminado agora irão utilizar o modelo de pessoa da sua conta padrão. Se rostos do modelo eliminado também são nomeados no modelo de predefinição da sua conta, esses rostos continuará a ser reconhecida nos vídeos.

### <a name="request-url"></a>URL do Pedido

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Segue-se o pedido no Curl.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|id|string|Sim|O id de modelo de pessoa (gerado quando é criado o modelo de pessoa)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando o modelo de pessoa é eliminado com êxito.

## <a name="get-all-person-models"></a>Obter todos os modelos de pessoa

Obter todos os modelos de pessoa na conta especificada. 

### <a name="request-call"></a>Chamada de pedido

Este é um pedido GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todos os modelos de pessoa na sua conta (incluindo o modelo de pessoa padrão na conta especificada) e cada um dos seus nomes e os ids de seguir o formato de exemplo abaixo.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Pode escolher qual modelo de que pretende utilizar para ver um vídeo com o **id** valor do modelo de pessoa para o **personModelId** parâmetro quando [carregar um vídeo para o índice](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexação um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Atualizar um rosto

Este comando permite-lhe atualizar um rosto no seu vídeo com um nome com o ID do vídeo e o ID do mostrador da. Este, em seguida, atualiza o modelo de pessoa que o vídeo foi associado ao carregar/indexar ou reindexação. Não se foi atribuído nenhum modelo de pessoa, ele atualiza o modelo de pessoa da conta padrão. 

Quando isso acontecer, ele reconhece as ocorrências do mostrador da mesma nos seus vídeos atuais que partilham o mesmo modelo de pessoa. Reconhecimento de rosto nos seus outros vídeos atuais poderá demorar algum tempo a entrar em vigor, como se trata de um processo em lotes.

Pode atualizar um rosto que Video Indexer reconhecida como uma celebridade com um novo nome. O novo nome que dar irá ter precedência sobre o reconhecimento de celebridade incorporada.

### <a name="request-call"></a>Chamada de pedido

Este é um pedido POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Segue-se o pedido no Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|videoId|string|Sim|ID do vídeo em que aparece o mostrador que pretende atualizar. Isso é criado quando o vídeo é carregado e indexado.|
|faceId|inteiro|Sim|ID para o rosto que será atualizado. Pode obter o face ID de índice de vídeos|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|
|name|string|Sim|Novo nome para atualizar o mostrador com.|

Os nomes são exclusivos para modelos de pessoa, portanto, se fornecer dois diferentes rostos na mesma pessoa modelam o mesmo **nome** valor do parâmetro, o Video Indexer visualiza os rostos como a mesma pessoa e converge-las assim que a reindexar o seu vídeo. 

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

Não existe nenhum conteúdo retornado quando o mostrador foi atualizado com êxito.

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o modelo de pessoa através do site do Video Indexer](customize-person-model-with-website.md)
