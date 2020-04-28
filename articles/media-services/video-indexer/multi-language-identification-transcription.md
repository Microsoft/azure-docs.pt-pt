---
title: Identifique e transcreve automaticamente conteúdo multilingues com Indexer de Vídeo
titleSuffix: Azure Media Services
description: Este tópico demonstra como identificar e transcrever automaticamente conteúdos multilingues com o Indexer de Vídeo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72968748"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Identifique e transcreve automaticamente conteúdo multilinguístico (pré-visualização)

O Indexer de vídeo suporta a identificação e transcrição automáticas de idiomas em conteúdo multilingues. Este processo envolve identificar automaticamente a linguagem falada em diferentes segmentos a partir do áudio, enviar cada segmento do ficheiro de mídia para ser transcrito e combinar a transcrição da transcrição de uma transcrição unificada. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Escolher a identificação multilíngue na indexação com o portal

Pode escolher a **deteção de várias línguas** ao carregar e indexar o seu vídeo. Em alternativa, pode escolher a **deteção de várias línguas** ao reindexar o seu vídeo. Os seguintes passos descrevem como reindexar:

1. Aceda ao site do [Video Indexer](https://vi.microsoft.com/) e inicie sessão.
1. Vá à página da **Biblioteca** e repasse o nome do vídeo que pretende reindexar. 
1. No canto inferior direito, clique no botão de **vídeo re-indexado.** 
1. No diálogo de **vídeo re-indexado,** escolha a **deteção multi-idioma** da caixa de drop-down da linguagem de origem de **vídeo.**

    * Quando um vídeo é indexado como multi-idioma, a página de insight incluirá essa opção, e um tipo de insight adicional aparecerá, permitindo ao utilizador visualizar qual segmento é transcrito em que língua "spoken language".
    * A tradução para todas as línguas está totalmente disponível a partir da transcrição multi-idioma.
    * Todos os outros insights aparecerão na linguagem principal detetada – que é a linguagem que mais apareceu no áudio.
    * Legendas fechadas no leitor também estão disponíveis em várias línguas.

![Experiência do portal](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Escolher a identificação multilíngue na indexação com API

Ao indexar ou [reindexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) um vídeo utilizando `multi-language detection` a `sourceLanguage` API, escolha a opção no parâmetro.

### <a name="model-output"></a>Saída do modelo

O modelo vai recuperar todas as línguas detetadas no vídeo numa lista

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Adicionalmente, cada instância na secção de transcrição incluirá a linguagem em que foi transcrita

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

## <a name="guidelines-and-limitations"></a>Orientações e limitações

* Conjunto de línguas apoiadas: inglês, francês, alemão, espanhol.
* Suporte para conteúdos multilingidiomas com até três línguas suportadas.
* Se o áudio contiver outras línguas que não a lista suportada acima, o resultado é inesperado.
* Comprimento mínimo do segmento para detetar para cada idioma – 15 segundos.
* A deteção de linguagem compensada é de 3 segundos, em média.
* Espera-se que a fala seja contínua. Alternâncias frequentes entre línguas podem afetar o desempenho dos modelos.
* A fala de falantes não nativos pode afetar o desempenho do modelo (por exemplo, quando os falantes usam a sua língua nativa e mudam para outra língua).
* O modelo foi concebido para reconhecer um discurso de conversação espontâneo com acústica sonora razoável (não comandos de voz, canto, etc.).
* A criação e edição de projetos não está atualmente disponível para vídeos multilingues.
* Os modelos de idioma personalizados não estão disponíveis quando se utiliza a deteção de várias línguas.
* A adição de palavras-chave não é suportada.
* Ao exportar ficheiros de legendas fechadas, a indicação linguística não aparecerá.
* A transcrição da atualização API não suporta ficheiros de vários idiomas.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)
