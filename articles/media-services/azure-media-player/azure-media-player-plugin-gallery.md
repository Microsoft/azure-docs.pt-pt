---
title: Galeria Plugin Azure Media Player
description: Este artigo contém uma lista de plugins disponíveis para o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: d958d93731f38b4b553bca94f48c740c1b75cfd5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449855"
---
# <a name="azure-media-player-plugin-gallery"></a>Galeria Plugin Azure Media Player #

## <a name="plugins"></a>Plug-ins ##

| Nome plugin                         | URL de demonstração                    | Código Fonte                | Descrição    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Características adicionais                 | | | |
| **Novo!** AMP360Video                | [Demonstração](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | O plugin permite visualizar 360 vídeos em Amp, quer no seu ambiente de trabalho, quer em dispositivos compatíveis com VR. A documentação completa está disponível [aqui:](https://doc.babylonjs.com/extensions/amp360video) |
|  Ponta sprite                         | [Demonstração](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Plugin Azure Media Player (AMP) para renderização de linha do tempo de um sprite de imagem de miniatura de vídeo que é gerado a partir de Azure Media Services (AMS) Media Encoder Standard (MES). |
| Sobreposição de Diagnósticos                 | [Demonstração](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Este plugin exibe: Todos os parâmetros-chave, estatísticas de vídeo, todos os eventos no ciclo de vida da reprodução de vídeo e informações de proteção drm, tais como ID chave, URLs de aquisição de licença, se protegidos.                                                                                                                                                                      |
| Taxa de fotogramas e calculadora de código de tempo | Não há demonstração disponível | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Este plugin calcula a taxa de fotogramas de vídeo com base nas `tfhd` / `trun` caixas MP4 do primeiro fragmento de vídeo MPEG-DASH, analisa o valor da escala de tempo do manifesto do cliente MPEG-DASH, e também fornece uma forma de gerar o cronograma por um determinado tempo absoluto do jogador (bem como fornece ao jogador tempo absoluto dado o código de tempo) |
| <strike>Velocidade de reprodução</strike>                      | [Demonstração](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Este plugin permite aos espectadores controlar a velocidade do vídeo. *Note que esta funcionalidade está automaticamente disponível na versão AMP v2.0.0+ mas desativada por padrão.* Para aprender como capacitá-lo, confira as nossas amostras [aqui](https://github.com/Azure-Samples/azure-media-player-samples) |
| Dica de tempo hover                      | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Exibe uma dica de tempo sobre a barra de progresso no rato hover para um tempo preciso procurando. *Nota: Este plugin já está integrado na AMP,* mas se estiver interessado em ver como é programado, sinta-se à vontade para dar uma olhada.                                                                                                                 |
| Sobreposição do título                       | [Demonstração](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Sobreposições título de vídeo configurável sobre tela |
| Marcadores de linha do tempo                    | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Este plugin acolhe uma série de vezes e sobrepõe-se a marcadores minúsculos sobre a barra de progresso nessas alturas. |
| Análise                           | | | |
| Application Insights                | [Blog Post](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Plugin que rastreia as métricas do seu jogador e o coloca no Power BI para uma representação gráfica intuitiva da experiência do jogador dos seus espectadores. |
| Google Analytics                    | N/D                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Plugin do Google Analytics para Azure Media Player |
| Diagnóstico                         | | | |
| Saída de Diagnóstico                  | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Este plugin produz uma série de diagnósticos do seu jogador, para vê-lo em ação ir para o link de demonstração e abrir a sua consola JavaScript. |
| Facilidade de Acesso                      | | | |
| Zoom In                             | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Este plugin exibe uma escala de zoom-in drag-able no ecrã dos jogadores para que os espectadores possam ampliar o seu conteúdo |
| Legendas ao vivo                       | [Azure Blog Post](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[SubPly Post](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | N/D | *Consulte o post para mais informações.* Fluxo de trabalho de fim a fim projetado para legendas ao vivo plugin construído para Azure Media Player, clique no link mais à esquerda para ir ao site da SubPly e saber mais sobre a solução |
| Chaves quentes                            | <strike>[Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | O plugin de teclas quentes permite que os espectadores controlem vários aspetos do leitor com combinações genéricas de plugins como F para ecrã completo, M para teclas de silenciamento e seta para o controlo da barra de progresso. *Nota: Este plugin já foi integrado na AMP mas sinta-se livre para usá-lo como um recurso* |
| Social                              | | | |
| Partilhar                               | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Este plugin adiciona um botão de partilha à barra de controlo do jogador para que os seus espectadores possam partilhar o vídeo que estão a ver com os seus amigos através do Facebook, Twitter ou Linkedin. |

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)
