---
title: Lista de funcionalidades do Azure Media Player
description: Uma referência de recurso para Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727232"
---
# <a name="feature-list"></a>Lista de recursos #
Aqui está a lista de funcionalidades testadas e funcionalidades não suportadas:

|                                         | TESTADO | PARCIALMENTE TESTADO | NÃO TESTADO | SEM APOIO | NOTAS                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Reproduzir                                |        |                  |          |             |                                                                                                                      |
| Reprodução básica a pedido                | X      |                  |          |             | Suporta streams apenas da Azure Media Services                                                                      |
| Reprodução básica ao vivo                     | X      |                  |          |             | Suporta streams apenas da Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Suporta o serviço de entrega de chaves de serviços de mídia Azure                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Suporta o serviço de entrega de chaves de serviços de mídia Azure                                                                   |
| Widevine                                |        | X                |          |             | Suporta caixas PSSH widevine delineadas em manifesto                                                                    |
| FairPlay                                |        | X                |          |             | Suporta o serviço de entrega de chaves de serviços de mídia Azure                                                                   |
| Tecnologias                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash Fallback (FlashSS)                | X      |                  |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia.                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia.                                                                         |
| Passagem do HLS nativo (Html5)         |        | X                |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia devido às restrições da plataforma.                                            |
| Funcionalidades                                |        |                  |          |             |                                                                                                                      |
| Suporte Da API                             | X      |                  |          |             | Ver lista de problemas conhecidos                                                                                                |
| UI básico                                | X      |                  |          |                                                                                                                                    |
| Inicialização através do JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inicialização através de etiqueta de vídeo        |        | X                |          |             |                                                                                                                      |
| Endereço de segmento - Baseado no Tempo         | X      |                  |          |             |                                                                                                                      |
| Endereço de segmento - Baseado em Índice        |        |                  |          | X           |                                                                                                                      |
| Endereço de segmento - Byte Based         |        |                  |          | X           |                                                                                                                      |
| Reescritor de URL da Azure Media Services       |        | X                |          |             |                                                                                                                      |
| Acessibilidade - Legendas e legendas  |        | X                |          |             |  WebVTT suportado a pedido, CEA 708 ao vivo parcialmente testado                                                       |
| Acessibilidade - Hotkeys                 | X      |                  |          |             |                                                                                                                      |
| Acessibilidade - Alto Contraste           |        | X                |          |             |                                                                                                                      |
| Acessibilidade - Tab Focus               |        | X                |          |             |                                                                                                                      |
| Mensagens de erro                         |        | X                |          |             | As mensagens de erro são inconsistentes entre os técnicos                                                                         |
| Desencadeamento de eventos                        | X      |                  |          |             |                                                                                                                      |
| Diagnóstico                             |        | X                |          |             | A informação de diagnóstico só está disponível na tecnologia AzureHtml5JS e parcialmente disponível na tecnologia SilverlightSS. |
| Ordem Técnica Personalizável                 |        | X                |          |             |                                                                                                                      |
| Heurística - Básico                      | X      |                  |          |             |                                                                                                                      |
| Heurística - Personalização              |        |                  | X        |             | A personalização só está disponível com a tecnologia AzureHtml5JS.                                                          |
| Descontinuidades                         | X      |                  |          |             |                                                                                                                      |
| Selecione Bitrate                          | X      |                  |          |             | Este API só está disponível nas tecnologias AzureHtml5JS e FlashSS.                                                    |
| Fluxo Multi-Áudio                      |        | X                |          |             | O interruptor de áudio programático é suportado nas tecnologias AzureHtml5JS e FlashSS, e está disponível através da seleção ui em AzureHtml5JS, FlashSS e html5 nativo (no Safari).  A maioria das plataformas requer os mesmos dados privados codec para mudar os fluxos de áudio (mesmo codec, canal, taxa de amostragem, etc.). |
| Localização da UI                         |        | X                |          |             |                                                                                                                      |
| Reprodução em várias instâncias                 |        |                  |          | X           | Este cenário pode funcionar para alguns técnicos, mas atualmente não é apoiado e não testado. Você também pode fazer com que isto funcione usando iframes |
| Apoio a Anúncios                             |        | x                |          |             | A AMP suporta a inserção de anúncios lineares pré-médio e pós-roll de servidores de anúncios compatíveis com VAST para VOD na tecnologia AzureHtml5JS |
| Análise                               |        | X                |          |             | A AMP fornece a capacidade de ouvir eventos de análise e diagnóstico de forma a enviar para um Backend Analytics da sua escolha.  Todos os eventos e propriedades não estão disponíveis em todos os técnicos devido a limitações da plataforma.                                                                            |
| Peles Personalizadas                            |        |                  | X        |             | Rode os controlos de regulação para falsos na AMP e utilizando o seu próprio HTML e CSS.           |
| Procurar limpeza de bar                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Apenas áudio                              |        |                  |          | X           | Pode trabalhar em alguns técnicos para o Streaming Adaptativo, mas atualmente não é suportado e não funciona no AzureHtml5JS. A reprodução progressiva de MP3 pode funcionar com a tecnologia HTML5 se a plataforma a suportar.                                                                                                        |
| Apenas vídeo                              |        |                  |          | X           | Pode trabalhar em alguns técnicos para o Streaming Adaptativo, mas atualmente não é suportado e não funciona no AzureHtml5JS.      |
| Apresentação de vários períodos               |        |                  |          | X                                                                                                                                  |
| Múltiplos ângulos de câmara                  |        |                  |          | X           |                                                                                                                      |
| Velocidade de reprodução                          |        | X                |          |             | A velocidade de reprodução é suportada na maioria dos cenários, exceto o caso móvel devido a um bug parcial no Chrome                 |

## <a name="next-steps"></a>Passos seguintes ##
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)