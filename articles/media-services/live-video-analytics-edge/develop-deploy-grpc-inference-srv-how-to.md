---
title: Desenvolver e implementar um servidor de inferência gRPC - Azure
description: Este artigo fornece orientações sobre como desenvolver e implementar um servidor de inferência gRPC.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 6184a369e73c26d3a8a716f9daf1c0420a5239fe
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881657"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Como guiar – Desenvolver e implementar um servidor de inferência gRPC

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como pode embrulhar os modelos de IA da sua escolha dentro de um servidor de inferência gRPC, para que possa ser integrado com Live Video Analytics (LVA) através de extensão de gráfico. 

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida

* [Extensões de Gráfico de Mídia](media-graph-extension-concept.md)
* [Protocolo de extensão gRPC](grpc-extension-protocol.md)
* [Inferência esquema de metadados](inference-metadata-schema.md)
* [Introdução ao gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 Guia de línguas](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo x86-64 ou um ARM64 que executa um dos [sistemas operativos Linux suportados](../../iot-edge/support.md#operating-systems) ou uma máquina Windows.
* [Instale o Docker](https://docs.docker.com/desktop/#download-and-install) na sua máquina.
* Instale [o tempo de execução do IoT Edge](../../iot-edge/how-to-install-iot-edge.md?tabs=linux).

## <a name="grpc-implementation-steps"></a>etapas de implementação do gRPC

Para criar um servidor de inferência gRPC e implementá-lo como uma extensão com Live Video Analytics, serão utilizados os seguintes passos:

### <a name="setup"></a>Configuração:

Execute os passos necessários para que o [módulo Live Video Analytics tenha sido implantado e a trabalhar num dispositivo IoT Edge](deploy-iot-edge-device.md).

### <a name="high-level-implementation-steps"></a>Etapas de implementação de alto nível:

1. Escolha uma das muitas línguas que são suportadas por gRPC: C#, C++, Dart, Go, Java, Node, Objective-C, PHP, Python, Ruby.
1. Implemente um servidor gRPC que comunicará com o Live Video Analytics utilizando [os ficheiros proto3](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc).

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="servidor gRPC que comunicará com o Live Video Analytics usando os ficheiros proto3":::

    Dentro deste serviço:
    1. Manuseie a troca de mensagens de descrição da sessão entre o servidor e o cliente.
    1. Manuseie [as mensagens de amostra de mídia](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) e devolva os resultados.

        1. Invoque o seu motor de inferenção que utiliza um modelo treinado para fazer inferências nas mensagens que chegam.
        1. Receba os resultados de inferencing do motor, embale-os de volta como uma amostra de mídia e submeta-se de volta ao Live Video Analytics utilizando o ficheiro [inferencing.proto.](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto)

            Em alternativa, invoque qualquer função de transformação mediática para a amostra de mídia.
1. Implemente a implementação do servidor gRPC. Há duas maneiras de fazer isto:

    1. Implementar como um módulo IoT co-localizado com módulo Live Video Analytics
    1. Implemente como um módulo IoT para um nó acessível à rede (na premissa ou na nuvem) que possa trocar dados com o módulo Live Video Analytics.
1. Configure uma topologia de gráficos live video analytics com o módulo Live Video Analytics e aponte-o para o servidor gRPC.

### <a name="recommendation"></a>Recomendação:

Ao colocacionar no mesmo nó, a memória partilhada pode ser usada para o melhor desempenho. Isto requer que utilize as capacidades de memória partilhadas do Linux expostas pela linguagem/ambiente de programação.

1. Abra o cabo de memória partilhada Linux.
1. Ao receber uma moldura, aceda ao endereço offset dentro da memória partilhada.
1. Reconheça a conclusão do processamento do quadro para que a sua memória possa ser recuperada pela Live Video Analytics.

## <a name="create-a-grpc-inference-server"></a>Criar um servidor de inferência gRPC

Agora você vai construir um módulo IoT Edge (AI Externo) que aceita quadros de vídeo do Live Video Analytics usando mensagens [de protobof](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) através de memória partilhada, classificar os quadros como "escuro" ou "claro" e devolver os resultados da inferência ao IoT Hub Message Sink in Live Video Analytics usando o [esquema de metadados de inferência](inference-metadata-schema.md).

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="construir um módulo IoT Edge (IA Externa)":::

Este servidor de inferência gRPC é uma aplicação de consola .NET Core construída para lidar com as mensagens [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) enviadas entre o Live Video Analytics e a sua IA personalizada. Segue-se o fluxo de mensagens entre o Live Video Analytics e o servidor de inferência gRPC:

1. Live Video Analytics envia um descritor de stream (ver [extension.proto)](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)que define as informações de fluxo de mídia que serão enviadas seguidas por quadros de vídeo para o servidor como uma mensagem [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) sobre a sessão de streaming gRPC. 
1. O servidor valida e reconhece o descritor de fluxo e configura o método de transferência de dados pretendido.
1. Live Video Analytics começa então a enviar os ficheiros MediaSample que contêm as molduras de vídeo.
1. O servidor analisa os quadros de vídeo à medida que recebe e começa a processá-los utilizando um Processador de Imagem definido por si.
1. Em seguida, o servidor retorna os resultados da inferência como mensagens [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) assim que estiverem disponíveis. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="Criar um servidor de inferência gRPC":::

Os quadros de vídeo podem ser transferidos através de [memória partilhada](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) ou podem ser incorporados na mensagem protobuf. O modo de transferência de dados pode ser configurado na topologia do gráfico LVA para determinar como os quadros serão transferidos. Isto é conseguido configurando o elemento **dataTransfer** da propriedade MediaGraphGrpcExtension como mostrado abaixo:

Incorporado:

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Memória partilhada:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Ao comunicar sobre memória partilhada, o valor do IpcMode deve ser definido para **compartilhar** e no módulo de servidor gRPC definir o valor do IpcMode para **o recipiente:{CONTAINER_NAME}**. Estas definições devem ser feitas no ficheiro manifesto de implantação que é utilizado para a implantação dos módulos no Azure IoT Hub. Abaixo está uma amostra das opções do recipiente a utilizar ao configurar os módulos IoT Edge.

Módulo de análise de vídeo ao vivo:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

módulo de extensão gRPC:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> Certifique-se de que pode aceder à área de memória partilhada de "container:lvaEdge" dentro da retenção de grpcExtension.

## <a name="sample-grpc-server"></a>Servidor gRPC amostra

Para entender os detalhes de como o servidor gRPC é desenvolvido, vamos ver a nossa amostra de código.

1. Clone o repo a partir da ligação [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) GitHub.
1. Lançar VSCode e navegar para a pasta /src/edge/modules/grpcExtension.
1. Vamos fazer uma rápida passagem pelos ficheiros:

    1. **Program.cs:** este é o ponto de entrada da aplicação. É responsável pela inicialização e gestão do servidor gRPC, que funcionará como anfitrião. Na nossa amostra, a porta para ouvir as mensagens gRPC recebidas de um cliente gRPC (como live video analytics) é especificada pelo elemento de configuração grpcBindings no AppConfig.jsligado.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Serviços\MediaGraphExtensionService.cs**: Esta classe é responsável pelo manuseamento das mensagens [protobuf.](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) Lerá o quadro na mensagem, invocará o ImageProcessor e escreverá os resultados da inferência.
Agora que configuramos e iniciaisizamos as ligações da porta do servidor gRPC, vamos ver como podemos processar as mensagens gRPC recebidas.

        * Uma vez estabelecida uma sessão gRPC, a primeira mensagem que o servidor gRPC receberá do cliente (Live Video Analytics) é um MediaStreamDescriptor que é definido no ficheiro [extension.proto.](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * Na implementação do nosso servidor, o método `ProcessMediaStreamDescriptor` validará a propriedade MediaDescriptor do MediaStreamDescriptor para um ficheiro Vídeo e, em seguida, configurará o modo de transferência de dados (que está a usar memória partilhada ou a utilizar o modo de transferência de quadros incorporado) dependendo do que especifica na topologia e no modelo de implementação utilizado. 
        * Ao receber a mensagem e configurar com sucesso o modo de transferência de dados, o servidor gRPC devolve então a mensagem do MediaStreamDescriptor ao cliente como reconhecimento e estabelecendo assim uma ligação entre o servidor e o cliente.    
        * Depois de o Live Video Analytics receber o reconhecimento, começará a transferir o fluxo de mídia para o servidor gRPC. Na implementação do nosso servidor, o método `ProcessMediaStream` processará o MediaStreamMessage que está a chegar. O MediaStreamMessage também é definido no [extension.proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto).

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * Dependendo do valor do loteSize no Appconfig.jsligado, o nosso servidor continuará a receber as mensagens e armazenará os quadros de vídeo numa Lista. Uma vez atingido o limite de loteSize, a função chamará a função ou o ficheiro que processará a imagem. No nosso caso, o método chama um ficheiro chamado BatchImageProcessor.cs
    1. **Processadores\BatchImageProcessor.cs**: Esta classe é responsável pelo processamento da(s) imagem. Usamos um modelo de classificação de imagem nesta amostra. Para cada imagem que será processada, o algoritmo utilizado é o seguinte:

        1. Converta a imagem num conjunto de byte para processamento. Ver método: `GetBytes(Bitmap image)`
        
            O processador de amostra que estamos a usar suporta apenas o quadro de imagem codificado JPG e nenhum como formato pixel. Caso o seu processador personalizado suporte uma codificação e/ou formato diferente, atualize o `IsMediaFormatSupported` método da classe do processador.
        1. Utilizando a [classe ColorMatrix,](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)converta a imagem em escala cinzenta. Consulte o método: `ToGrayScale(Image source)` .
        1. Assim que tivermos a imagem em escala cinzenta, calculamos a média dos bytes de escala cinzenta.
        1. Se o valor médio < 127, então classificamos a imagem como "escura", então vamos classificá-las como "leves" com valor de confiança como 1.0. Consulte o método: `ProcessImage(List<Image> images)` .

    Pode adicionar a sua própria lógica de processador modificando esta classe ou adicionando uma nova classe e implementando o método:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    Uma vez adicionada a nova classe, terá de atualizar o MediaGraphExtensionService.cs para que ele instantaneamente a sua classe e invoque o método ProcessImage nele para executar a sua lógica de processamento. 

## <a name="connect-with-live-video-analytics-module"></a>Conecte-se com o módulo de análise de vídeo ao vivo

Agora que criou o seu módulo de extensão gRPC, vamos agora criar e implementar a topologia de gráficos de mídia.

1. Utilizando o Código do Estúdio Visual, siga [estas instruções](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) para iniciar sinsus no Docker.
1. No Código do Estúdio Visual, vá para src/edge. Você vê o seu ficheiro .env e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda. Inclui alguns valores de espaço reservado. O ficheiro .env inclui os valores para essas variáveis.
    
    Em seguida, selecione Build and Push IoT Edge Solution. Utilize src/edge/deployment.grpc.template.jspara este passo.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Conecte-se com o módulo de análise de vídeo ao vivo":::
    
    Esta ação constrói o módulo do servidor GRPC e empurra a imagem para o registo do seu contentor Azure.
    Verifique se tem as variáveis ambientais CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr definidas no ficheiro .env.
1. Aceda à pasta src/cloud-to-device-app. Aqui vê a sua appsettings.jsno ficheiro e em alguns outros ficheiros:

    * c2d-console-app.csproj - O ficheiro do projeto para Visual Studio Code.
    * operations.jsem - Uma lista das operações que quer que o programa seja executado.
    * Program.cs - O código do programa de amostra. Este código:

        * Carrega as definições da aplicação.
        * Invoca métodos diretos que o live video analytics no módulo IoT Edge expõe. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](direct-methods.md)
        * Pausas para que possa examinar a saída do programa na janela TERMINAL e examinar os eventos que foram gerados pelo módulo na janela OUTPUT.
        * Invoca métodos diretos para limpar recursos.
1. Editar o operations.jsno ficheiro:

    * Altere a ligação para a topologia do gráfico:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * Em GraphInstanceSet, edite o nome da topologia do gráfico para corresponder ao valor no link anterior:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * Sob a GraphTopologyDelete, edite o nome:<br/>`"name": "InferencingWithGrpcExtension"`

            A topologia (por exemplo, `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json` ) deve definir um endereço de extensão:
    * Parâmetro de endereço de extensão

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Configuração

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implementar o manifesto de implantação IoT Edge

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda e as definições de configuração desses módulos. Siga estes passos para gerar um manifesto a partir do ficheiro do modelo e, em seguida, desloque-o para o dispositivo de borda.
Este passo cria o manifesto de implantação IoT Edge em src/edge/config/deployment.grpc.amd64.jsligado. Clique com o botão direito nesse ficheiro e selecione **Criar Implementação para um único dispositivo**.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="Gerar e implementar o manifesto de implantação IoT Edge":::

Em seguida, o Código do Estúdio Visual pede-lhe para selecionar um dispositivo IoT Hub. Selecione o seu dispositivo IoT Edge, que deve ser o dispositivo de amostra de Lva.
Nesta fase, iniciou-se a implantação de módulos de borda para o seu dispositivo IoT Edge. Em cerca de 30 segundos, refresque o Azure IoT Hub na secção inferior esquerda no Código do Estúdio Visual. Devias ver que um novo módulo foi implantado chamado LvaExtension.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="Um novo módulo foi implantado chamado LvaExtension":::

## <a name="next-steps"></a>Próximos passos

Acompanhe o **Prepare-se para monitorizar os passos mencionados** no vídeo ao vivo da [Análise com o seu modelo](use-your-model-quickstart.md) para executar a amostra e interpretar os resultados. Confira também as nossas topologias gRPC da amostra: [gRPCExtension,](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json) [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension, e [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).