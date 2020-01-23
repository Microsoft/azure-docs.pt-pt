---
title: Usar Video Indexer para identificar automaticamente os idiomas falados – Azure
titleSuffix: Azure Media Services
description: Este artigo descreve como o modelo de identificação de idioma Video Indexer é usado para identificar automaticamente o idioma falado em um vídeo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 7a2e03b8dacbf6c3ff20e02c804804b671e86d97
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513886"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identificar automaticamente o idioma falado com o modelo de identificação de idioma

O Video Indexer dá suporte à identificação automática de idioma (tampa), que é o processo de identificar automaticamente o conteúdo do idioma falado do áudio e enviar o arquivo de mídia para ser transcrita na linguagem identificada dominante. Atualmente, a tampa oferece suporte a inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e Português (Brasil). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Escolhendo a identificação automática de idioma na indexação

Ao indexar ou [reindexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) um vídeo usando a API, escolha a opção `auto detect` no parâmetro `sourceLanguage`.

Ao usar o portal, acesse os vídeos da sua **conta** no [Video indexer](https://www.videoindexer.ai/) Home Page e passe o mouse sobre o nome do vídeo que você deseja reindexar. No canto inferior direito, clique no botão reindexar. No diálogo **reindexar vídeo** , escolha *detecção automática* na caixa suspensa **idioma da origem do vídeo** .

![detecção automática](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Saída do modelo

Video Indexer transcreve o vídeo de acordo com a linguagem mais provável se a confiança para esse idioma for `> 0.6`. Se o idioma não puder ser identificado com confiança, ele assumirá que o idioma falado é o inglês. 

O idioma dominante do modelo está disponível no JSON do insights como o atributo `sourceLanguage` (em raiz/vídeos/insights). Uma pontuação de confiança correspondente também está disponível no atributo `sourceLanguageConfidence`.

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

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

* Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português (Brasil).
* Se o áudio contiver idiomas diferentes da lista de suporte acima, o resultado será inesperado.
* Se Video Indexer não puder identificar o idioma com alta confiança suficiente (`>0.6`), o idioma de fallback será o inglês.
* Não há suporte atual para arquivos com áudio de idiomas mistos. Se o áudio contiver idiomas mistos, o resultado será inesperado. 
* O áudio de baixa qualidade pode afetar os resultados do modelo.
* O modelo requer pelo menos um minuto de fala no áudio.
* O modelo foi projetado para reconhecer uma fala de conversa espontaneável (não comandos de voz, assinar, etc.).

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral](video-indexer-overview.md)
* [Identificar e transcrever automaticamente o conteúdo em vários idiomas](multi-language-identification-transcription.md)
