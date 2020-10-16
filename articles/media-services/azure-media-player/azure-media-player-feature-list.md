---
title: Lista de recursos do Azure Media Player
description: Uma referência de recurso para o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 88048c3328114f17b30859efb41bb9f059b71439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296370"
---
# <a name="feature-list"></a>Lista de funcionalidades #
Aqui está a lista de funcionalidades testadas e funcionalidades não apoiadas:

| Funcionalidade | TESTADO | PARCIALMENTE TESTADO | NÃO TESTADO | SEM APOIO | NOTAS |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **Reproduzir**                                |        |                  |          |             |                                                                                                                      |
| Reprodução básica a pedido                | X      |                  |          |             | Suporta streams apenas da Azure Media Services                                                                      |
| Reprodução ao vivo básica                     | X      |                  |          |             | Suporta streams apenas da Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Suporta o Serviço de Entrega de Chaves de Serviços de Media Azure                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Suporta o Serviço de Entrega de Chaves de Serviços de Media Azure                                                                   |
| Widevine                                |        | X                |          |             | Suporta caixas pssh largas delineadas em manifesto                                                                    |
| FairPlay                                |        | X                |          |             | Suporta o Serviço de Entrega de Chaves de Serviços de Media Azure                                                                   |
| **Técnicos**                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Recuo flash (flashss)                | X      |                  |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia.                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia.                                                                         |
| Passagem do HLS nativo (Html5)         |        | X                |          |             | Nem todas as funcionalidades estão disponíveis nesta tecnologia devido às restrições da plataforma.                                            |
| **Funcionalidades**                                |        |                  |          |             |                                                                                                                      |
| Suporte da API                             | X      |                  |          |             | Ver lista de questões conhecidas                                                                                                |
| UI básico                                | X      |                  |          |                                                                                                                                    |
| Inicialização através do JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inicialização através de videovigil,        |        | X                |          |             |                                                                                                                      |
| Segmentação - Baseado no Tempo         | X      |                  |          |             |                                                                                                                      |
| Segmentação - Index Based        |        |                  |          | X           |                                                                                                                      |
| Segmentação - Byte Based         |        |                  |          | X           |                                                                                                                      |
| Reescrita de URL da Azure Media Services       |        | X                |          |             |                                                                                                                      |
| Acessibilidade - Legendas e Legendas  | X      |                 |          |             |  WebVTT (a pedido), CEA 708 (a pedido e ao vivo) e IMSC1 (a pedido e ao vivo)                                                       |
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
| Suporte a anúncios                             |        | X                |          |             | A AMP suporta a inserção de anúncios lineares pré-médios e pós-rolos de servidores de anúncios compatíveis com VAST para VOD na tecnologia AzureHtml5JS |
| Análise                               |        | X                |          |             | A AMP fornece a capacidade de ouvir eventos de análise e diagnóstico para enviar para um Backend Analytics à sua escolha.  Todos os eventos e propriedades não estão disponíveis em tecnologias devido a limitações da plataforma.                                                                            |
| Peles personalizadas                            |        |                  | X        |             | Este cenário pode ser alcançado rodando os controlos de definição para falsos em AMP e usando o seu próprio HTML e CSS.           |
| Procurar bar scrubbing                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Apenas áudio                              | X      |                  |          |           | Suportado em AzureHtml5JS. A reprodução progressiva de MP3 pode funcionar com a tecnologia HTML5 se a plataforma a suportar.                                                                                                        |
| Apenas vídeo                              | X      |                  |          |           | Suportado em AzureHtml5JS.                                                                                                        |
| Apresentação pluástico               |        |                  |          | X                                                                                                                                  |
| Vários ângulos de câmara                  |        |                  |          | X           |                                                                                                                      |
| Velocidade de reprodução                          |        | X                |          |             | A velocidade de reprodução é suportada na maioria dos cenários, exceto no caso móvel devido a um bug parcial no Chrome                 |

## <a name="next-steps"></a>Passos seguintes ##
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)