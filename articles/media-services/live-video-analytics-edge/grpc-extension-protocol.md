---
title: protocolo de extensão gRPC - Azure
description: Neste artigo, você aprenderá sobre a utilização do protocolo de extensão gRPC para enviar mensagens entre o módulo Live Video Analytics e a extensão personalizada de IA ou CV.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 7f21ff358b8dd5ac540de8c39c37c52e98977e59
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401632"
---
# <a name="grpc-extension-protocol"></a>Protocolo de extensão gRPC

O Live Video Analytics on IoT Edge permite-lhe alargar as capacidades de processamento de gráficos de mídia através de um [nó de extensão de gráfico](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/media-graph-extension-concept?branch=release-lva-dec-update). Se utilizar o processador de extensão gRPC como nó de extensão, então a comunicação entre o módulo Live Video Analytics e o seu módulo AI ou CV é sobre o protocolo estruturado baseado em gRPC e altamente performante.

Neste artigo, você aprenderá sobre a utilização do protocolo de extensão gRPC para enviar mensagens entre o módulo Live Video Analytics e a extensão personalizada de IA ou CV.

GRPC é um moderno quadro RPC de código aberto e de alto desempenho que funciona em qualquer ambiente e suporta plataforma cruzada e comunicação de línguas cruzadas. O serviço de transporte gRPC utiliza o streaming bidirecional HTTP/2 entre:

* o cliente gRPC (Live Video Analytics no módulo IoT Edge) e 
* o servidor gRPC (a sua extensão personalizada).

Uma sessão gRPC é uma única ligação do cliente gRPC ao servidor gRPC sobre a porta TCP/TLS. 

Numa única sessão: O cliente envia um descritor de stream de mídia seguido de quadros de vídeo para o servidor como uma mensagem [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) sobre a sessão de streaming gRPC. O servidor valida o descritor de fluxo, analisa a moldura de vídeo e devolve os resultados da inferência como uma mensagem protobuf. 

Recomenda-se vivamente que as respostas sejam devolvidas utilizando documentos JSON válidos seguindo o esquema pré-estabelecido definido de acordo com o modelo de [objeto de esquema de metadados de inferência](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/inference-metadata-schema?branch=release-lva-dec-update). Isto garantirá melhor a interoperabilidade com outros componentes e possíveis capacidades futuras adicionadas ao módulo Live Video Analytics.

![contrato de extensão gRPC](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>Implementação do protocolo gRPC

### <a name="creating-a-grpc-connection"></a>Criação de uma ligação gRPC

A extensão personalizada deve implementar o seguinte serviço gRPC:

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

Quando chamado, isto abrirá um fluxo biducional para que as mensagens fluam entre a extensão gRPC e o gráfico live video analytics. A primeira mensagem enviada neste stream por cada uma das partes conterá um MediaStreamDescriptor, que define que informações serão enviadas nos seguintes MediaSamples.

Por exemplo, a extensão do gráfico pode enviar a mensagem (expressa aqui em JSON) para indicar que enviará quadros codificados de 416x416 rgb24 incorporados nas mensagens gRPC para a extensão personalizada.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

A extensão personalizada enviaria, em resposta, a seguinte mensagem para indicar que está apenas a devolver inferências.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

Agora que ambos os lados trocaram descritores de meios, o Live Video Analytics começará a transmitir quadros para a extensão.

> [!NOTE]
> A implementação lateral do servidor gRPC pode ser feita na linguagem de programação à sua escolha.
### <a name="sequence-numbers"></a>Número de sequência

Tanto o nó de extensão gRPC como a extensão personalizada mantêm um conjunto separado de números de sequência, que são atribuídos às suas mensagens. Estes números de sequência devem aumentar monotonicamente a partir de 1. `ack_sequence_number` pode ser ignorado se não for reconhecida nenhuma mensagem, o que pode ocorrer quando a primeira mensagem enviada.

Um pedido deve ser reconhecido uma vez que a mensagem correspondente tenha sido totalmente processada. No caso de uma transferência de memória partilhada, este reconhecimento indica que o remetente pode libertar a memória partilhada e que o recetor não irá mais aceder à sua memória. O remetente deve guardar qualquer memória partilhada até que seja reconhecida.

### <a name="reading-embedded-content"></a>Ler conteúdo incorporado

O conteúdo incorporado pode ser lido diretamente fora da `MediaSample` mensagem através `content_byte` do campo s. Os dados serão codificados de acordo com `MediaDescriptor` o .

### <a name="reading-content-from-shared-memory"></a>Ler Conteúdo da Memória Partilhada

Ao transferir conteúdo através da memória partilhada, o remetente incluirá `SharedMemoryBufferTransferProperties` no `MediaStreamDescriptor` seu quando estabelecerem uma sessão pela primeira vez. Isto pode parecer o seguinte (expresso em JSON):

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

Em seguida, o recetor abre o ficheiro `/dev/shm/inference_client_share_memory_2146989006636459346` . O remetente enviará uma `MediaSample` mensagem, que contém um `ContentReference` referimento a um lugar específico neste ficheiro.

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

Em seguida, o recetor lê os dados desta localização no ficheiro.

Para que o recipiente Live Video Analytics comunique sobre a memória partilhada, o modo IPC do recipiente deve ser configurado corretamente. Isto pode ser feito de muitas maneiras, mas aqui estão algumas configurações recomendadas.

* Ao comunicar com um motor de inferencção gRPC que funciona no dispositivo de hospedeiro, o modo IPC deve ser definido para hospedar.
* Ao comunicar com um servidor gRPC em execução noutro módulo IoT Edge, o modo IPC deve ser definido para partilhar para o módulo Live Video Analytics e `container:liveVideoAnalytics` para a extensão personalizada, onde `liveVideoAnalytics` está o nome do módulo Live Video Analytics.

Aqui está o que isto pode parecer no dispositivo twin usando a primeira opção de cima.

```
"liveVideoAnalytics": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "host"
      }
  }
}
```

Para obter mais informações sobre os modos IPC, consulte https://docs.docker.com/engine/reference/run/#ipc-settings---ipc .

## <a name="mediagraph-grpc-extension-contract-definitions"></a>Definições de contrato de extensão MediaGraph gRPC

Esta secção define o contrato gRPC que define o fluxo de dados.

### <a name="protocol-messages"></a>Mensagens de protocolo

![Mensagens de protocolo](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>Autenticação de cliente

Os implementadores de extensões personalizadas podem validar a autenticidade das ligações gRPC de entrada para ter certeza de que são provenientes do nó de extensão gRPC. O nó fornecerá uma entrada nos cabeçalhos de pedido para validar contra.

As credenciais de nome de utilizador/palavra-passe podem ser usadas para o conseguir. Ao criar um nó de extensão gRPC, as credenciais são fornecidas como abaixo:

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

Quando o pedido de gRPC for enviado, o seguinte cabeçalho será incluído nos metadados do pedido, imitando a autenticação básica HTTP.

`x-ms-authentication: Basic (Base64 Encoded username:password)`


## <a name="configuring-inference-server-for-each-mediagraph-over-grpc-extension"></a>Configurar o servidor de inferência para cada MediaGraph sobre extensão gRPC
Ao configurar o seu servidor de inferência, não precisa de expor um nó para cada modelo de IA que esteja embalado dentro do servidor de inferência. Em vez disso, para uma instância de gráfico, você pode usar a `extensionConfiguration` propriedade do nó e definir como selecionar `MediaGraphGrpcExtension` o(s) modelo de IA. Durante a execução, a LVA passará esta cadeia para o servidor de inferenculação que pode usá-lo para invocar o modelo de IA pretendido. Esta `extensionConfiguration` propriedade é uma propriedade opcional e é específica do servidor. A propriedade pode ser usada como abaixo:
```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "5"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>Utilização de gRPC sobre TLS

Uma ligação gRPC utilizada para a inferenção pode ser fixada sobre o TLS. Isto é útil em situações em que a segurança da rede entre o Live Video Analytics e o motor de inferencing não pode ser garantida. O TLS encriptará qualquer conteúdo incorporado nas mensagens gRPC, causando uma sobrecarga adicional de CPU ao transmitir quadros a uma taxa elevada.

As opções de verificação ignoreHostname e IgnoreSignature não são suportadas pelo gRPC, pelo que o certificado do servidor, que o motor de inferenculação apresenta, deve conter um CN que corresponda exatamente ao endereço IP/nome de anfitrião no URL do nó final do nó final do gRPC.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Esquema de Metadados de Inferência](inference-metadata-schema.md)
