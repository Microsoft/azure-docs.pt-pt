---
title: Notas de versão do indexador de vídeo de serviços de multimédia do Azure | Documentos da Microsoft
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre o indexador de vídeo dos serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454025"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notas de versão do indexador de vídeo de serviços de multimédia do Azure

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* Versões mais recentes
* Problemas conhecidos
* Correções de erros
* Funcionalidades preteridas

## <a name="june-2019"></a>Junho de 2019

### <a name="video-indexer-deployed-to-japan-east"></a>O Video Indexer implementado leste do Japão

Agora, pode criar um indexador de vídeo pago conta na região Leste do Japão.

### <a name="create-and-repair-account-api-preview"></a>Criar e reparar conta API (pré-visualização)

Adicionada uma nova API que permite-lhe [atualizar o ponto final de ligação de serviço de multimédia do Azure ou a chave](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Melhorar o carregamento de tratamento de erros 

Uma mensagem descritiva é devolvida em caso de uma configuração incorreta da conta de Media Services do Azure subjacente.

### <a name="player-timeline-keyframes-preview"></a>Pré-visualização quadros-chave de linha cronológica de Player 

Agora, pode ver uma pré-visualização de imagem para cada hora na linha de tempo do jogador.

### <a name="editor-semi-select"></a>Semisseletivo de editor

Agora, pode ver uma pré-visualização de todas as informações que são selecionados como resultado de escolha de um período de tempo de ideias específicas no editor.

## <a name="may-2019"></a>Maio de 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Atualizar o modelo de idioma personalizado do ficheiro de legendas

[Criar modelo de idioma personalizado](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e [atualizar modelos de idioma personalizado](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) APIs suportam agora VTT, SRT, e formatos de arquivo TTML como entrada para modelos de linguagem.

Ao chamar o [transcrição de vídeo de atualização API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), transcrição é adicionada automaticamente. O modelo de formação associado com o vídeo é atualizado automaticamente também. Para obter informações sobre como personalizar e preparar os seus modelos de linguagem, veja [personalizar um modelo de idioma com o indexador de vídeo](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Novos formatos de transcrição de download – TXT e CSV

Além do legenda codificado formato fechado já suportado (SRT VTT e TTML), o Video Indexer suporta agora baixar transcrição em formatos CSV e TXT.

## <a name="next-steps"></a>Passos Seguintes

[Descrição Geral](video-indexer-overview.md)
