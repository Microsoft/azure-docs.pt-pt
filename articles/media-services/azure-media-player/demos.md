---
title: Demonstrações do Leitor de Multimédia do Azure
description: Esta página contém uma lista de links para demos do Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/05/2021
ms.openlocfilehash: b67edd55906385f1eddeb576b5f4c97b18e10153
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448496"
---
# <a name="azure-media-player-demos"></a>Demonstrações do Leitor de Multimédia do Azure

Segue-se uma lista de links para demos do Azure Media Player. Você pode baixar todas as amostras do [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples) do GitHub.

## <a name="demo-listing"></a>Listagem de demonstração

| Nome da amostra | Programático via JavaScript | Estático via elemento de vídeo HTML5 | Descrição |
| ------------|----------------------------|-------------------------------------|--------------|
| Básico |
| Definir Origem | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |Reproduzir conteúdo desprotegido.|
| Funcionalidades |
| Inserção de anúncios VOD - VAST | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | N/D | Insira anúncios VAST pré-médios e pós-roll num ativo VOD. |
| Velocidade de reprodução | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| N/D | Permite que os espectadores controlem a velocidade em que estão a ver o seu vídeo. |
| AMP Flush Skin | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | Permite uma nova pele AMP. **Nota:** O amp flush só é suportado nas versões AMP 2.1.0+ |
| Legendas e legendas | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | Reprodução com legendas WebVTT.
| Legendas ao vivo CEA 708 | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | N/D | Reproduzir com legendas de entrada AO VIVO CEA 708 com as legendas alinhadas à esquerda. |
| Streaming com Fallback Progressivo | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | Configuração básica de reprodução adaptativa com recuo para progressivo se o streaming não for suportado na plataforma. |
| Vídeo Progressivo MP4 | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | Reprodução de áudio progressivo MP4. |
| Mp3 áudio progressivo | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | Reprodução de áudio progressivo MP3. |
| DD+ | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | N/D | Reprodução de conteúdo com áudio DD+. |
| Opções |
| Perfil heurístico | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | Mudar o perfil da heurística |
| Localização | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
Definição de localização |
| Menu de faixas de áudio | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
Opções para mostrar como exibir o menu de faixas de áudio na pele padrão. |
| Hotkeys | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | Esta amostra mostra como configurar quais as chave quentes que estão ativadas no leitor |
| Eventos, Registo e Diagnósticos |
| Registar Eventos | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | N/D | Reprodução com os ouvintes do evento. |
| Registo | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | Ligar o registo verboso para a consola. |
| Diagnóstico | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | N/D | Obtenção de dados de diagnóstico. Esta amostra só funciona em alguns técnicos. |
| AES |
| AES sem símbolo | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | Reprodução de conteúdo AES sem ficha. |
| Ficha AES | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | Reprodução de conteúdo AES com ficha. |
| Simulação de procuração de AES HLS | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | Reprodução de conteúdo AES com token, mostrando um proxy para HLS para que o token possa ser usado com dispositivos iOS. |
| Flash de força simbólica AES | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | Reprodução de conteúdo AES com token, forçando a tecnologia flashSS. |
| DRM |
| Multi-DRM com PlayReady, Widevine e FairPlay | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | Reprodução de conteúdo drm sem símbolo, com cabeçalhos PlayReady, Widevine e FairPlay. |
| PlayReady sem token | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | Reprodução de conteúdo PlayReady sem ficha. |
| PlayReady sem símbolo forçar Silverlight | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | Reprodução de conteúdo PlayReady sem token, forçando a tecnologia SilverlightSS. |
| Token PlayReady | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | Reprodução de conteúdo PlayReady com token. |
| Token PlayReady força Silverlight | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | Reprodução de conteúdo PlayReady com token, forçando a tecnologia silverlightSS. |
| Protocolo e Tecnologia |
| Alterar techOrder | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | Alterar a ordem tecnológica |
| Força MPEG-DASH apenas em UrlRewriter | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | Reprodução de conteúdo desprotegido apenas utilizando o protocolo DASH. |
| Excluir MPEG-DASH em UrlRewriter | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | Reprodução de conteúdo desprotegido apenas utilizando os protocolos Smooth e HLS. |
| Política de entrega múltipla | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [Estático](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | Definir a fonte com conteúdo que tem múltiplas políticas de entrega da Azure Media Services |
| Selecione programaticamente |
| Selecione faixa de texto | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | N/D | Selecionando uma faixa WebVTT da lista de faixas. |
| Selecione bitrate | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | N/D | Selecionando um bitrate da lista de bitrates. Esta amostra só funciona em alguns técnicos. |
| Selecione o fluxo de áudio | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | N/D | Selecionando um Audio Stream a partir da lista de streams de áudio disponíveis. Esta amostra só funciona em alguns técnicos. |

## <a name="next-steps"></a>Passos seguintes

<!---Some context for the following links goes here--->
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)