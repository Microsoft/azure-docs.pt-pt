---
title: Análise de vídeo ao vivo nas FAQs IoT Edge - Azure
description: Este tópico dá respostas ao Live Video Analytics nas FAQs IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401581"
---
# <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (PERGUNTAS Frequentes)

Este tópico dá respostas ao Live Video Analytics nas FAQs IoT Edge.

## <a name="general"></a>Geral

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Quais são as variáveis do sistema que podem ser usadas na definição de topologia de gráficos?

|Variável   |Descrição|
|---|---|
|[Sistema.DataTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Representa um instante na hora utc, tipicamente expresso como data e hora do dia (representação básica yMddTHHmmssZ).|
|System.PreciseDateTime|Representa uma instância de data UTC em formato compatível com ficheiros ISO8601 com milissegundos (representação básica yMddTHHmmss.fffZ).|
|Nome System.GraphTopology   |Representa uma topologia de gráficos de mídia, detém a planta de um gráfico.|
|Nome system.graphInstance|  Representa uma instância de gráfico de mídia, detém valores de parâmetros e faz referência à topologia.|

## <a name="configuration-and-deployment"></a>Configuração e implantação

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>Posso implantar o módulo de borda de mídia para um dispositivo Windows 10?

Sim. Consulte o artigo sobre [os recipientes Linux no Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Captura a partir de câmara IP e definições de RTSP

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>Preciso de utilizar um SDK especial no meu dispositivo para enviar uma transmissão de fluxo de vídeo?

Não. Live Video Analytics on IoT Edge suporta a captura de meios usando o protocolo de streaming de vídeo RTSP (que é suportado na maioria das câmaras IP).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>Posso pressionar os meios de comunicação para live video analytics no IoT Edge usando RTMP ou Smooth (como um Evento ao Vivo dos Serviços de Media)?

* Não. O Live Video Analytics só suporta o RTSP para capturar vídeos a partir de câmaras IP.
* Qualquer câmara que suporte o streaming RTSP sobre o TCP/HTTP deve funcionar. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>Posso reiniciar ou atualizar o URL da origem RTSP numa instância de gráfico?

Sim, quando a instância do gráfico está em estado inativo.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>Existe um simulador RTSP disponível para ser utilizado durante o teste e desenvolvimento?

Sim. Existe um módulo de borda [de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) disponível para utilização nos arranques rápidos e tutoriais para suportar o processo de aprendizagem. Este módulo é proporcionado como a melhor solução e pode nem sempre estar disponível. É fortemente encorajado a não usar isto por mais de algumas horas. Deve investir em testes com a sua fonte rtsp real antes de fazer planos para uma implementação de produção.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>É suportada a deteção ONVIF de câmaras IP no Edge?

Não, não existe suporte para a deteção ONVIF de dispositivos no Edge.

## <a name="streaming-and-playback"></a>Streaming e reprodução

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Os ativos registados na AMS a partir da borda podem ser reproduzidos utilizando tecnologias de streaming de Media Services como o HLS ou o DASH?

Sim. Os ativos registados podem ser transmitidos como qualquer outro ativo na Azure Media Services. Para transmitir o conteúdo, tem de ter um Streaming Endpoint criado e no estado de funcionamento. A utilização do processo padrão de criação do Localizador de Streaming irá dar-lhe acesso a um manifesto HLS ou DASH para streaming para qualquer estrutura de jogador capaz. Para obter detalhes sobre a criação de manifestos HLS ou DASH, consulte [a embalagem dinâmica.](../latest/dynamic-packaging-overview.md)

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>Posso utilizar a proteção padrão de conteúdos e as funcionalidades de DRM dos Serviços de Mídia num ativo arquivado?

Sim. Todas as funcionalidades de proteção de conteúdos de encriptação dinâmica padrão e drm estão disponíveis para utilização nos ativos registados a partir de um gráfico de mídia.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Que jogadores posso usar para ver conteúdo dos ativos gravados?

Todos os jogadores padrão que suportam a versão 3 (HLS) do Apple HTTP Live Streaming (HLS) ou a versão 4 são suportados. Além disso, qualquer jogador que seja capaz de cumprir a reprodução MPEG-DASH também é suportado.

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

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Quais são os limites para o streaming de um ativo gráfico de mídia?

O streaming de um ativo vivo ou gravado a partir de um gráfico de mídia utiliza a mesma infraestrutura de alta escala e ponto final de streaming que os Media Services suportam para streaming on-demand e live para Media & Entertainment, OTT e clientes de transmissão. Isto significa que pode, de forma rápida e fácil, permitir que o Azure CDN, Verizon ou Akamai entregue o seu conteúdo a um público tão pequeno como alguns espectadores, ou até milhões, dependendo do seu cenário.

Os conteúdos podem ser entregues usando o Apple HTTP Live Streaming (HLS) ou o MPEG-DASH.

## <a name="design-your-ai-model"></a>Desenhe o seu modelo de IA 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Tenho vários modelos de IA embrulhados num contentor de estivadores. Como devo usá-los com video analytics ao vivo? 

As soluções são diferentes dependendo do protocolo de comunicação utilizado pelo servidor de inferenculação para comunicar com o Live Video Analytics. Abaixo estão algumas formas de fazer isto.

#### <a name="http-protocol"></a>Protocolo HTTP:

* Recipiente único (único lvaExtension):  

   No seu servidor de inferenculação, pode utilizar uma única porta, mas diferentes pontos finais para diferentes modelos de IA. Por exemplo, para uma amostra python pode utilizar `route` s diferentes por modelo como: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   E, em seguida, na sua implementação live video analytics, quando instantaneamente gráficos, deite o URL do servidor de inferência para cada instância como: 

   1º caso: URL do servidor de inferência=`http://lvaExtension:44000/score/face_detection`<br/>
   2º caso: URL do servidor de inferência=`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > Em alternativa, também pode expor os seus modelos de IA em diferentes portas e chamá-los quando os instantanear gráficos.  

* Vários contentores: 

   Cada recipiente é implantado com um nome diferente. Atualmente, no conjunto de documentação live video analytics, mostramos-lhe como implementar uma extensão com o nome: **LvaExtension**. Agora pode desenvolver dois recipientes diferentes. Cada recipiente tem a mesma interface HTTP (ou seja, o mesmo `/score` ponto final). Desdobre estes dois recipientes com nomes diferentes e certifique-se de que ambos estão a ouvir em **diferentes portas.** 

   Por exemplo, um recipiente com o nome `lvaExtension1` está a ouvir a `44000` porta, outro recipiente com o nome `lvaExtension2` está a ouvir a `44001` porta. 

   Na sua topologia live video analytics, você instantaneamente dois gráficos com URLs de inferência diferentes como: 

   Primeira instância: URL do servidor de inferência = `http://lvaExtension1:44001/score`    
   Segunda instância: URL do servidor de inferência = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>Protocolo GRPC: 

Com o módulo Live Video Analytics 1.0, quando se utiliza um protocolo gRPC, a única forma seria se o servidor gRPC expôs diferentes modelos de IA através de diferentes portas. [Neste exemplo,](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)há uma única porta, 44000 que está expondo todos os modelos yolo. Em teoria, o servidor yolo gRPC poderia ser reescrito para expor alguns modelos a 44000, outros a 45000, ... 

Com o módulo Live Video Analytics 2.0, uma nova propriedade é adicionada ao nó de extensão gRPC. Esta propriedade é chamada **extensãoConfiguration** que é uma cadeia opcional que pode ser usada como parte do contrato gRPC. Quando tiver vários modelos de IA embalados num único servidor de inferência, não precisará de expor um nó para cada modelo de IA. Em vez disso, para uma instância de gráfico, o fornecedor de extensão (você) pode definir como selecionar os diferentes modelos de IA usando a propriedade **de extensãoConfiguration** e durante a execução, Live Video Analytics passará esta cadeia para o servidor de inferencing que pode usá-lo para invocar o modelo de IA desejado. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>Estou a construir um servidor gRPC em torno de um modelo de IA, e quero ser capaz de suportar ser usado por várias câmaras/instâncias de gráfico. Como devo construir o meu servidor? 

 Em primeiro lugar, certifique-se de que o seu servidor consegue lidar com mais do que um pedido de cada vez. Ou certifique-se de que o seu servidor funciona em fios paralelos. 

Por exemplo, numa das [amostras GRPC](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)do Live Video Analytics, existe um número padrão de canais paralelos definidos. Veja: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Na instantânea do servidor gRPC acima, o servidor pode abrir apenas três canais por câmara (assim por instância de topologia de gráfico) de cada vez. Não deve tentar ligar mais de três instâncias ao servidor. Se tentar abrir mais de três canais, os pedidos estarão pendentes até que uma existente caia.  

Acima da implementação do servidor gRPC é usada nas nossas amostras Python. Os desenvolvedores podem implementar os seus próprios servidores ou na implementação acima padrão pode aumentar o número de trabalhador definido para o número de câmaras usadas para obter feed de vídeo. 

Para configurar e utilizar várias câmaras, os desenvolvedores podem instantaneamente várias situações de topologia de gráficos onde cada instância aponta para o mesmo ou diferente servidor de inferência (por exemplo, servidor mencionado no parágrafo acima). 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Quero poder receber vários quadros a montante antes de tomar uma decisão infernante. Como posso permitir isso? 

As [amostras predefinidos](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) atuais funcionam no modo "apátrida". Estas amostras não mantêm o estado das chamadas anteriores e mesmo quem ligou (o que significa que várias instâncias de topologia podem chamar o mesmo servidor de inferência, e o servidor não será capaz de distinguir quem está a ligar e o estado por chamada) 

#### <a name="http-protocol"></a>Protocolo HTTP

Ao utilizar o protocolo HTTP: 

Para manter o estado, cada chamador (instância de topologia de gráfico) chamará o servidor de inferencing com o parâmetro http consulta exclusivo do chamador. Por exemplo, endereço URL do servidor de inferência para  

1ª instância topologia= `http://lvaExtension:44000/score?id=1`<br/>
2ª instância de topologia= `http://lvaExtension:44000/score?id=2`

… 

No lado do servidor, a rota de pontuação saberá quem está a ligar. Se iD=1, então pode manter o estado separadamente para esse chamador (instância topologia de gráfico). Em seguida, pode manter os quadros de vídeo recebidos num tampão (por exemplo, matriz ou dicionário com Tecla DateTime, e valor é o quadro) e, em seguida, pode definir o servidor para processar (inferir) depois de x frames serem recebidos. 

#### <a name="grpc-protocol"></a>Protocolo GRPC 

Ao utilizar o protocolo gRPC: 

Com uma extensão gRPC, cada sessão destina-se a um único feed de câmara, pelo que não há necessidade de fornecer um ID. Assim, agora com a extensãoConfiguration propriedade, você pode armazenar os quadros de vídeo em um tampão e definir o servidor para processar (inferir) depois de x frames serem recebidos. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Todos os ProcessMediaStreams de um determinado recipiente funcionam no mesmo modelo de IA? 

Não.  

As chamadas de início/paragem do utilizador final numa instância de gráfico constituem uma sessão, ou talvez haja uma câmara desligada/reconectada. O objetivo é persistir uma sessão se a câmara estiver a transmitir vídeo. 

* Duas câmaras que enviam vídeo para processamento criam duas sessões. 
* Uma câmara que vai para um gráfico que tem dois nós gRPCExtension cria duas sessões. 

Cada sessão é uma ligação duplex completa entre o Live Video Analytics e o servidor gRPC e cada sessão pode ter um modelo/pipeline diferente. 

> [!NOTE]
> Em caso de desconexão/reconexão da câmara (com a câmara a ficar offline por um período além dos limites de tolerância), o Live Video Analytics abrirá uma nova sessão com o servidor gRPC. Não existe qualquer requisito para que o servidor rastree o estado através destas sessões. 

Live Video Analytics também acrescentou suporte de múltiplas extensões gRPC para uma única câmara em uma instância de gráfico. Poderá utilizar estas extensões gRPC para efetuar o processamento de IA sequencialmente ou em paralelo ou até mesmo ter uma combinação de ambas. 

> [!NOTE]
> Ter múltiplas extensões executadas em paralelo irá afetar os seus recursos de hardware e terá de manter esta mente ao escolher o hardware que irá atender às suas necessidades computacionais. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Qual é o máximo de ProcessMediaStreams simultâneos? 

Não há limite que o Live Video Analytics se aplique.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Como devo decidir se o meu servidor de inferenculação deve usar CPU ou GPU ou qualquer outro acelerador de hardware? 

Isto depende completamente da complexidade do modelo de IA e da forma como o desenvolvedor quer utilizar o CPU e os aceleradores de hardware. Ao desenvolver o modelo de IA, os desenvolvedores podem especificar quais os recursos que devem ser usados pelo modelo para executar que ações. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>Como guardo imagens com caixas de delimitação pós-processamento? 

Hoje, estamos a fornecer coordenadas de caixa limitadas apenas como mensagens de inferência. Os desenvolvedores podem construir um streamer MJPEG personalizado que pode usar estas mensagens e sobrepor as caixas de delimitação sobre os quadros de vídeo.  

## <a name="grpc-compatibility"></a>compatibilidade gRPC 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Como saberei quais são os campos obrigatórios para o descritor de fluxos de mídia? 

Qualquer valor de campo que não seja fornecido será dado um predefinido [conforme especificado por gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Live Video Analytics usa a versão **proto3** da linguagem tampão do protocolo. Todos os dados do tampão de protocolo utilizados pelos contratos live video analytics estão disponíveis nos ficheiros tampão do protocolo [definidos aqui.](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Como devo assegurar-me de que estou a usar os mais recentes ficheiros-tampão de protocolo? 

Os ficheiros-tampão de protocolo mais recentes podem ser [obtidos aqui.](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) Sempre que atualizarmos os ficheiros do contrato, eles aparecerão neste local. Embora não exista um plano imediato para atualizar os ficheiros do protocolo, procure o nome do pacote no topo dos ficheiros para conhecer a versão. Deve ler-se: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Quaisquer atualizações a estes ficheiros incrementarão o "valor V" no final do nome. 

> [!NOTE]
> Uma vez que o Live Video Analytics utiliza a versão proto3 do idioma, os campos são opcionais, o que o torna compatível para trás e para a frente. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Que funcionalidades gRPC estão disponíveis para eu usar com o Live Video Analytics? Quais as características obrigatórias e quais são opcionais? 

Quaisquer funcionalidades gRPC do lado do servidor podem ser utilizadas desde que o contrato de protobof esteja cumprido. 

## <a name="monitoring-and-metrics"></a>Monitorização e métricas

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Posso monitorizar o gráfico de mídia na borda usando a Grade de Eventos?

Sim. Pode consumir as métricas prometheus e publicá-las na grelha de eventos. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Posso usar o Azure Monitor para ver a saúde, as métricas e o desempenho dos meus gráficos mediáticos na nuvem ou no limite?

Sim. Esta ação é suportada. Saiba mais sobre [como utilizar as Métricas do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Existem ferramentas para facilitar a monitorização do módulo IoT Edge dos Serviços de Comunicação Social?

O Código do Estúdio Visual suporta a extensão "Azure IoT Tools" que lhe permite monitorizar facilmente os pontos finais do módulo LVAEdge. Pode utilizar esta ferramenta para começar rapidamente a monitorizar o ponto final incorporado do IoT Hub para "eventos" e ver as mensagens de inferência que são encaminhadas do dispositivo de borda para a nuvem. 

Além disso, pode utilizar esta extensão para editar o Módulo Twin para o módulo LVAEdge para modificar as definições de gráfico de mídia.

Para obter mais informações, consulte o artigo [de monitorização e registo.](monitoring-logging.md)

## <a name="billing-and-availability"></a>Faturação e disponibilidade

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Como é que o Live Video Analytics no IoT Edge é cobrado?

Consulte [a página de preços](https://azure.microsoft.com/pricing/details/media-services/) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Get start - Live Video Analytics on IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
