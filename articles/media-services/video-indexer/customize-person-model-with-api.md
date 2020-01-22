---
title: Usar a API de Video Indexer para personalizar um modelo Person-Azure
titleSuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo Person com a API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 370e9e515359e2e2e598db90aa379f796b13c3fe
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292404"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalizar um modelo Person com a API Video Indexer

Video Indexer dá suporte à detecção de face e ao reconhecimento de celebridade para conteúdo de vídeo. O recurso de reconhecimento de celebridade abrange aproximadamente 1 milhão rostos com base na fonte de dados solicitada comumente, como o IMDB, a Wikipédia e os principais influenciadores do LinkedIn. As faces que não são reconhecidas pelo recurso de reconhecimento de celebridade são detectadas; no entanto, elas são deixadas sem nome. Depois de carregar seu vídeo para Video Indexer e obter resultados de volta, você pode voltar e nomear as faces que não foram reconhecidas. Depois de rotular uma face com um nome, a face e o nome serão adicionados ao modelo Person da sua conta. Video Indexer, em seguida, reconhecerá essa face em seus vídeos futuros e vídeos anteriores.

Você pode usar a API Video Indexer para editar faces que foram detectadas em um vídeo, conforme descrito neste tópico. Você também pode usar o site Video Indexer, conforme descrito em [Personalizar modelo de pessoa usando o site do video indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gerenciando modelos de várias pessoas 

Video Indexer dá suporte a vários modelos de pessoa por conta. Esse recurso está atualmente disponível somente por meio das APIs de Video Indexer.

Se sua conta atender a diferentes cenários de caso de uso, talvez você queira criar vários modelos de pessoa por conta. Por exemplo, se o conteúdo estiver relacionado a esportes, você poderá criar um modelo de pessoa separado para cada esporte (futebol, basquete, futebol, etc.). 

Depois que um modelo é criado, você pode usá-lo fornecendo a ID do modelo de um modelo Person específico ao carregar/indexar ou reindexar um vídeo. Treinar uma nova face para um vídeo atualiza o modelo personalizado específico ao qual o vídeo foi associado.

Cada conta tem um limite de 50 modelos de pessoa. Se você não precisar do suporte ao modelo de várias pessoas, não atribua uma ID de modelo de pessoa ao seu vídeo ao carregar/indexar ou reindexar. Nesse caso, Video Indexer usa o modelo de pessoa personalizada padrão em sua conta.

## <a name="create-a-new-person-model"></a>Criar um novo modelo de pessoa

Para criar um novo modelo de pessoa na conta especificada, use a API [criar um modelo Person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) .

A resposta fornece o nome e a ID de modelo gerada do modelo Person que você acabou de criar, seguindo o formato do exemplo abaixo.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Em seguida, você deve usar o valor de **ID** para o parâmetro **personModelId** ao [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Excluir um modelo Person

Para excluir um modelo de pessoa personalizada da conta especificada, use a API de [modelo excluir uma pessoa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) . 

Depois que o modelo Person for excluído com êxito, o índice dos seus vídeos atuais que estavam usando o modelo excluído permanecerá inalterado até que você os reindexe. Após a reindexação, as faces que foram nomeadas no modelo excluído não serão reconhecidas pelo Video Indexer em seus vídeos atuais que foram indexados usando esse modelo; no entanto, essas faces ainda serão detectadas. Seus vídeos atuais que foram indexados usando o modelo excluído agora usarão o modelo de pessoa padrão da sua conta. Se as faces do modelo excluído também forem nomeadas no modelo padrão da sua conta, essas faces continuarão a ser reconhecidas nos vídeos.

Não há nenhum conteúdo retornado quando o modelo Person é excluído com êxito.

## <a name="get-all-person-models"></a>Obter todos os modelos de pessoas

Para obter todos os modelos de pessoa na conta especificada, use a API [obter um modelo Person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) .

A resposta fornece uma lista de todos os modelos de pessoa em sua conta (incluindo o modelo de pessoa padrão na conta especificada) e cada um de seus nomes e IDs seguindo o formato do exemplo abaixo.

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

Você pode escolher o modelo que deseja usar para um vídeo usando o valor de **ID** do modelo Person para o parâmetro **personModelId** ao [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Atualizar uma face

Esse comando permite que você atualize uma face em seu vídeo com um nome usando a ID do vídeo e a ID da face. Isso atualiza o modelo Person ao qual o vídeo foi associado após o carregamento/indexação ou a reindexação. Se nenhum modelo de pessoa foi atribuído, ele atualiza o modelo de pessoa padrão da conta. 

Quando isso acontece, ele reconhece as ocorrências da mesma face em seus outros vídeos atuais que compartilham o mesmo modelo de pessoa. O reconhecimento da face em seus outros vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lote.

Você pode atualizar uma face que Video Indexer reconhecida como um celebridade com um novo nome. O novo nome que você atribuir terá precedência sobre o reconhecimento de celebridade interno.

Para atualizar a face, use [atualizar uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) API de face de vídeo.

Os nomes são exclusivos para modelos de pessoa, portanto, se você fornecer duas faces diferentes no mesmo modelo de pessoa com o mesmo valor de parâmetro de **nome** , Video indexer exibirá os rostos como a mesma pessoa e os convergirá quando você reindexar o vídeo. 

## <a name="next-steps"></a>Passos seguintes

[Personalizar o modelo Person usando o site Video Indexer](customize-person-model-with-website.md)
