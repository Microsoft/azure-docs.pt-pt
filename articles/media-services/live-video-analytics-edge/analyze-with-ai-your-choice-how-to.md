---
title: Analise o vídeo ao vivo com AI à sua escolha - Azure
description: Neste artigo, você vai aprender a construir um módulo IoT Edge que pode ser integrado com Live Video Analytics no IoT Edge para analisar vídeo ao vivo usando um modelo de visão de computador à sua escolha.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0ac2af280eefd5ce293a8be422551d5ee6f6d3f3
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261262"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Analise o vídeo ao vivo com AI à sua escolha

Neste artigo, você vai aprender a construir um módulo IoT Edge que pode ser integrado com Live Video Analytics no IoT Edge para analisar vídeo ao vivo usando um modelo de visão de computador à sua escolha. 

## <a name="pre-reading"></a>Pré-leitura

[Conceito de gráfico de mídia](media-graph-concept.md)

## <a name="media-graph-extension"></a>Extensão de gráfico de mídia

Live Video Analytics on IoT Edge define um modelo de extensibilidade que lhe permite alargar as capacidades de processamento de gráficos de mídia aos seus próprios componentes de análise de mídia através de uma extensão de gráfico. O seu componente de análise pode utilizar técnicas tradicionais de processamento de imagem ou modelos de IA de visão computacional. As extensões de gráfico são ativadas através da inclusão do nó do [processador de extensão HTTP](media-graph-concept.md#http-extension-processor) num gráfico de mídia. O processador de extensão HTTP pode transmitir quadros de vídeo para um ponto final HTTP especificado por si e funciona como interface para o seu componente através disso. A ligação pode ser feita a um ponto final local ou remoto e pode ser protegida por autenticação e encriptação TLS, se necessário. Além disso, o processador permite a dimensionamento e codificação opcionais dos quadros de vídeo antes de serem retransmitidos para a frente.

Pode optar por executar o modelo de inferência à sua escolha em qualquer tempo de inferência disponível, como ONNX, TensorFlow, PyTorch ou outros no seu próprio recipiente de estivador. Também pode utilizar o idioma de programação e as bibliotecas à sua escolha para expor as capacidades de inferição de imagem, embora um servidor HTTP no seu próprio recipiente. O recipiente de inferenção deve ser implantado ao lado do módulo de borda live video analytics para melhor desempenho e será depois invocado através do processador de extensão HTTP incluído na sua topologia de gráfico.
Além disso, a frequência das chamadas para o seu módulo personalizado pode ser acelerada adicionando opcionalmente um [processador de detetor de movimento](media-graph-concept.md#motion-detection-processor) e um processador de filtro de taxa de [fotogramas](media-graph-concept.md#frame-rate-filter-processor) a montante para o processador de extensão HTTP.

O diagrama abaixo retrata o fluxo de dados de alto nível:

![Dispositivo de borda](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

Isto permite-lhe analisar vídeo usando modelos de IA à sua escolha para atender às suas necessidades de negócio únicas. Os modelos de IA podem ser da comunidade de código aberto, ou dos seus próprios cientistas de dados ou de um fornecedor especializado de IA.

## <a name="media-graph-http-extension-contract-definitions"></a>Definições de contrato de extensão HTTP de gráfico de mídia HTTP

Esta secção define o contrato HTTP que define o fluxo de dados.

### <a name="http-extensibility-protocol"></a>Protocolo de extensibilidade HTTP  

O contrato HTTP é definido da seguinte forma:

* O seu módulo funciona como o servidor HTTP.
* Vídeo ao vivo Analytics no módulo IoT Edge funciona como cliente HTTP.

### <a name="request"></a>Pedir

Os pedidos do módulo Live Video Analytics para o seu módulo seriam os seguintes:

|||
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Aceitar|aplicação/json,*/*|
|Autorização| Básico, Digest, Portador (através de suporte personalizado de cabeçalho)|
|Content-Type|imagem/jpeg<br/>imagem/png<br/>imagem/bmp<br/>imagem/x-cru|
|Comprimento do conteúdo|Comprimento do corpo, em bytes|
|User-Agent|Serviços de Multimédia do Azure|
|Corpo|Bytes de imagem, binário codificados num dos tipos de conteúdo suportado.|

#### <a name="example"></a>Exemplo

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Resposta

As respostas do seu módulo para o módulo Live Video Analytics devem ser as seguintes

|||
|---|---|
|Código de Estado|200 OK - Resultados de inferência encontrados<br/>204 No Content - Nenhum conteúdo encontrado pela IA<br/>400 Mau Pedido - Não esperado<br/>500 Erro interno do Servidor - Não esperado<br/>503 Server Busy - AMS irá recuar com base no cabeçalho "Retry-After" ou baseado numa quantidade padrão de tempo no caso de o cabeçalho não estar predefinido.|
|Content-Type|application/json|
|Comprimento do conteúdo|    Comprimento do corpo, em bytes|
|Corpo|Objeto JSON com propriedade única de "inferências".|

#### <a name="example"></a>Exemplo

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Recomenda-se vivamente que as respostas sejam devolvidas utilizando documentos JSON válidos seguindo o esquema pré-estabelecido definido abaixo. Isto garantirá melhor a interoperabilidade com outros componentes e possíveis capacidades futuras adicionadas ao módulo Live Video Analytics.

Se o seu módulo devolver uma resposta em que o tipo de conteúdo não é "aplicação/json", o Live Video Analytics codificará a mensagem como um conteúdo base 64 e serializará-a como uma carga útil opaca do JSON.

Se o seu módulo devolver uma resposta com o tipo de conteúdo como "aplicação/json" mas o esquema JSON não seguir o [esquema de metadados de inferência descrito abaixo,](#inference-metadata-schema)a carga útil da mensagem será reencaminhada através do pipeline, mas a interoperabilidade será reduzida.

> [!NOTE]
> Se o seu módulo não produzir qualquer resultado, deverá devolver HTTP 204 Status Code (Sem Conteúdo) com um corpo de resposta vazio. O Live Video Analytics compreenderá isto como um resultado vazio e não irá encaminhar o evento para todo o oleoduto.

### <a name="inference-metadata-schema"></a>Esquema de metadados de inferência

Cada objeto de Inferência segue este esquema de superconserto:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Contratos de dados - hierarquia de classes

![Contratos de dados - hierarquia de classes](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Exemplos  

O exemplo abaixo contém um único evento com todos os tipos de inferência suportados:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Módulos de extensão HTTP da amostra

Alguns módulos de extensão HTTP de amostra podem ser encontrados no [repo GitHub do Vídeo Ao Vivo.](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) Uma [destas amostras](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) de análise de vídeo mostra como usar o modelo [Yolov3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) para construir um módulo IoT Edge para deteção de objetos. Pode utilizar a mesma abordagem para construir um módulo próprio com um modelo de IA à sua escolha.

## <a name="next-steps"></a>Passos seguintes

[Resolução de problemas](troubleshoot-how-to.md)
