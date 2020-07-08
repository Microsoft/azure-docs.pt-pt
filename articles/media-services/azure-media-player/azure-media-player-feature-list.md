---
title: Lista de recursos do Azure Media Player
description: Uma referência de recurso para o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727232"
---
# <a name="feature-list"></a>Lista de funcionalidades #
Aqui está a lista de funcionalidades testadas e funcionalidades não apoiadas:

|                                         | TESTADO | PARCIALMENTE TESTADO | NÃO TESTADO | SEM APOIO | NOTAS                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Reproduzir                                |        |                  |          |             |                                                                                                                      |
| Reprodução básica a pedido                | X      |                  |          |             | Suporta streams apenas da Azure Media Services                                                                      |
| Reprodução ao vivo básica                     | X      |                  |          |             | Suporta streams apenas da Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Suporta o Serviço de Entrega de Chaves de Serviços de Media Azure                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Suporta o Serviço de Entrega de Chaves de Serviços de Media Azure                                                                   |
| Widevine                                |        | X                |          |             | Suporta caixas pssh largas delineadas em manifesto                                                                    |
| FairPlay                                |        | X                |          |             | Suporta o Serviço de Entrega de Chaves de Serviços de Media Azure                                                                   |
| Técnicos                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Recuo flash (flashss)                | X      |                  |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia.                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia.                                                                         |
| Passagem do HLS nativo (Html5)         |        | X                |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia devido às restrições da plataforma.                                            |
| Funcionalidades                                |        |                  |          |             |                                                                                                                      |
| Suporte da API                             | X      |                  |          |             | Ver lista de questões conhecidas                                                                                                |
| UI básico                                | X      |                  |          |                                                                                                                                    |
| Inicialização através do JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inicialização através de videovigil,        |        | X                |          |             |                                                                                                                      |
| Segmentação - Baseado no Tempo         | X      |                  |          |             |                                                                                                                      |
| Segmentação - Index Based        |        |                  |          | X           |                                                                                                                      |
| Segmentação - Byte Based         |        |                  |          | X           |                                                                                                                      |
| Reescrita de URL da Azure Media Services       |        | X                |          |             |                                                                                                                      |
| Acessibilidade - Legendas e Legendas  |        | X                |          |             |  WebVTT suportado a pedido, CEA 708 ao vivo parcialmente testado                                                       |
| Acessibilidade - Hotkeys                 | X      |                  |          |             |                                                                                                                      |
| Acessibilidade - Alto Contraste           |        | X                |          |             |                                                                                                                      |
| Acessibilidade - Foco do separador               |        | X                |          |             |                                                                                                                      |
| Mensagens de erro                         |        | X                |          |             | As mensagens de erro são inconsistentes entre os técnicos                                                                         |
| Desencadeamento de eventos                        | X      |                  |          |             |                                                                                                                      |
| Diagnóstico                             |        | X                |          |             | A informação de diagnóstico só está disponível na tecnologia AzureHtml5JS e está parcialmente disponível na tecnologia SilverlightSS. |
| Encomenda Tecnológica Personalizável                 |        | X                |          |             |                                                                                                                      |
| Heurística - Básico                      | X      |                  |          |             |                                                                                                                      |
| Heurística - Personalização              |        |                  | X        |             | A personalização só está disponível com a tecnologia AzureHtml5JS.                                                          |
| Descontinuidades                         | X      |                  |          |             |                                                                                                                      |
| Selecione Bitrate                          | X      |                  |          |             | Esta API só está disponível nas tecnologias AzureHtml5JS e FlashSS.                                                    |
| Fluxo multi-áudio                      |        | X                |          |             | O interruptor de áudio programático é suportado nas tecnologias AzureHtml5JS e FlashSS, e está disponível através da seleção de UI em AzureHtml5JS, FlashSS e nativo Html5 (em Safari).  A maioria das plataformas requer os mesmos dados privados codec para mudar os fluxos de áudio (mesmo codec, canal, taxa de amostragem, etc.). |
| Localização UI                         |        | X                |          |             |                                                                                                                      |
| Reprodução de vários casos                 |        |                  |          | X           | Este cenário pode funcionar para alguns técnicos, mas atualmente não é suportado e não testado. Você também pode fazer com que isto funcione usando iframes |
| Suporte a anúncios                             |        | x                |          |             | A AMP suporta a inserção de anúncios lineares pré-médios e pós-rolos de servidores de anúncios compatíveis com VAST para VOD na tecnologia AzureHtml5JS |
| Análise                               |        | X                |          |             | A AMP fornece a capacidade de ouvir eventos de análise e diagnóstico para enviar para um Backend Analytics à sua escolha.  Todos os eventos e propriedades não estão disponíveis em tecnologias devido a limitações da plataforma.                                                                            |
| Peles personalizadas                            |        |                  | X        |             | Rode os controlos de definição para falsos em AMP e utilize o seu próprio HTML e CSS.           |
| Procurar bar scrubbing                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Apenas áudio                              |        |                  |          | X           | Pode funcionar em algumas tecnologias para o Adaptive Streaming, mas atualmente não é suportado e não funciona em AzureHtml5JS. A reprodução progressiva de MP3 pode funcionar com a tecnologia HTML5 se a plataforma a suportar.                                                                                                        |
| Apenas vídeo                              |        |                  |          | X           | Pode funcionar em algumas tecnologias para o Adaptive Streaming, mas atualmente não é suportado e não funciona em AzureHtml5JS.      |
| Apresentação pluástico               |        |                  |          | X                                                                                                                                  |
| Vários ângulos de câmara                  |        |                  |          | X           |                                                                                                                      |
| Velocidade de reprodução                          |        | X                |          |             | A velocidade de reprodução é suportada na maioria dos cenários, exceto no caso móvel devido a um bug parcial no Chrome                 |

## <a name="next-steps"></a>Próximos passos ##
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)