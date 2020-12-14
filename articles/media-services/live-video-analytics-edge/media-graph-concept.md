---
title: Conceito de gráfico de mídia - Azure
description: Um gráfico mediático permite definir de onde os meios de comunicação devem ser capturados, como deve ser processado e onde os resultados devem ser entregues. Este artigo apresenta uma descrição detalhada do conceito de gráfico mediático.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 6f23e7db8cecb46106a63fdecdb6ba04dbd99682
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401105"
---
# <a name="media-graph"></a>Grafo do suporte de dados

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)

## <a name="overview"></a>Descrição Geral

Um gráfico mediático permite definir de onde os meios de comunicação devem ser capturados, como deve ser processado e onde os resultados devem ser entregues. Consegue-o ligando componentes, ou nós, da maneira desejada. O diagrama abaixo fornece uma representação gráfica de um gráfico mediático.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="Grafo do suporte de dados":::

Live Video Analytics on IoT Edge suporta diferentes tipos de fontes, processadores e pias.

* **Os nós de origem** permitem a captura de meios de comunicação no gráfico de mídia. Os meios de comunicação neste contexto, conceptualmente, podem ser um fluxo de áudio, um fluxo de vídeo, um fluxo de dados, ou um fluxo que tenha áudio, vídeo e/ou dados combinados num único fluxo.
* **Os nós do processador** permitem o processamento de meios de comunicação dentro do gráfico de mídia.
* **Os nós de sink permitem** entregar os resultados do processamento a serviços e aplicações fora do gráfico de mídia.

## <a name="media-graph-topologies-and-instances"></a>Topologias e instâncias de gráficos de mídia 

O Live Video Analytics on IoT Edge permite-lhe gerir gráficos de mídia através de dois conceitos – "topologia de gráficos" e "exemplo de gráfico". Uma topologia de gráfico permite definir a planta de um gráfico, com parâmetros como espaços reservados para valores. A topologia define quais os nós que são usados no gráfico mediático, e como estão ligados dentro do gráfico mediático. Por exemplo, se quisesse gravar o feed a partir de uma câmara, precisaria de um gráfico com um nó de origem que recebe vídeo e um nó de pia que escreve o vídeo.

Os valores para os parâmetros na topologia são especificados quando cria instâncias de gráfico que referenciam a topologia. Isto permite-lhe criar múltiplas instâncias referentes à mesma topologia, mas com valores diferentes para os parâmetros especificados na topologia. No exemplo acima, poderia ter usado parâmetros para representar o endereço IP da câmara e o nome para o vídeo gravado. Você poderia criar muitas instâncias de gráfico com essa topologia - um exemplo para cada câmara em um edifício, talvez, cada um com o endereço IP específico e nome específico.

## <a name="media-graph-states"></a>Estados gráficos de mídia  

O ciclo de vida das topologias de gráficos e das instâncias de gráfico é mostrado no diagrama de estado seguinte.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="Topologia de gráficos e gráfico exemplo ciclo de vida":::

Começa-se por [criar uma topologia de gráficos.](direct-methods.md#graphtopologyset) Em seguida, para cada vídeo em direto que pretende processar com esta topologia, [cria-se uma instância de gráfico](direct-methods.md#graphinstanceset). 

A instância do gráfico estará no `Inactive` estado (ocioso).

Quando estiver pronto para enviar o feed de vídeo [](direct-methods.md#graphinstanceactivate) ao vivo para a instância do gráfico, ative-o. A instância do gráfico passará brevemente por um estado de `Activating` transição, e se for bem sucedida, entrará num `Active` estado. No `Active` estado, os meios de comunicação serão processados (se a instância do gráfico receber dados de entrada).

> [!NOTE]
>  Uma instância de gráfico pode ser ativa sem que os dados fluam através dele (por exemplo, a câmara fica offline).
> A sua subscrição Azure será faturada quando a instância do gráfico estiver no estado ativo.

Pode repetir o processo de criação e ativação de outras instâncias de gráfico para a mesma topologia, se tiver outros feeds de vídeo ao vivo para processar.

Quando terminar de processar o feed de vídeo ao vivo, pode [desativar](direct-methods.md#graphinstancedeactivate) a instância do gráfico. A instância do gráfico passará brevemente por um estado de `Deactivating` transição, lavará todos os dados que tiver e, em seguida, regressará ao `Inactive` estado.

Só pode [apagar](direct-methods.md#graphinstancedelete) uma instância de gráfico quando se encontra no `Inactive` estado.

Depois de todas as instâncias de gráfico que se referem a uma topologia de gráficos específicas terem sido eliminadas, pode [eliminar a topologia do gráfico](direct-methods.md#graphtopologydelete).


## <a name="sources-processors-and-sinks"></a>Fontes, processadores e pias  

Live Video Analytics on IoT Edge suporta os seguintes tipos de nó de nó dentro de um gráfico de mídia:

### <a name="sources"></a>Origens 

#### <a name="rtsp-source"></a>Fonte RTSP 

Um nó de origem RTSP permite-lhe ingerir meios de comunicação a partir de um servidor [RTSP.](https://tools.ietf.org/html/rfc2326) As câmaras de vigilância e ip transmitem os seus dados num protocolo chamado RTSP (protocolo de streaming em tempo real) que é diferente de outros tipos de dispositivos, como telefones e câmaras de vídeo. Este protocolo é utilizado para estabelecer e controlar as sessões de mídia entre um servidor (a câmara) e um cliente. O nó de origem RTSP num gráfico de mídia funciona como cliente e pode estabelecer uma sessão com um servidor RTSP. Muitos dispositivos, como a maioria das [câmaras IP,](https://en.wikipedia.org/wiki/IP_camera) têm um servidor RTSP incorporado. [A ONVIF](https://www.onvif.org/) obriga o RTSP a ser apoiado na sua definição de [dispositivos compatíveis com perfis G, S & T.](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) O nó de origem RTSP requer que especifique um URL RTSP, juntamente com credenciais para ativar uma ligação autenticada.

#### <a name="iot-hub-message-source"></a>Fonte de mensagem IoT Hub 

À semelhança de [outros módulos IoT Edge,](../../iot-edge/iot-edge-glossary.md#iot-edge-module)o live video analytics no módulo IoT Edge pode receber mensagens através do [hub IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Estas mensagens podem ser enviadas a partir de outros módulos, ou aplicações em execução no dispositivo Edge, ou a partir da nuvem. Tais mensagens são entregues (encaminhadas) para uma [entrada nomeada](../../iot-edge/module-composition.md#sink) no módulo. Um nó de mensagem IoT Hub permite que tais mensagens cheguem a um gráfico de mídia. Estas mensagens ou sinais podem então ser utilizados internamente no gráfico de mídia, normalmente para ativar os portões de sinal (ver [portões](#signal-gate-processor) de sinal abaixo). 

Por exemplo, pode ter um módulo IoT Edge que gera uma mensagem quando uma porta é aberta. A mensagem desse módulo pode ser encaminhada para o hub IoT Edge, de onde pode ser encaminhado para a mensagem do hub IoT de um gráfico de mídia. Dentro do gráfico de mídia, a fonte de mensagem do hub IoT pode passar o evento para um processador do portão de sinal, que pode então ligar a gravação do vídeo a partir de uma fonte RTSP para um ficheiro. 

### <a name="processors"></a>Processadores  

#### <a name="motion-detection-processor"></a>Processador de deteção de movimentos 

O nó do processador de deteção de movimentos permite-lhe detetar movimento em vídeo ao vivo. Examina os quadros de vídeo que entram e determina se há movimento no vídeo. Se o movimento for detetado, passa nas molduras de vídeo para o componente a jusante e emite um evento. O nó do processador de deteção de movimentos (em conjunto com outros nós) pode ser utilizado para ativar a gravação do vídeo de entrada quando houver movimento detetado.

#### <a name="frame-rate-filter-processor"></a>Processador de filtro de taxa de fotogramas  

O nó do processador do filtro de taxa de fotogramas permite-lhe recolher os quadros da transmissão de vídeo recebida a uma taxa especificada. Isto permite-lhe reduzir o número de fotogramas enviados para componentes de fluxo (como o nó do processador de extensão HTTP) para posterior processamento.
>[!WARNING]
> Este processador está **precotado** na mais recente versão do Live Video Analytics no módulo IoT Edge. A gestão da Taxa de Fotogramas é agora suportada nos próprios processadores de extensão de gráficos.

#### <a name="http-extension-processor"></a>Processador de extensão HTTP

O nó do processador de extensão HTTP permite-lhe ligar o seu próprio módulo IoT Edge a um gráfico de mídia. Este nó leva quadros de vídeo descodificados como a entrada, e transmite tais quadros para um ponto final HTTP REST exposto pelo seu módulo. Este nó tem a capacidade de autenticar com o ponto final REST, se necessário. Além disso, o nó tem um formato de imagem incorporado para dimensionamento e codificação de quadros de vídeo antes de serem retransmitidos para o ponto final REST. O scaler tem opções para que a relação de aspeto de imagem seja preservada, acolchoada ou esticada. O codificadores de imagem suporta os formatos JPEG, PNG ou BMP. Saiba mais sobre o processador [aqui.](media-graph-extension-concept.md#http-extension-processor)

#### <a name="grpc-extension-processor"></a>processador de extensão gRPC

O nó do processador de extensão gRPC leva quadros de vídeo descodificados como entrada, e retransmite esses quadros para um ponto final [gRPC](terminology.md#grpc) exposto pelo seu módulo. O nó suporta a transferência de dados utilizando [memória partilhada](https://en.wikipedia.org/wiki/Shared_memory) ou incorporando diretamente o conteúdo no corpo de mensagens gRPC. Além disso, o nó tem um formato de imagem incorporado para dimensionamento e codificação de quadros de vídeo antes de serem retransmitidos para o ponto final gRPC. O scaler tem opções para que a relação de aspeto de imagem seja preservada, acolchoada ou esticada. O codificadores de imagem suporta formatos jpeg, png ou bmp. Saiba mais sobre o processador [aqui.](media-graph-extension-concept.md#grpc-extension-processor)

#### <a name="signal-gate-processor"></a>Processador do portão de sinal  

O nó do processador do portão de sinal permite-lhe encaminhar os meios de um nó para outro. Também funciona como um tampão, permitindo a sincronização de meios e eventos. Uma caixa de utilização típica é inserir um nó do processador do portão de sinal entre o nó de origem RTSP e o nó da pia do ativo, e usar a saída de um nó do processador de processador de movimento para acionar o portão. Com um gráfico tão mediático, só estaria a gravar vídeos quando o movimento for detetado.

### <a name="sinks"></a>Pias  

#### <a name="asset-sink"></a>Pia de ativos  

Um nó de afundanço de ativos permite-lhe escrever dados de meios (vídeo e/ou áudio) a um ativo da Azure Media Services. Só pode haver um nó de afundar um ativo num gráfico mediático. Consulte a secção [de ativos](terminology.md#asset) para obter mais informações sobre os ativos e o seu papel na gravação e reprodução de meios de comunicação. Também pode ver o artigo [de gravação de vídeo contínuo](continuous-video-recording-concept.md) para obter detalhes sobre como as propriedades deste nó são usadas.

#### <a name="file-sink"></a>Pia de arquivo  

O nó da pia do ficheiro permite-lhe escrever dados de mídia (vídeo e/ou áudio) para uma localização no sistema de ficheiros local do dispositivo IoT Edge. Só pode haver um nó de lavatório de ficheiros num gráfico de mídia, e deve ser a jusante de um nó do processador do portão de sinal. Isto limita a duração dos ficheiros de saída aos valores especificados nas propriedades do nó do processador do portão de sinal. Para garantir que o seu dispositivo de borda não se esgota no espaço do disco, também pode definir o tamanho máximo que o módulo Live Video Analytics no módulo IoT Edge pode usar para armazenar dados.  
> [!NOTE]
Se o Sumidouro de Ficheiros ficar cheio, o módulo Live Video Analytics no IoT Edge começará a eliminar os dados mais antigos e a substituí-lo pelo novo.
#### <a name="iot-hub-message-sink"></a>Pia de mensagem IoT Hub  

Um nó de pia de mensagem IoT Hub permite-lhe publicar eventos no hub IoT Edge. O hub IoT Edge pode então encaminhar os dados para outros módulos ou aplicações no dispositivo de borda, ou para ioT Hub na nuvem (por rotas especificadas no manifesto de implantação). O nó da pia de mensagem IoT Hub pode aceitar eventos de processadores a montante, como um nó do processador de deteção de movimentos, ou de um serviço de inferência externa através de um nó de processador de extensão HTTP.

## <a name="rules-on-the-use-of-nodes"></a>Regras relativas à utilização de nosdes

Consulte [as limitações nas topologias de gráficos](quotas-limitations.md#limitations-on-graph-topologies-at-preview) para obter regras adicionais sobre como diferentes nós podem ser usados dentro de um gráfico de mídia.

## <a name="scenarios"></a>Cenários

Utilizando uma combinação das fontes, processadores e pias acima definidas, pode construir gráficos de mídia para uma variedade de cenários que envolvam a análise de vídeo ao vivo. Os cenários de exemplo são:

* [Gravação de vídeo contínua](continuous-video-recording-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Live Video Analytics sem gravação de vídeo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Passos seguintes

Para ver como pode executar a deteção de movimentos num feed de vídeo ao vivo, consulte [Quickstart: Run Live Video Analytics com o seu próprio modelo](use-your-model-quickstart.md).
