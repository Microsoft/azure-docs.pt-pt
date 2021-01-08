---
title: Identificar e transcrever automaticamente conteúdos multi-linguísticos com o Video Indexer
titleSuffix: Azure Media Services
description: Este tópico demonstra como identificar e transcrever automaticamente conteúdos multi-linguísticos com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 657ccafa0e7b7f640122fd6b397b3fa2a7c5f0fc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015560"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Identificar e transcrever automaticamente conteúdo em vários idiomas

O Indexer de Vídeo suporta identificação automática de linguagem e transcrição em conteúdo multi-linguístico. Este processo envolve identificar automaticamente a língua falada em diferentes segmentos a partir do áudio, enviando cada segmento do ficheiro de mídia para ser transcrito e combinando a transcrição de volta a uma transcrição unificada. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Escolher identificação multilíngue na indexação com portal

Pode escolher **a deteção de vários idiomas** ao carregar e indexar o seu vídeo. Em alternativa, pode escolher **a deteção de várias línguas**  ao re-indexar o seu vídeo. Os seguintes passos descrevem como reindexo:

1. Aceda ao site do [Video Indexer](https://vi.microsoft.com/) e inicie sessão.
1. Vá à página da **Biblioteca** e paire sobre o nome do vídeo que pretende reindexo. 
1. No canto inferior direito, clique no botão **de vídeo Re-index.** 
1. No diálogo **de vídeo Re-index,** escolha **a deteção de vários idiomas** a partir da caixa de drop-down de linguagem de **origem vídeo.**

    * Quando um vídeo é indexado como multi-idioma, a página de insight incluirá essa opção, e aparecerá um tipo de insight adicional, permitindo ao utilizador visualizar qual o segmento transcrito em que língua "língua falada".
    * A tradução para todos os idiomas está totalmente disponível a partir da transcrição multi-língua.
    * Todos os outros insights aparecerão na linguagem principal detetada – é essa a linguagem que mais apareceu no áudio.
    * As legendas fechadas no leitor também estão disponíveis em vários idiomas.

![Experiência do portal](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Escolha da identificação multilíngue na indexação com API

Ao indexar ou [reindexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) um vídeo utilizando a API, escolha a `multi-language detection` opção no `sourceLanguage` parâmetro.

### <a name="model-output"></a>Saída do modelo

O modelo irá recuperar todas as línguas detetadas no vídeo numa lista

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Além disso, cada instância na secção de transcrição incluirá a língua em que foi transcrita

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

* Conjunto de línguas apoiadas: inglês, francês, alemão, espanhol.
* Suporte para conteúdos multi-linguísticos com até três línguas apoiadas.
* Se o áudio contiver idiomas diferentes da lista suportada acima, o resultado é inesperado.
* Comprimento mínimo do segmento para detetar para cada idioma – 15 segundos.
* A desempenhamento na deteção de idiomas é de 3 segundos, em média.
* Espera-se que a fala seja contínua. Alternações frequentes entre línguas podem afetar o desempenho dos modelos.
* A fala de falantes não nativos pode afetar o desempenho do modelo (por exemplo, quando os falantes usam a sua língua nativa e mudam para outra língua).
* O modelo foi concebido para reconhecer um discurso de conversação espontâneo com acústica áudio razoável (não comandos de voz, canto, etc.).
* A criação e edição de projetos não está atualmente disponível para vídeos multi-linguísticos.
* Os modelos linguísticos personalizados não estão disponíveis quando se utilizam a deteção de vários idiomas.
* A adição de palavras-chave não é suportada.
* Ao exportar ficheiros de legendas fechados, a indicação linguística não aparecerá.
* A transcrição da API não suporta vários ficheiros de idiomas.

## <a name="next-steps"></a>Próximos passos

[Descrição geral do Video Indexer](video-indexer-overview.md)
