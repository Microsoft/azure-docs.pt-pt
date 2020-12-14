---
title: Protocolo de extensão HTTP - Azure
description: Neste artigo, você aprenderá sobre a utilização do protocolo de extensão HTTP para enviar mensagens entre o módulo Live Video Analytics e o seu módulo de IA ou CV.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 52c98231780a2776f4ff67992f29b247eccb8bc2
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399150"
---
# <a name="http-extension-protocol"></a>Protocolo de extensão HTTP

O Live Video Analytics on IoT Edge permite-lhe alargar as capacidades de processamento de gráficos de mídia através de um [nó de extensão de gráfico](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/media-graph-extension-concept?branch=release-lva-dec-update). Se utilizar o processador de extensão HTTP como nó de extensão, então a comunicação entre o módulo Live Video Analytics e o seu módulo de IA ou CV é mais http HTTP

Neste artigo, você aprenderá sobre a utilização do protocolo de extensão HTTP para enviar mensagens entre o módulo Live Video Analytics e o seu módulo de IA ou CV. 

O contrato HTTP é definido entre os dois componentes seguintes:

* Servidor HTTP
* Vídeo ao vivo Analytics no módulo IoT Edge atua como cliente HTTP

## <a name="request"></a>Pedir

Os pedidos do módulo Live Video Analytics para o seu servidor HTTP seriam os seguintes:

|Chave|Valor|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Aceitar|aplicação/json,  */*|
|Autorização|Básico, Digest, Portador (através de suporte personalizado de cabeçalho)|
|Content-Type|imagem/jpeg<br/>imagem/png<br/>imagem/bmp<br/>imagem/x-cru|
|Comprimento do comprimento do conteúdo comprimento do corpo, em bytes|
|User-Agent|Serviços de Multimédia do Azure|
|Corpo|Bytes de imagem, binário codificados num dos tipos de conteúdo suportado.|

### <a name="example"></a>Exemplo

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>Resposta

As respostas do módulo para o módulo Live Video Analytics devem ser as seguintes:

|Chave|Valor|
|---|---|
|Código de Estado|200 OK - Resultados de inferência encontrados<br/>204 No Content - Nenhum conteúdo encontrado pela IA<br/>400 Mau Pedido - Não esperado<br/>500 Erro interno do Servidor - Não esperado<br/>503 Server Busy - AMS irá recuar com base no cabeçalho "Retry-After" ou baseado numa quantidade padrão de tempo no caso de o cabeçalho não estar predefinido.|
|Content-Type|application/json|
|Comprimento do conteúdo|Comprimento do corpo, em bytes|
|Corpo|Objeto JSON com propriedade única de "inferências".|

### <a name="example"></a>Exemplo

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

Recomenda-se vivamente que as respostas sejam devolvidas utilizando documentos JSON válidos seguindo o esquema pré-estabelecido definido de acordo com o modelo de [objeto de esquema de metadados de inferência](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/inference-metadata-schema?branch=release-lva-dec-update). Isto garantirá melhor a interoperabilidade com outros componentes e possíveis capacidades futuras adicionadas ao módulo Live Video Analytics.

Se o seu módulo devolver uma resposta em que o tipo de conteúdo não é "aplicação/json", o Live Video Analytics codificará a mensagem como um conteúdo base 64 e serializará-a como uma carga útil opaca do JSON.

Se o seu módulo devolver uma resposta com o tipo de conteúdo como "aplicação/json" mas o esquema JSON não seguir o esquema de metadados de inferência descrito abaixo, a carga útil da mensagem será reencaminhada através do pipeline, mas a interoperabilidade será reduzida. Consulte [aqui](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/inference-metadata-schema?branch=release-lva-dec-update) para obter informações detalhadas e atualizadas sobre o esquema de metadados de inferência.

> [!NOTE]
> Se o seu módulo não produzir qualquer resultado, deverá devolver HTTP 204 Status Code (Sem Conteúdo) com um corpo de resposta vazio. O Live Video Analytics compreenderá isto como um resultado vazio e não irá encaminhar o evento para todo o oleoduto.


## <a name="next-steps"></a>Passos seguintes

[Protocolo de extensão gRPC](./grpc-extension-protocol.md)
