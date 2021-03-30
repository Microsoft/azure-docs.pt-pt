---
title: Personalize um modelo de Pessoa com API indexante de vídeo
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo Pessoa com a API do Indexante de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 09366dea1a0d77052b6f99e9f5ab52c270e341b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047015"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalize um modelo pessoa com a API indexante de vídeo

O Video Indexer suporta a deteção facial e o reconhecimento de celebridades para conteúdos de vídeo. A funcionalidade de reconhecimento de celebridades abrange cerca de um milhão de rostos com base em fontes de dados comumente solicitadas, tais como IMDB, Wikipedia e principais influenciadores do LinkedIn. Rostos que não são reconhecidos pela funcionalidade de reconhecimento de celebridades são detetados, mas deixados sem nome. Depois de fazer o upload do seu vídeo para o Video Indexer e obter os resultados de volta, pode voltar atrás e nomear os rostos que não foram reconhecidos. Uma vez que rotula um rosto com um nome, o rosto e o nome são adicionados ao modelo Pessoa da sua conta. O Video Indexer reconhecerá então esta cara nos seus vídeos e vídeos passados.

Pode utilizar a API do Indexer de Vídeo para editar rostos que foram detetados num vídeo, como descrito neste tópico. Também pode utilizar o website do Índice de Vídeo, conforme descrito no [modelo Personale Person utilizando o website do Índice de Vídeo](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gerir vários modelos pessoais

O Indexer de Vídeo suporta vários modelos pessoais por conta. Esta funcionalidade está atualmente disponível apenas através das APIs do Indexante de Vídeo.

Se a sua conta atender a diferentes cenários de casos de utilização, é possível que queira criar vários modelos Pessoa por conta. Por exemplo, se o seu conteúdo estiver relacionado com o desporto, pode então criar um modelo pessoa separada para cada desporto (futebol, basquetebol, futebol, e assim por diante).

Uma vez criado um modelo, pode usá-lo fornecendo o ID do modelo de um modelo específico da Pessoa ao carregar/indexar ou reindexar um vídeo. Treinar um novo rosto para um vídeo atualiza o modelo personalizado específico a que o vídeo estava associado.

Cada conta tem um limite de 50 modelos pessoais. Se não necessitar do suporte de modelo multiple Person, não atribua um ID do modelo pessoa ao seu vídeo ao carregar/indexar ou reindexar. Neste caso, o Video Indexer utiliza o modelo personalizado padrão pessoa na sua conta.

## <a name="create-a-new-person-model"></a>Criar um novo modelo pessoa

Para criar um novo modelo Pessoa na conta especificada, utilize a [API modelo de criação de uma pessoa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)

A resposta fornece o nome e o ID do modelo de modelo gerado que acaba de criar seguindo o formato do exemplo abaixo.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Em seguida, utilize o valor **de id** para o parâmetro **personModelId** [ao carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Excluir um modelo de Pessoa

Para eliminar um modelo personalizado pessoa da conta especificada, utilize a [API modelo de exclusão de uma pessoa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)

Uma vez eliminado o modelo Person com sucesso, o índice dos seus vídeos atuais que estavam a utilizar o modelo eliminado permanecerá inalterado até reindexá-los. Após o reindexo, os rostos que foram nomeados no modelo eliminado não serão reconhecidos pelo Video Indexer nos seus vídeos atuais que foram indexados usando esse modelo, mas os rostos ainda serão detetados. Os vídeos atuais que foram indexados utilizando o modelo eliminado utilizarão agora o modelo Person padrão da sua conta. Se os rostos do modelo eliminado também forem nomeados no modelo padrão da sua conta, esses rostos continuarão a ser reconhecidos nos vídeos.

Não há conteúdo devolvido quando o modelo Pessoa é eliminado com sucesso.

## <a name="get-all-person-models"></a>Obtenha todos os modelos pessoais

Para obter todos os modelos Pessoa na conta especificada, utilize a API [modelo de pessoa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)

A resposta fornece uma lista de todos os modelos Pessoa na sua conta (incluindo o modelo de Pessoa predefinido na conta especificada) e cada um dos seus nomes e IDs seguindo o formato do exemplo abaixo.

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

Pode escolher qual o modelo que pretende utilizar para um vídeo utilizando o `id` valor do modelo Pessoa para o parâmetro ao carregar um vídeo para `personModelId` [indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Atualizar um rosto

Este comando permite-lhe atualizar um rosto no seu vídeo com um nome utilizando o ID do vídeo e iD do rosto. Esta ação atualiza então o modelo Pessoa a que o vídeo foi associado ao carregar/indexar ou reindexar. Se nenhum modelo de Pessoa foi atribuído, atualiza o modelo person predefinido da conta.

O sistema reconhece então as ocorrências do mesmo rosto nos outros vídeos atuais que partilham o mesmo modelo Pessoa. O reconhecimento do rosto nos seus outros vídeos atuais pode demorar algum tempo a fazer efeito, uma vez que este é um processo de lote.

Pode atualizar um rosto que o Video Indexer reconheceu como uma celebridade com um novo nome. O novo nome que dá terá precedência sobre o reconhecimento de celebridades incorporadas.

Para atualizar o rosto, utilize a atualização de uma API [de rosto de vídeo.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)

Os nomes são únicos para os modelos Person, por isso, se você der duas caras diferentes no mesmo modelo Pessoa o mesmo `name` valor de parâmetro, O Indexer de Vídeo vê os rostos como a mesma pessoa e converge-os assim que reindex o seu vídeo.

## <a name="next-steps"></a>Passos seguintes

[Personalizar o modelo pessoa usando o site do Indexer de Vídeo](customize-person-model-with-website.md)
