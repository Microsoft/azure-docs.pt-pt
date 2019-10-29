---
title: Notas de versão do Video Indexer de serviços de mídia do Azure | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os serviços de mídia do Azure Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 0583c0093d240026e3ebcdad7b14494f07986ec2
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968721"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notas de versão do Video Indexer de serviços de mídia do Azure

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

## <a name="october-2019"></a>Outubro de 2019
 
* Pesquisar caracteres animados na Galeria

    Ao indexar caracteres animados, agora você pode pesquisá-los na galé de vídeo da conta. Para obter mais informações, consulte [manuscrito de caracteres animados](animated-characters-recognition.md).

## <a name="september-2019"></a>Setembro de 2019
 
Vários avanços anunciados em IBC 2019:
 
* Reconhecimento de caracteres animados (visualização pública)

    Capacidade de detectar os caracteres do grupo do AD Recognize no conteúdo animado, por meio da integração com visão personalizada. Para obter mais informações, consulte a [detecção de caracteres animados](animated-characters-recognition.md).
* Identificação de vários idiomas (visualização pública)

    Detecte segmentos em vários idiomas na faixa de áudio e crie uma transcrição multilíngue com base neles. Suporte inicial: Inglês, espanhol, alemão e francês. Para obter mais informações, consulte [identificar e transcrever automaticamente o conteúdo em vários idiomas](multi-language-identification-transcription.md).
* Extração de entidade nomeada para pessoas e local

    Extrai marcas, locais e pessoas de fala e texto visual por meio do NLP (processamento de idioma natural).
* Classificação do tipo editorial shot

    Marcação de capturas com tipos editoriais como close up, Medium, two shot, interno, externamente, etc. Para obter mais informações, consulte [detecção do tipo editorial shot](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Aprimoramento do tópico inferência – agora cobrindo o nível 2
    
    O tópico inferência Model agora oferece suporte à granularidade mais profunda da taxonomia IPTC. Leia os detalhes completos nos [serviços de mídia do Azure nova inovação com alimentação de ia](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Agosto de 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer implantado no Sul do Reino Unido

Agora você pode criar uma conta paga Video Indexer na região do Sul do Reino Unido.

### <a name="new-editorial-shot-type-insights-available"></a>Novas informações de tipo de captura editorial disponíveis

As novas marcas adicionadas às capturas de vídeo fornecem "tipos de captura" editoriais para identificá-las com as frases editoriais comuns usadas no fluxo de trabalho de criação de conteúdo, como: Extreme closeup, closeup, Wide, Medium, two shot, externamente, interno, face esquerda e direita (disponível no JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extração de entidades de novas pessoas e locais disponíveis

Video Indexer identifica locais nomeados e pessoas por meio do NLP (processamento de idioma natural) do OCR e da transcrição do vídeo. Video Indexer usa o algoritmo de aprendizado de máquina para reconhecer quando locais específicos (por exemplo, a torre de Eiffel) ou pessoas (por exemplo, João da Silva) estão sendo chamados em um vídeo.

### <a name="keyframes-extraction-in-native-resolution"></a>Extração de quadros-chave na resolução nativa

Os quadros-chave extraídos por Video Indexer estão disponíveis na resolução original do vídeo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA para treinamento de modelos de face personalizados de imagens

Rostos de treinamento de imagens movidas do modo de visualização para GA (disponível via API e no Portal).

> [!NOTE]
> Não há impacto de preço relacionado à transição de "visualização para GA".

### <a name="hide-gallery-toggle-option"></a>Ocultar opção de alternância da Galeria

O usuário pode optar por ocultar a guia Galeria do portal (semelhante a ocultar a guia amostras).
 
### <a name="maximum-url-size-increased"></a>Tamanho máximo de URL aumentado

Suporte para cadeia de caracteres de consulta de URL 4096 (em vez de 2048) na indexação de um vídeo.
 
### <a name="support-for-multi-lingual-projects"></a>Suporte para projetos multilíngues

Agora, os projetos podem ser criados com base em vídeos indexados em diferentes idiomas (somente API).

## <a name="july-2019"></a>Julho de 2019

### <a name="editor-as-a-widget"></a>Editor como um widget

O Video Indexer AI-Editor agora está disponível como um widget a ser inserido nos aplicativos do cliente.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Atualizar o modelo de idioma personalizado do arquivo de legenda oculta no portal

Os clientes podem fornecer os formatos de arquivo VTT, SRT e TTML como entrada para modelos de linguagem na página personalização do Portal.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer implantado no leste do Japão

Agora você pode criar uma conta paga Video Indexer na região leste do Japão.

### <a name="create-and-repair-account-api-preview"></a>Criar e reparar a API da conta (versão prévia)

Adicionada uma nova API que permite que você [atualize a chave ou o ponto de extremidade de conexão do serviço de mídia do Azure](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Melhorar o tratamento de erros ao carregar 

Uma mensagem descritiva é retornada em caso de configuração incorreta da conta subjacente dos serviços de mídia do Azure.

### <a name="player-timeline-keyframes-preview"></a>Linha do tempo do Player-visualização de quadros principais 

Agora você pode ver uma visualização de imagem para cada vez na linha do tempo do jogador.

### <a name="editor-semi-select"></a>Seleção semi do editor

Agora você pode ver uma visualização de todas as informações que são selecionadas como resultado da escolha de um período de insight específico no editor.

## <a name="may-2019"></a>Maio de 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Atualizar o modelo de idioma personalizado do arquivo de legenda oculta

[Criar um modelo de linguagem personalizado](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e atualizar as APIs de [modelos de linguagem personalizados](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) agora dão suporte aos formatos de arquivo VTT, Srt e TTML como entrada para modelos de linguagem.

Ao chamar a [API Update transcrição de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), a transcrição é adicionada automaticamente. O modelo de treinamento associado ao vídeo também é atualizado automaticamente. Para obter informações sobre como personalizar e treinar seus modelos de idioma, consulte [Personalizar um modelo de linguagem com Video indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Novos formatos de transcrição de download – TXT e CSV

Além do formato de legenda oculta já suportado (SRT, VTT e TTML), Video Indexer agora dá suporte ao download da transcrição nos formatos TXT e CSV.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral](video-indexer-overview.md)
