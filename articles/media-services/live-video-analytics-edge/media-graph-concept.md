---
title: Conceito de gráfico de mídia - Azure
description: O gráfico de mídia permite definir de onde os meios de comunicação devem ser capturados, como deve ser processado e onde os resultados devem ser entregues. Este artigo apresenta uma descrição detalhada do conceito de gráfico mediático.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: ee055c34fd37d2d1cc5e7d0bb5147c945dcbff94
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260968"
---
# <a name="media-graph-concept"></a>Conceito de gráfico de mídia

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)

## <a name="overview"></a>Descrição geral

O gráfico de mídia permite definir de onde os meios de comunicação devem ser capturados, como deve ser processado e onde os resultados devem ser entregues. Um gráfico de mídia é composto por gódes de origem, processador e pia. O diagrama abaixo fornece uma representação gráfica de um gráfico mediático.   

![Uma representação gráfica de um gráfico mediático](./media/media-graph/overview.png)


* **Os nós de origem** permitem a captura de meios de comunicação no gráfico de mídia. Os meios de comunicação neste contexto, conceptualmente, podem ser um fluxo de áudio, um fluxo de vídeo, um fluxo de dados, ou um fluxo que tenha áudio, vídeo e/ou dados combinados num único fluxo.
* **Os nós do processador** permitem o processamento de meios de comunicação dentro do gráfico de mídia.
* **Os nós de sink permitem** entregar os resultados do processamento a serviços e aplicações fora do gráfico de mídia.

## <a name="media-graph-topologies-and-instances"></a>Topologias e instâncias de gráficos de mídia 

O Live Video Analytics on IoT Edge permite-lhe gerir gráficos de mídia através de dois conceitos – "topologia de gráficos" e "exemplo de gráfico". Uma topologia de gráfico permite definir uma planta de um gráfico, com parâmetros como espaços reservados para valores. A topologia define quais os nós que são usados no gráfico mediático, e como estão ligados dentro do gráfico mediático. Os valores para os parâmetros podem ser especificados ao criar instâncias de gráfico referentes à topologia. Isto permite-lhe criar múltiplas instâncias referentes à mesma topologia, mas com valores diferentes para os parâmetros especificados na topologia. 

## <a name="media-graph-states"></a>Estados gráficos de mídia  

O gráfico de mídia pode estar num dos seguintes estados:

* Inativo – representa o estado em que um gráfico mediático é configurado, mas não ativo.
* Ativação – o estado quando um gráfico mediático está a ser instantâneo (isto é, o estado de transição entre inativo e ativo).
* Ativo – o estado quando um gráfico de mídia está ativo. 

    > [!NOTE]
    >  O gráfico de mídia pode estar ativo sem que os dados fluam através dele (por exemplo, a fonte de vídeo de entrada fica offline).
* Desativar – Este é o estado em que um gráfico mediático está em transição de ativo para inativo.

O diagrama abaixo ilustra a máquina de estado de gráfico de mídia.

![Máquina de estado de gráfico de mídia](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>Fontes, processadores e pias  

Live Video Analytics on IoT Edge suporta os seguintes tipos de nó de nó dentro de um gráfico de mídia:

### <a name="sources"></a>Origens 

#### <a name="rtsp-source"></a>Fonte RTSP 

Uma fonte RTSP permite capturar meios de comunicação a partir de um servidor [RTSP.](https://tools.ietf.org/html/rfc2326) O RTSP é utilizado para estabelecer e controlar as sessões de mídia entre um servidor e um cliente. O nó de origem RTSP no gráfico de mídia funciona como cliente e pode estabelecer uma sessão com o servidor RTSP especificado. Muitos dispositivos, como a maioria das [câmaras IP,](https://en.wikipedia.org/wiki/IP_camera) têm um servidor RTSP incorporado. [A ONVIF](https://www.onvif.org/) obriga o RTSP a ser apoiado na sua definição de [dispositivos compatíveis com perfis G, S & T.](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) O nó de origem RTSP no gráfico de mídia requer que especifique um URL RTSP, juntamente com credenciais para ativar uma ligação autenticada.

#### <a name="iot-hub-message-source"></a>Fonte de mensagem IoT Hub 

À semelhança de [outros módulos IoT Edge,](../../iot-edge/iot-edge-glossary.md#iot-edge-module)o live video analytics no módulo IoT Edge pode receber mensagens através do [hub IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Estas mensagens podem ser enviadas a partir de outros módulos, ou aplicações em execução no dispositivo Edge, ou a partir da nuvem. Tais mensagens podem ser entregues (encaminhadas) para uma [entrada nomeada](../../iot-edge/module-composition.md#sink) no módulo. Uma fonte de mensagem IoT Hub permite que tais mensagens sejam encaminhadas para uma instância de gráfico de mídia. Estas mensagens ou sinais podem então ser utilizados internamente no gráfico de mídia, normalmente para ativar portões de sinal (ver [processador do portão de sinal](#signal-gate-processor) abaixo). 

Por exemplo, pode ter um módulo IoT Edge que gera uma mensagem quando uma porta é aberta. A mensagem desse módulo pode ser encaminhada para o hub IoT Edge, de onde pode ser encaminhado para a mensagem do hub IoT de um gráfico de mídia. Dentro do gráfico de mídia, a fonte de mensagem do hub IoT pode passar o evento para um processador do portão de sinal, que pode então ligar a gravação do vídeo a partir de uma fonte RTSP para um ficheiro. 

### <a name="processors"></a>Processadores  

#### <a name="motion-detection-processor"></a>Processador de deteção de movimentos 

O processador de deteção de movimentos permite-lhe detetar movimento em vídeo ao vivo. Examina o vídeo de entrada e determina se há movimento no vídeo. Se o movimento for detetado, passa o vídeo para o nó a jusante e emite um evento. O processador de deteção de movimentos (em conjunto com outros nós gráficos de mídia) pode ser utilizado para ativar a gravação do vídeo de entrada quando houver movimento detetado.

#### <a name="frame-rate-filter-processor"></a>Processador de filtro de taxa de fotogramas  

O processador de filtro de taxa de fotogramas permite-lhe recolher quadros a partir do fluxo de vídeo de entrada a uma taxa de fotogramas especificada. Isto permite-lhe reduzir o número de quadros enviados para nós de fluxo (como processador de extensão HTTP) para posterior processamento.

#### <a name="http-extension-processor"></a>Processador de extensão HTTP 

O processador de extensão HTTP permite-lhe ligar a sua própria IA a um gráfico de mídia. O processador de extensão HTTP toma como quadros de vídeo descodificados de entrada e retransmite tais quadros para um ponto final HTTP. O processador tem a capacidade de autenticar com o ponto final HTTP, se necessário. Além disso, o processador tem formato de imagem incorporado que permite a escala e codificação de quadros de vídeo antes de serem retransmitidos para a frente. O dimensionamento tem opções para que a relação de aspeto de imagem seja preservada, acolchoada ou esticada, enquanto a codificação fornece opções para diferentes codificações de imagem, tais como jpeg, png ou bmp.

#### <a name="signal-gate-processor"></a>Processador do portão de sinal  

O processador do portão de sinal permite que os meios de comunicação sejam reencaminhados condicionalmente de um nó para outro. Também funciona como um tampão, permitindo a sincronização de meios e eventos. Um exemplo de utilização é inserir um processador de portão de sinal entre uma fonte RTSP e um lavatório de ativos e usar a saída do processador de detetor de movimento para ativar o portão. Com um gráfico de mídia deste tipo, só pode desencadear a gravação dos meios de comunicação quando o movimento for detetado no vídeo de entrada. 

### <a name="sinks"></a>Pias  

#### <a name="asset-sink"></a>Pia de ativos  

Um lavatório de ativos permite que um gráfico de mídia escreva dados de meios (vídeo e/ou áudio) para um ativo da Azure Media Services. Consulte a secção [de ativos](terminology.md#asset) para obter mais informações sobre os ativos e o seu papel na gravação e reprodução de meios de comunicação.  

#### <a name="file-sink"></a>Pia de arquivo  

O lavatório de ficheiros permite que um gráfico de mídia escreva dados de mídia (vídeo e/ou áudio) para uma localização no sistema de ficheiros local do dispositivo IoT Edge. A pia deve estar a jusante de um processador do portão de sinalização. Isto limita a duração dos ficheiros de saída aos valores especificados nas propriedades do processador do portão de sinal.

#### <a name="iot-hub-message-sink"></a>Pia de mensagem IoT Hub  

Um lavatório de mensagens IoT Hub permite-lhe publicar eventos no hub IoT Edge. O hub de borda pode então encaminhar os dados para outros módulos ou aplicações no dispositivo de borda, ou para IoT Hub na nuvem (por rotas especificadas no manifesto de implantação). O lava-mensagens IoT Hub pode aceitar eventos de processadores a montante, como um processador de deteção de movimentos ou de um serviço de inferência externa através de processador de extensão HTTP.

## <a name="scenarios"></a>Cenários

Utilizando uma combinação da origem, processador e nós de pia, definidos acima, pode construir gráficos de mídia para uma variedade de cenários. Alguns cenários de exemplo são os seguintes

* [Gravação contínua de vídeo](continuous-video-recording-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Análise de vídeo ao vivo sem gravação de vídeo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Passos seguintes

Siga o [Quickstart: Executar Live Video Analytics com o seu próprio](use-your-model-quickstart.md) artigo de modelo para ver como pode executar a deteção de movimentos num feed de vídeo ao vivo.
