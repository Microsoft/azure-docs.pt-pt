---
title: Personalize um modelo pessoa com API indexante de vídeo
titleSuffix: Azure Media Services
description: Aprenda a personalizar um modelo Pessoa com a API do Indexer de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127897"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalize um modelo pessoa com a API do Indexer de Vídeo

O Indexer de vídeo suporta a deteção facial e o reconhecimento de celebridades por conteúdos de vídeo. A funcionalidade de reconhecimento de celebridades abrange cerca de um milhão de rostos com base em fontes de dados comumente solicitadas, tais como IMDB, Wikipedia e influenciadores de topo do LinkedIn. Rostos que não são reconhecidos pela característica de reconhecimento de celebridades são detetados, mas deixados sem nome. Depois de fazer o upload do seu vídeo para o Video Indexer e obter resultados de volta, pode voltar atrás e nomear as caras que não foram reconhecidas. Uma vez rotulado um rosto com um nome, o rosto e o nome são adicionados ao modelo Pessoa da sua conta. O Indexer de vídeo reconhecerá então este rosto nos seus futuros vídeos e vídeos passados.

Pode utilizar a API do Indexer de Vídeo para editar rostos que foram detetados num vídeo, como descrito neste tópico. Também pode utilizar o website do Indexer de Vídeo, conforme descrito no [modelo Personaliz Person utilizando o website do Indexer de Vídeo](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gestão de vários modelos pessoa

O Indexer de vídeo suporta vários modelos pessoa por conta. Esta funcionalidade encontra-se atualmente disponível apenas através das APIs do Indexer de Vídeo.

Se a sua conta atender a diferentes cenários de caso de utilização, é possível que queira criar vários modelos pessoa por conta. Por exemplo, se o seu conteúdo estiver relacionado com desporto, pode então criar um modelo pessoa separado para cada desporto (futebol, basquetebol, futebol, e assim por diante).

Uma vez criado um modelo, pode usá-lo fornecendo o id do modelo de um modelo pessoa específico ao carregar/indexar ou reindexar um vídeo. Treinar um novo rosto para um vídeo atualiza o modelo personalizado específico com o que o vídeo estava associado.

Cada conta tem um limite de 50 modelos pessoais. Se não necessitar do suporte do modelo pessoa múltipla, não designe um ID do modelo Pessoa para o seu vídeo ao carregar/indexar ou reindexar. Neste caso, o Indexer de Vídeo utiliza o modelo pessoa personalizado padrão na sua conta.

## <a name="create-a-new-person-model"></a>Criar um novo modelo Pessoa

Para criar um novo modelo Pessoa na conta especificada, utilize a criação de [uma API modelo de pessoa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)

A resposta fornece o nome e o modelo gerado ID do modelo Pessoa que acabou de criar seguindo o formato do exemplo abaixo.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Em seguida, utilize o valor **de id** para o parâmetro **personModelId** ao [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Eliminar um modelo Pessoa

Para eliminar um modelo pessoa personalizado da conta especificada, utilize a [apague uma API modelo de pessoa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)

Uma vez eliminado o modelo Pessoa com sucesso, o índice dos seus vídeos atuais que estavam a utilizar o modelo eliminado permanecerá inalterado até os reindexar. Após a reindexação, os rostos que foram nomeados no modelo apagado não serão reconhecidos pelo Indexer de Vídeo nos seus vídeos atuais que foram indexados usando esse modelo, mas as faces ainda serão detetadas. Os seus vídeos atuais que foram indexados com o modelo eliminado irão agora utilizar o modelo pessoa padrão da sua conta. Se os rostos do modelo apagado também forem nomeados no modelo padrão da sua conta, essas faces continuarão a ser reconhecidas nos vídeos.

Não há conteúdo devolvido quando o modelo Pessoa é apagado com sucesso.

## <a name="get-all-person-models"></a>Obtenha todos os modelos Pessoa

Para obter todos os modelos Pessoa na conta especificada, use o modelo aAPI de [obter uma pessoa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)

A resposta fornece uma lista de todos os modelos Pessoa na sua conta (incluindo o modelo Pessoa padrão na conta especificada) e cada um dos seus nomes e IDs seguindo o formato do exemplo abaixo.

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

Pode escolher qual modelo pretende utilizar para um `id` vídeo utilizando o `personModelId` valor do modelo Pessoa para o parâmetro ao [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Atualizar um rosto

Este comando permite-lhe atualizar um rosto no seu vídeo com um nome usando a identificação do vídeo e identificação do rosto. Esta ação atualiza então o modelo Pessoa a que o vídeo estava associado ao upload/indexação ou reindexing. Se nenhum modelo Pessoa foi atribuído, atualiza o modelo pessoa padrão da conta.

O sistema reconhece então as ocorrências da mesma face nos seus outros vídeos atuais que partilham o mesmo modelo Pessoa. O reconhecimento do rosto nos seus outros vídeos atuais pode levar algum tempo a fazer efeito, uma vez que se trata de um processo de lote.

Pode atualizar um rosto que o Video Indexer reconheceu como uma celebridade com um novo nome. O novo nome que dás terá precedência sobre o reconhecimento de celebridades incorporadas.

Para atualizar o rosto, utilize a atualização de uma API [face a vídeo.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)

Os nomes são únicos para os modelos Person, por isso, se você dá duas faces diferentes no mesmo modelo Pessoa o mesmo `name` valor de parâmetro, O Indexer de Vídeo vê as caras como a mesma pessoa e converge-as assim que reindexa o seu vídeo.

## <a name="next-steps"></a>Passos seguintes

[Personalize o modelo da pessoa usando o site do Indexer de Vídeo](customize-person-model-with-website.md)
