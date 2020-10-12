---
title: Análise de vídeo ao vivo nas FAQs IoT Edge - Azure
description: Este tópico dá respostas ao Live Video Analytics nas FAQs IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011520"
---
# <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (PERGUNTAS Frequentes)

Este tópico dá respostas ao Live Video Analytics nas FAQs IoT Edge.

## <a name="general"></a>Geral

Quais são as variáveis do sistema que podem ser usadas na definição de topologia de gráficos?

|Variável   |Descrição|
|---|---|
|[Sistema.DataTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Representa um instante no tempo, tipicamente expresso como data e hora do dia.|
|Nome System.GraphTopology   |Representa uma topologia de gráficos de mídia, detém a planta de um gráfico.|
|Nome system.graphInstance|  Representa uma instância de gráfico de mídia, detém valores de parâmetros e faz referência à topologia.|

## <a name="configuration-and-deployment"></a>Configuração e implantação

Posso implantar o módulo de borda de mídia para um dispositivo Windows 10?
    * Sim. Consulte o artigo sobre [os recipientes Linux no Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Captura a partir de câmara IP e definições de RTSP

* Preciso de utilizar um SDK especial no meu dispositivo para enviar uma transmissão de fluxo de vídeo?
    * N.º Live Video Analytics on IoT Edge suporta a captura de meios usando o protocolo de streaming de vídeo RTSP (que é suportado na maioria das câmaras IP).
* Posso pressionar os meios de comunicação para live video analytics no IoT Edge usando RTMP ou Smooth (como um Evento ao Vivo dos Serviços de Media)?
    * N.º A LVA suporta apenas o RTSP para capturar vídeos a partir de câmaras IP.
    * Qualquer câmara que suporte o streaming RTSP sobre o TCP/HTTP deve funcionar. 
* Posso reiniciar ou atualizar o URL da origem RTSP numa instância de gráfico?
    * Sim, quando a instância do gráfico está em estado inativo.  
* Existe um simulador RTSP disponível para ser utilizado durante o teste e desenvolvimento?
    * Sim. Existe um módulo de borda [de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) disponível para utilização nos arranques rápidos e tutoriais para suportar o processo de aprendizagem. Este módulo é proporcionado como a melhor solução e pode nem sempre estar disponível. É fortemente encorajado a não usar isto por mais de algumas horas. Deve investir em testes com a sua fonte rtsp real antes de fazer planos para uma implementação de produção.
* É suportada a deteção ONVIF de câmaras IP no Edge?
    * Não, não existe suporte para a deteção ONVIF de dispositivos no Edge.

## <a name="streaming-and-playback"></a>Streaming e reprodução

* Os ativos registados na AMS a partir da borda podem ser reproduzidos utilizando tecnologias de streaming de Media Services como o HLS ou o DASH?
    * Sim. Os ativos registados podem ser transmitidos como qualquer outro ativo na Azure Media Services. Para transmitir o conteúdo, tem de ter um Streaming Endpoint criado e no estado de funcionamento. A utilização do processo padrão de criação do Localizador de Streaming irá dar-lhe acesso a um manifesto HLS ou DASH para streaming para qualquer estrutura de jogador capaz. Para obter detalhes sobre a criação de manifestos HLS ou DASH, consulte [a embalagem dinâmica.](../latest/dynamic-packaging-overview.md)
* Posso utilizar a proteção padrão de conteúdos e as funcionalidades de DRM dos Serviços de Mídia num ativo arquivado?
    * Sim. Todas as funcionalidades de proteção de conteúdos de encriptação dinâmica padrão e drm estão disponíveis para utilização nos ativos registados a partir de um gráfico de mídia.
* Que jogadores posso usar para ver conteúdo dos ativos gravados?
   * Todos os jogadores padrão que suportam a versão 3 (HLS) do Apple HTTP Live Streaming (HLS) ou a versão 4 são suportados. Além disso, qualquer jogador que seja capaz de cumprir a reprodução MPEG-DASH também é suportado.
    Os jogadores recomendados para testes incluem:

    * [Media Player do Azure](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Jogador shaka](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [Apple nativo HTTP Live Streaming](https://developer.apple.com/streaming/)
    * Edge, Chrome ou Safari construído em leitor de vídeo HTML5
    * Jogadores comerciais que suportam reprodução HLS ou DASH
* Quais são os limites para o streaming de um ativo gráfico de mídia?
    * O streaming de um ativo vivo ou gravado a partir de um gráfico de mídia utiliza a mesma infraestrutura de alta escala e ponto final de streaming que os Media Services suportam para streaming on-demand e live para Media & Entertainment, OTT e clientes de transmissão. Isto significa que pode, de forma rápida e fácil, permitir que o Azure CDN, Verizon ou Akamai entregue o seu conteúdo a um público tão pequeno como alguns espectadores, ou até milhões, dependendo do seu cenário.

    Os conteúdos podem ser entregues usando o Apple HTTP Live Streaming (HLS) ou o MPEG-DASH.

## <a name="monitoring-and-metrics"></a>Monitorização e métricas

* Posso monitorizar o gráfico de mídia na borda usando a Grade de Eventos?
    * N.º Atualmente, a Grade de Eventos não é suportada.
* Posso usar o Azure Monitor para ver a saúde, as métricas e o desempenho dos meus gráficos mediáticos na nuvem ou no limite?
    * N.º
* Existem ferramentas para facilitar a monitorização do módulo IoT Edge dos Serviços de Comunicação Social?
    * O Código do Estúdio Visual suporta a extensão "Azure IoT Tools" que lhe permite monitorizar facilmente os pontos finais do módulo LVAEdge. Pode utilizar esta ferramenta para começar rapidamente a monitorizar o ponto final incorporado do IoT Hub para "eventos" e ver as mensagens de inferência que são encaminhadas do dispositivo de borda para a nuvem. 

    Além disso, pode utilizar esta extensão para editar o Módulo Twin para o módulo LVAEdge para modificar as definições de gráfico de mídia.

Para obter mais informações, consulte o artigo [de monitorização e registo.](monitoring-logging.md)

## <a name="billing-and-availability"></a>Faturação e disponibilidade

* Como é cobrado o LiveVideo Analytics no IoT Edge?
    * Consulte [a página de preços](https://azure.microsoft.com/pricing/details/media-services/) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Get start - Live Video Analytics on IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
