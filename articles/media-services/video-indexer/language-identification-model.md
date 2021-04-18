---
title: Use o Indexador de Vídeo para identificar automaticamente línguas faladas - Azure
titleSuffix: Azure Media Services
description: Este artigo descreve como o modelo de identificação de linguagem do Indexante de Vídeo é usado para identificar automaticamente a língua falada num vídeo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 40f2e146956919e154f59d90b56a1b03379abbb2
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600642"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identificar automaticamente o idioma falado com o modelo de identificação de idioma

O Indexante de Vídeo suporta a identificação automática da linguagem (LID), que é o processo de identificar automaticamente o conteúdo da língua falada a partir do áudio e enviar o ficheiro de mídia para ser transcrito na língua identificada dominante. 

Atualmente o LID suporta: Inglês, Espanhol, Francês, Alemão, Italiano, Mandarim Chinês, Japonês, Russo e Português (brasileiro). 

Certifique-se de rever a secção [de Diretrizes e Limitações](#guidelines-and-limitations) abaixo.

## <a name="choosing-auto-language-identification-on-indexing"></a>Escolha da identificação de linguagem automática na indexação

Ao indexar ou [re-indexar](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) um vídeo utilizando a API, escolha a `auto detect` opção no `sourceLanguage` parâmetro.

Ao utilizar o portal, aceda aos vídeos da sua **Conta** na página inicial do [Índice de Vídeo](https://www.videoindexer.ai/) e sobre o nome do vídeo que pretende re-indexar. No canto inferior direito clique no botão de re-indexação. No diálogo **de vídeo Re-index,** escolha *deteção automática* a partir da caixa de entrega de linguagem de **origem vídeo.**

![detetar automaticamente](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Saída do modelo

O Indexer de Vídeo transcreve o vídeo de acordo com a linguagem mais provável se a confiança para essa língua for `> 0.6` . Se a língua não pode ser identificada com confiança, assume que a língua falada é inglês. 

A linguagem dominante do modelo está disponível nos insights JSON como `sourceLanguage` o atributo (sob raiz/vídeos/insights). Uma pontuação de confiança correspondente também está disponível no âmbito do `sourceLanguageConfidence` atributo.

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

* A identificação automática da linguagem (LID) suporta as seguintes línguas: 

    Inglês, Espanhol, Francês, Alemão, Italiano, Mandarim Chines, Japonês, Russo e Português (Brasileiro).
* Embora o Indexante de Vídeo suporte o árabe (Modern Standard e Levantine), hindi e coreano, estas línguas não são suportadas no LID.
* Se o áudio contiver idiomas diferentes da lista suportada acima, o resultado é inesperado.
* Se o Indexador de Vídeo não conseguir identificar o idioma com uma confiança suficientemente alta `>0.6` (), a língua de recuo é o inglês.
* Não existe suporte atual para ficheiros com áudio de línguas mistas. Se o áudio contiver línguas mistas, o resultado é inesperado. 
* O áudio de baixa qualidade pode ter impacto nos resultados do modelo.
* O modelo requer pelo menos um minuto de discurso no áudio.
* O modelo foi concebido para reconhecer um discurso de conversação espontâneo (não comandos de voz, canto, etc.).

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral](video-indexer-overview.md)
* [Identificar e transcrever automaticamente conteúdo em vários idiomas](multi-language-identification-transcription.md)
