---
title: Personalizar um modelo Person no Video Indexer-Azure
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo Person no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838301"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizar um modelo Person no Video Indexer

Video Indexer dá suporte ao reconhecimento de celebridade em seus vídeos. O recurso de reconhecimento de celebridade abrange aproximadamente 1 milhão rostos com base na fonte de dados solicitada comumente, como o IMDB, a Wikipédia e os principais influenciadores do LinkedIn. Faces que não são reconhecidas pelo Video Indexer ainda são detectadas, mas não têm nome. Os clientes podem criar modelos de pessoa personalizados e permitir que Video Indexer reconheçam faces que não são reconhecidas por padrão. Os clientes podem criar esses modelos de pessoa emparelhando o nome de uma pessoa com arquivos de imagem da face da pessoa.  

Se sua conta atender a casos de uso diferentes, você poderá se beneficiar com a possibilidade de criar vários modelos de pessoa por conta. Por exemplo, se o conteúdo em sua conta for destinado a ser classificado em canais diferentes, talvez você queira criar um modelo de pessoa separado para cada canal. 

> [!NOTE]
> Cada modelo de pessoa dá suporte a até 1 milhão pessoas e cada conta tem um limite de 50 modelos de pessoa. 

Depois que um modelo é criado, você pode usá-lo fornecendo a ID do modelo de um modelo Person específico ao carregar/indexar ou reindexar um vídeo. Treinar uma nova face para um vídeo, atualiza o modelo personalizado específico ao qual o vídeo foi associado. 

Se você não precisar do suporte ao modelo de várias pessoas, não atribua uma ID de modelo de pessoa ao seu vídeo ao carregar/indexar ou reindexar. Nesse caso, Video Indexer usará o modelo Person padrão em sua conta. 

Você pode usar o site Video Indexer para editar faces que foram detectadas em um vídeo e para gerenciar vários modelos de pessoa personalizada em sua conta, conforme descrito no tópico [Personalizar um modelo Person usando um site](customize-person-model-with-website.md) . Você também pode usar a API, conforme descrito em [Personalizar um modelo Person usando APIs](customize-person-model-with-api.md).
