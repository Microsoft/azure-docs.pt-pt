---
title: Use o Indexer de Vídeo para identificar automaticamente línguas faladas - Azure
titleSuffix: Azure Media Services
description: Este artigo descreve como o modelo de identificação de linguagem do Indexer de Vídeo é usado para identificar automaticamente a língua falada num vídeo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 7a2e03b8dacbf6c3ff20e02c804804b671e86d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513886"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identifique automaticamente a língua falada com o modelo de identificação linguística

O Indexer de vídeo suporta a identificação automática de idiomas (LID), que é o processo de identificação automática do conteúdo da linguagem falada a partir do áudio e envio do ficheiro de mídia para ser transcrito na língua identificada dominante. Atualmente a LID apoia inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português (brasileiro). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Escolher a identificação de linguagem automática na indexação

Ao indexar ou [reindexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) um vídeo utilizando `auto detect` a API, escolha a opção no `sourceLanguage` parâmetro.

Ao utilizar o portal, vá aos vídeos da sua **Conta** na página inicial do [Indexer](https://www.videoindexer.ai/) de Vídeo e paire sobre o nome do vídeo que pretende reindexar. No canto inferior direito clique no botão de re-indexar. No diálogo de **vídeo re-indexado,** escolha *detetar automaticamente* a partir da caixa de drop-down da linguagem de origem de **vídeo.**

![deteção automática](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Saída do modelo

O Indexer de vídeo transcreve o vídeo de acordo `> 0.6`com a linguagem mais provável se a confiança para essa língua for . Se a língua não pode ser identificada com confiança, assume que a língua falada é inglês. 

A linguagem dominante do modelo está disponível nos insights JSON como atributo `sourceLanguage` (em raiz/vídeos/insights). Uma pontuação de confiança `sourceLanguageConfidence` correspondente também está disponível sob o atributo.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Orientações e limitações

* As línguas apoiadas incluem inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português brasileiro.
* Se o áudio contiver outras línguas que não a lista suportada acima, o resultado é inesperado.
* Se o Indexer de Vídeo não conseguir`>0.6`identificar a língua com uma confiança suficientemente alta , a língua de recuo é o inglês.
* Não existe suporte atual para ficheiros com áudio de línguas mistas. Se o áudio contiver línguas mistas, o resultado é inesperado. 
* O áudio de baixa qualidade pode ter impacto nos resultados do modelo.
* O modelo requer pelo menos um minuto de discurso no áudio.
* O modelo foi concebido para reconhecer um discurso de conversação espontâneo (não comandos de voz, cantar, etc.).

## <a name="next-steps"></a>Passos seguintes

* [Descrição Geral](video-indexer-overview.md)
* [Identifique e transcreve automaticamente conteúdo multilingues](multi-language-identification-transcription.md)
