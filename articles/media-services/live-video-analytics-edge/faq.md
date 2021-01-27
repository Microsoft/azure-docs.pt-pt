---
title: Vídeo ao vivo analytics em IoT Edge FAQ - Azure
description: Este artigo responde a perguntas comumente sobre live video analytics on IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 0cb378bf614582070dd1bdd0a11706b26437af53
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880055"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Análise de vídeo ao vivo em IoT Edge FAQ

Este artigo responde a perguntas comumente sobre live video analytics em Azure IoT Edge.

## <a name="general"></a>Geral

**Que variáveis de sistema posso usar na definição de topologia do gráfico?**

| Variável   |  Descrição  | 
| --- | --- | 
| [Sistema.DataTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Representa um instante na hora utc, tipicamente expresso como data e hora do dia no seguinte formato:<br>*yMddTHHmmssZ* | 
| System.PreciseDateTime | Representa uma data-tempo universal coordenada (UTC) num formato compatível com ficheiros ISO8601 com milissegundos, no seguinte formato:<br>*yMddTHHmmss.fffZ* | 
| Nome System.GraphTopology   | Representa uma topologia de gráficos de mídia, e detém a planta de um gráfico. | 
| Nome system.graphInstance |    Representa uma instância de gráfico de mídia, detém valores de parâmetros, e faz referência à topologia. | 

## <a name="configuration-and-deployment"></a>Configuração e implantação

**Posso implantar o módulo de borda de mídia para um dispositivo Windows 10?**

Yes. Para mais informações, consulte [os recipientes Linux no Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Captura a partir de câmara IP e definições de RTSP

**Preciso de utilizar um SDK especial no meu dispositivo para enviar uma transmissão de fluxo de vídeo?**

Não, live video analytics on IoT Edge suporta a captura de meios através da utilização de RTSP (Protocolo de Streaming em Tempo Real) para streaming de vídeo, que é suportado na maioria das câmaras IP.

**Posso pressionar os meios de comunicação para o Live Video Analytics no IoT Edge utilizando Real-Time Protocolo de Mensagens (RTMP) ou Protocolo de Streaming Suave (como um Evento Ao Vivo dos Serviços de Media)?**

Não, o Live Video Analytics suporta apenas o RTSP para capturar vídeos a partir de câmaras IP. Qualquer câmara que suporte o streaming RTSP sobre o TCP/HTTP deve funcionar. 

**Posso reiniciar ou atualizar o URL de origem RTSP numa instância de gráfico?**

Sim, quando a instância do gráfico está em estado *inativo.*  

**Um simulador RTSP está disponível para ser utilizado durante o teste e desenvolvimento?**

Sim, um módulo de borda [de simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) está disponível para uso nos quickstarts e tutoriais para suportar o processo de aprendizagem. Este módulo é fornecido como o melhor esforço e pode nem sempre estar disponível. Recomendamos vivamente que *não* utilize o simulador por mais de algumas horas. Deve investir em testes com a sua fonte rtsp real antes de planear uma implantação de produção.

**É suportada a deteção ONVIF de câmaras IP no Edge?**

Não, não suportamos a descoberta de dispositivos no limite do Open Network Video Interface Forum (ONVIF).

## <a name="streaming-and-playback"></a>Streaming e reprodução

**Posso reproduzir os ativos registados na Azure Media Services a partir do limite, utilizando tecnologias de streaming como o HLS ou o DASH?**

Yes. Pode transmitir ativos registados como qualquer outro ativo na Azure Media Services. Para transmitir o conteúdo, tem de ter um ponto final de streaming criado e no estado de funcionamento. A utilização do processo padrão de criação do Localizador de Streaming irá dar-lhe acesso a um apple HTTP Live Streaming (HLS) ou a Um Streaming Adaptativo Dinâmico sobre HTTP (DASH, também conhecido como MPEG-DASH) manifesto para streaming para qualquer estrutura de jogador capaz. Para obter mais informações sobre a criação e publicação de manifestos HLS ou DASH, consulte [a embalagem dinâmica.](../latest/dynamic-packaging-overview.md)

**Posso utilizar a proteção padrão de conteúdos e as funcionalidades de DRM dos Serviços de Mídia num ativo arquivado?**

Yes. Todas as funcionalidades de proteção de conteúdos de encriptação dinâmica padrão e gestão de direitos digitais (DRM) estão disponíveis para utilização em ativos que são registados a partir de um gráfico de mídia.

**Que jogadores posso usar para ver conteúdo dos ativos gravados?**

Todos os jogadores padrão que suportam a versão 3 ou a versão 4 do HLS em conformidade são suportados. Além disso, qualquer jogador que seja capaz de cumprir a reprodução MPEG-DASH também é suportado.

Os jogadores recomendados para testes incluem:

* [Media Player do Azure](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Jogador shaka](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple nativo HTTP Live Streaming](https://developer.apple.com/streaming/)
* Leitor de vídeo Edge, Chrome ou Safari incorporado HTML5
* Jogadores comerciais que suportam reprodução HLS ou DASH

**Quais são os limites para o streaming de um ativo gráfico de mídia?**

O streaming de um ativo vivo ou gravado a partir de um gráfico de mídia utiliza a mesma infraestrutura de alta escala e ponto final de streaming que os Media Services suportam para streaming on-demand e live para Media & Entertainment, Over the Top (OTT) e clientes de transmissão. Isto significa que pode, de forma rápida e fácil, ativar a Rede de Entrega de Conteúdos Azure, a Verizon ou a Akamai para entregar o seu conteúdo a um público tão pequeno como alguns espectadores ou até milhões, dependendo do seu cenário.

Pode entregar conteúdo utilizando o Apple HLS ou o MPEG-DASH.

## <a name="design-your-ai-model"></a>Desenhe o seu modelo de IA 

**Tenho vários modelos de IA embrulhados num contentor do Docker. Como devo usá-los com video analytics ao vivo?** 

As soluções variam consoante o protocolo de comunicação que é utilizado pelo servidor de inferencing para comunicar com o Live Video Analytics. As seguintes secções descrevem como cada protocolo funciona.

*Utilize o protocolo HTTP*:

* Recipiente único (único lvaExtension):  

   No seu servidor de inferenculação, pode utilizar uma única porta, mas diferentes pontos finais para diferentes modelos de IA. Por exemplo, para uma amostra python você pode usar `route` diferentes s por modelo, como mostrado aqui: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   E então, na sua implementação live video analytics, quando instantaneamente gráficos, deite o URL do servidor de inferência para cada instância, como mostrado aqui: 

   1º caso: URL do servidor de inferência=`http://lvaExtension:44000/score/face_detection`<br/>
   2º caso: URL do servidor de inferência=`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Em alternativa, pode expor os seus modelos de IA em diferentes portas e chamá-los quando os fizer gráficos instantâneos.  

* Vários contentores: 

   Cada recipiente é implantado com um nome diferente. Anteriormente, no conjunto de documentação live video analytics, mostramos-lhe como implementar uma extensão chamada *LvaExtension*. Agora pode desenvolver dois recipientes diferentes, cada um com a mesma interface HTTP, o que significa que têm o mesmo `/score` ponto final. Desdobre estes dois recipientes com nomes diferentes e certifique-se de que ambos estão a ouvir em *diferentes portas*. 

   Por exemplo, um contentor chamado `lvaExtension1` está a ouvir o `44000` porto, e um segundo recipiente com o nome `lvaExtension2` está a ouvir o `44001` porto. 

   Na sua topologia live video analytics, você instantaneamente dois gráficos com URLs de inferência diferentes, como mostrado aqui: 

   Primeira instância: URL do servidor de inferência = `http://lvaExtension1:44001/score`    
   Segunda instância: URL do servidor de inferência = `http://lvaExtension2:44001/score`
   
*Utilize o protocolo gRPC:* 

* Com o módulo Live Video Analytics 1.0, quando utiliza um protocolo de chamada remota de uso geral (gRPC), a única maneira de o fazer é se o servidor gRPC expor diferentes modelos de IA através de diferentes portas. [Neste exemplo de código](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json), uma única porta, 44000, expõe todos os modelos yolo. Em teoria, o servidor yolo gRPC poderia ser reescrito para expor alguns modelos na porta 44000 e outros na porta 45000. 

* Com o módulo Live Video Analytics 2.0, uma nova propriedade é adicionada ao nó de extensão gRPC. Esta propriedade, **extensãoConfiguration,** é uma cadeia opcional que pode ser usada como parte do contrato gRPC. Quando tem vários modelos de IA embalados num único servidor de inferência, não precisa de expor um nó para cada modelo de IA. Em vez disso, para uma instância de gráfico, você, como fornecedor de extensão, pode definir como selecionar os diferentes modelos de IA utilizando a **propriedade extensionConfiguration.** Durante a execução, o Live Video Analytics passa esta cadeia para o servidor de inferenculação, que pode usá-lo para invocar o modelo de IA desejado. 

**Estou a construir um servidor gRPC em torno de um modelo de IA, e quero ser capaz de suportar a sua utilização através de várias câmaras ou instâncias de gráficos. Como devo construir o meu servidor?** 

 Em primeiro lugar, certifique-se de que o seu servidor consegue lidar com mais do que um pedido de cada vez ou trabalhar em linhas paralelas. 

Por exemplo, um número padrão de canais paralelos foi definido na seguinte [amostra de Vídeo Ao Vivo Analytics gRPC](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py): 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Na instantânea do servidor GRPC anterior, o servidor pode abrir apenas três canais de cada vez por câmara, ou por instância de topologia de gráficos. Não tente ligar mais de três instâncias ao servidor. Se tentar abrir mais de três canais, os pedidos estarão pendentes até que um canal existente caia.  

A implementação anterior do servidor gRPC é usada nas nossas amostras Python. Como desenvolvedor, pode implementar o seu próprio servidor ou utilizar a implementação predefinida anterior para aumentar o número de trabalhadores, que definiu para o número de câmaras a utilizar para feeds de vídeo. 

Para configurar e utilizar várias câmaras, pode instantaneamente várias instâncias de topologia de gráficos, cada uma apontando para o mesmo ou um servidor de inferência diferente (por exemplo, o servidor mencionado no parágrafo anterior). 

**Quero poder receber vários quadros a montante antes de tomar uma decisão infernante. Como posso permitir isso?** 

As [nossas atuais amostras padrão](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) funcionam num modo *apátrida.* Não mantêm o estado das chamadas anteriores, nem mesmo quem ligou. Isto significa que várias instâncias de topologia podem chamar o mesmo servidor de inferência, mas o servidor não consegue distinguir quem está a ligar ou o estado por chamada. 

*Utilize o protocolo HTTP*:

Para manter o estado, cada chamada, ou instância de topologia de gráfico, chama o servidor de inferencing usando o parâmetro de consulta HTTP que é único para quem telefona. Por exemplo, os endereços URL do servidor de inferência para cada instância são mostrados aqui:  

1ª instância topologia= `http://lvaExtension:44000/score?id=1`<br/>
2ª instância de topologia= `http://lvaExtension:44000/score?id=2`

… 

No lado do servidor, a rota de pontuação sabe quem está a ligar. Se iD=1, então pode manter o estado separadamente para que o chamador ou a instância de topologia de gráficos. Em seguida, pode guardar as molduras de vídeo recebidas num tampão. Por exemplo, use uma matriz ou um dicionário com uma chave DateTime, e o valor é o quadro. Em seguida, pode definir o servidor para processar (inferir) depois de *x* número de quadros serem recebidos. 

*Utilize o protocolo gRPC:* 

Com uma extensão gRPC, cada sessão é para um único feed de câmara, por isso não há necessidade de fornecer um ID. Agora, com a extensãoConfiguration propriedade, você pode armazenar os quadros de vídeo em um tampão e definir o servidor para processar (inferir) depois de *x* número de quadros serem recebidos. 

**Todos os ProcessMediaStreams de um determinado recipiente funcionam no mesmo modelo de IA?** 

Não. Iniciar ou parar chamadas do utilizador final numa instância de gráfico constitui uma sessão, ou talvez haja uma câmara desligada ou reconectar-se. O objetivo é persistir uma sessão se a câmara estiver a transmitir vídeo. 

* Duas câmaras que enviam vídeo para processamento criam duas sessões. 
* Uma câmara que vai para um gráfico que tem dois nós de extensão gRPC cria duas sessões. 

Cada sessão é uma ligação duplex completa entre o Live Video Analytics e o servidor gRPC, e cada sessão pode ter um modelo ou pipeline diferente. 

> [!NOTE]
> Em caso de desconexão ou reconexão da câmara, com a câmara a ficar offline por um período além dos limites de tolerância, o Live Video Analytics abrirá uma nova sessão com o servidor gRPC. Não há necessidade de o servidor rastrear o estado através destas sessões. 

Live Video Analytics também adiciona suporte para múltiplas extensões gRPC para uma única câmara em uma instância de gráfico. Pode utilizar estas extensões gRPC para efetuar o processamento de IA sequencialmente, paralelamente, ou como uma combinação de ambos. 

> [!NOTE]
> Ter múltiplas extensões executadas em paralelo irá afetar os seus recursos de hardware. Tenha isto em mente enquanto escolhe o hardware que se adequa às suas necessidades computacionais. 

**Qual é o número máximo de ProcessMediaStreams simultâneos?** 

O Live Video Analytics não aplica limites a este número.  

**Como posso decidir se o meu servidor de inferencing deve usar CPU ou GPU ou qualquer outro acelerador de hardware?** 

A sua decisão depende da complexidade do modelo de IA desenvolvido e da forma como pretende utilizar o CPU e os aceleradores de hardware. À medida que está a desenvolver o modelo de IA, pode especificar que recursos o modelo deve usar e que ações deve realizar. 

**Como guardo imagens com caixas de delimitação pós-processamento?** 

Hoje, estamos a fornecer coordenadas de caixa limitadas apenas como mensagens de inferência. Você pode construir um streamer MJPEG personalizado que pode usar estas mensagens e sobrepor as caixas de delimitação nos quadros de vídeo.  

## <a name="grpc-compatibility"></a>compatibilidade gRPC 

**Como saberei quais são os campos obrigatórios para o descritor de fluxos de mídia?** 

Qualquer campo a que não forneça um valor é dado um [valor padrão, conforme especificado pelo gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Live Video Analytics utiliza a versão *proto3* da linguagem tampão protocolar. Todos os dados do tampão do protocolo utilizados pelos contratos live video analytics estão disponíveis nos [ficheiros tampão](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)do protocolo . 

**Como posso garantir que estou a usar os últimos ficheiros de tampão de protocolo?** 

Pode obter os mais recentes ficheiros-tampão de protocolo no site de [ficheiros contratuais](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Sempre que atualizarmos os ficheiros do contrato, eles estarão neste local. Não há um plano imediato para atualizar os ficheiros do protocolo, por isso procure o nome do pacote no topo dos ficheiros para saber a versão. Deve ler-se: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Quaisquer atualizações a estes ficheiros irão incrementar o "valor V" no final do nome. 

> [!NOTE]
> Como o Live Video Analytics utiliza a versão proto3 do idioma, os campos são opcionais, e a versão é compatível para trás e para a frente. 

**Que funcionalidades gRPC estão disponíveis para eu usar com o Live Video Analytics? Quais as características obrigatórias e quais são opcionais?** 

Pode utilizar quaisquer funcionalidades gRPC do lado do servidor, desde que o contrato de Buffers protocolar (Protobuf) esteja cumprido. 

## <a name="monitoring-and-metrics"></a>Monitorização e métricas

**Posso monitorizar o gráfico mediático no limite utilizando a Grelha de Eventos do Azure?**

Yes. Pode consumir métricas prometheus e publicá-las na sua grelha de eventos. 

**Posso usar o Azure Monitor para ver a saúde, as métricas e o desempenho dos meus gráficos mediáticos na nuvem ou no limite?**

Sim, apoiamos esta abordagem. Para saber mais, consulte [a visão geral do Azure Monitor Metrics](../../azure-monitor/platform/data-platform-metrics.md).

**Existem ferramentas para facilitar a monitorização do módulo IoT Edge dos Serviços de Comunicação Social?**

O Código do Estúdio Visual suporta a extensão Azure IoT Tools, com a qual pode monitorizar facilmente os pontos finais do módulo LVAEdge. Pode utilizar esta ferramenta para começar rapidamente a monitorizar o seu ponto final incorporado no hub IoT para "eventos" e ver as mensagens de inferência que são encaminhadas do dispositivo de borda para a nuvem. 

Além disso, pode utilizar esta extensão para editar o módulo twin para o módulo LVAEdge para modificar as definições de gráfico de mídia.

Para obter mais informações, consulte o artigo [de monitorização e registo.](monitoring-logging.md)

## <a name="billing-and-availability"></a>Faturação e disponibilidade

**Como é que o Live Video Analytics no IoT Edge é cobrado?**

Para obter detalhes de faturação, consulte [os preços dos Serviços de Comunicação Social.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="next-steps"></a>Próximos passos

[Quickstart: Começa com o Live Video Analytics no IoT Edge](get-started-detect-motion-emit-events-quickstart.md)