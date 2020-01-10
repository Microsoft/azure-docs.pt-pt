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
ms.date: 01/07/2020
ms.author: juliako
ms.openlocfilehash: 88effd58e807d39e5915aa41425ecf2e8ca8e3cc
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832315"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notas de versão do Video Indexer de serviços de mídia do Azure

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` em seu leitor de RSS feed.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* Versões mais recentes
* Problemas conhecidos
* Correções de erros
* Funcionalidades preteridas

## <a name="december-2019"></a>Dezembro de 2019

### <a name="update-transcript-with-the-new-api"></a>Atualizar transcrição com a nova API

Atualize uma seção específica na transcrição usando a API [Update-Video-index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) .

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Corrigir a configuração da conta no portal de Video Indexer

Agora você pode atualizar a configuração de conexão dos serviços de mídia para autoajudar com problemas como: 

* recurso incorreto dos serviços de mídia do Azure
* alterações de senha
* Os recursos dos serviços de mídia foram movidos entre assinaturas  

Para corrigir a configuração da conta, no portal do Video Indexer, navegue até Configurações > guia conta (como proprietário).

### <a name="configure-the-custom-vision-account"></a>Configurar a conta de visão personalizada

Configurar a conta de visão personalizada em contas pagas usando o portal de Video Indexer (anteriormente, isso só era suportado pela API). Para fazer isso, entre no portal de Video Indexer, escolha personalização de modelo > caracteres animados > Configurar. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Cenas, capturas e quadros-chave – agora em um painel de insights

Os bastidores, as capturas e os quadros-chave agora são mesclados em uma percepção para facilitar o consumo e a navegação. Ao selecionar a cena desejada, você pode ver quais capturas e quadros-chave são consistentes. 

### <a name="notification-about-a-long-video-name"></a>Notificação sobre um nome de vídeo longo

Quando um nome de vídeo tem mais de 80 caracteres, Video Indexer mostra um erro descritivo no carregamento.

### <a name="streaming-endpoint-is-disabled-notification"></a>A notificação de ponto de extremidade de streaming está desabilitada

Quando o ponto de extremidade de streaming estiver desabilitado, Video Indexer mostrará um erro descritivo na página do Player.

### <a name="error-handling-improvement"></a>Aprimoramento do tratamento de erros

O código de status 409 agora será retornado de [reindexação de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video? https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) e atualização de APIs de [índice de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) caso um vídeo esteja ativamente indexado, para evitar a substituição das alterações de reindexação atuais por acidente.

## <a name="november-2019"></a>Novembro de 2019
 
* Suporte aos modelos de idioma personalizado coreano

    O video indexer agora dá suporte a modelos de linguagem personalizados em coreano (`ko-KR`) tanto na API quanto no Portal. 
* Novos idiomas com suporte para conversão de fala em texto (STT)

    Video Indexer APIs agora dão suporte a STT em árabe Levantine (ar-SY), dialeto inglês do Reino Unido (en-GB) e dialeto australiano inglês (en-AU).
    
    Para carregamento de vídeo, substituimos zh-HANS a zh-CN, ambos têm suporte, mas o zh-CN é recomendado e mais preciso.
    
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
    
    O tópico inferência Model Agora dá suporte à granularidade mais profunda da taxonomia IPTC. Leia os detalhes completos nos [serviços de mídia do Azure nova inovação com alimentação de ia](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

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
