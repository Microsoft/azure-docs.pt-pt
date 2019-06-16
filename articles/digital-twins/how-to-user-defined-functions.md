---
title: Como criar funções definidas pelo utilizador no duplos Digital do Azure | Documentos da Microsoft
description: Como criar funções definidas pelo utilizador, matchers e atribuições de funções no duplos Digital do Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: b5e230f45fd5889e216f2993f58adf6940ef7679
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072901"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Como criar funções definidas pelo utilizador no duplos Digital do Azure

[Funções definidas pelo utilizador](./concepts-user-defined-functions.md) permitir que os utilizadores configurar a lógica personalizada para ser executado a partir de mensagens de telemetria de entrada e os metadados de gráfico espaciais. Os utilizadores podem também enviar eventos para predefinidos [pontos de extremidade](./how-to-egress-endpoints.md).

Este guia explica um exemplo que demonstram como detetar e alerta sobre qualquer leitura que excede uma determinada temperatura dos eventos recebidos de temperatura.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Referência da biblioteca de cliente

As funções disponíveis como métodos auxiliares no tempo de execução de funções definidas pelo utilizador são listadas no [referência da biblioteca de cliente](./reference-user-defined-functions-client-library.md) documento.

## <a name="create-a-matcher"></a>Criar na ferramenta de correspondência

Matchers são objetos de gráfico que determinam o que executar funções definidas pelo utilizador para uma mensagem de telemetria de determinado.

- Comparações de condição de válido na ferramenta de correspondência:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Destinos de condição de válido na ferramenta de correspondência:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

A seguinte na ferramenta de correspondência do exemplo é avaliada como true em qualquer evento de telemetria de sensores com `"Temperature"` como seu valor de tipo de dados. Pode criar vários matchers numa função definida pelo utilizador ao fazer um pedido de HTTP POST autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

Com o corpo JSON:

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Value | Substituir |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Qual sua instância estiver alojada num servidor a região |

## <a name="create-a-user-defined-function"></a>Criar uma função definida Pelo utilizador

A criação de uma função definida pelo utilizador envolve fazer uma solicitação HTTP com várias partes das APIs de gestão de duplos Digital do Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Depois dos matchers são criados, carregar o trecho de código de função com o seguinte autenticado com várias partes pedido POST de HTTP para:

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Utilize o corpo do seguinte:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Value | Substituir |
| --- | --- |
| USER_DEFINED_BOUNDARY | Um nome de limites com várias partes de conteúdo |
| YOUR_SPACE_IDENTIFIER | O identificador de espaço  |
| YOUR_MATCHER_IDENTIFIER | O ID da na ferramenta de correspondência que pretende utilizar |

1. Certifique-se de que os cabeçalhos incluem: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Certifique-se de que o corpo é com várias partes:

   - A primeira parte contém os metadados necessários função definida pelo utilizador.
   - A segunda parte contém a lógica de computação do JavaScript.

1. Na **USER_DEFINED_BOUNDARY** secção, substitua a **spaceId** (`YOUR_SPACE_IDENTIFIER`) e **matchers** (`YOUR_MATCHER_IDENTIFIER`) valores.
1. Certifique-se de que a função definida pelo utilizador do JavaScript é fornecida como `Content-Type: text/javascript`.

### <a name="example-functions"></a>Funções de exemplo

Definir a telemetria de sensores diretamente para o sensor de leitura com o tipo de dados **temperatura**, que é `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

O **telemetria** parâmetro expõe a **SensorId** e **mensagem** atributos, correspondente a uma mensagem enviada por um sensor. O **executionContext** parâmetro expõe os seguintes atributos:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

No exemplo seguinte, vamos registar uma mensagem se a leitura de telemetria de sensores excede um limiar predefinido. Se as definições de diagnóstico estão ativadas na instância duplos Digital do Azure, também são reencaminhados a registos de funções definidas pelo utilizador:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

O código a seguir aciona uma notificação se o nível de temperatura fica acima da constante predefinida:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Para obter um exemplo de código de função definida pelo utilizador mais complexo, consulte a [guia de introdução de ocupação](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função

Crie uma atribuição de função para a função definida pelo utilizador ser executado sob. Se nenhuma atribuição de função para a função definida pelo usuário, ele não terá as permissões adequadas para interagir com a API de gestão ou ter acesso para efetuar ações em objetos de gráfico. Ações que pode executar uma função definida pelo utilizador são especificadas e definidas por meio de controlo de acesso baseado em funções dentro das APIs de gestão de duplos Digital do Azure. Por exemplo, funções definidas pelo usuário podem ter escopo limitadas ao especificar determinadas funções ou determinados caminhos de controle de acesso. Para obter mais informações, consulte a [controlo de acesso baseado em funções](./security-role-based-access-control.md) documentação.

1. [Consultar a API de sistema](./security-create-manage-role-assignments.md#all) para todas as funções obter o ID de função que pretende atribuir à sua função definida pelo utilizador. Fazê-lo ao fazer um pedido HTTP GET autenticado para:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Manter o ID de função desejada. Ela será transmitida como o atributo de corpo JSON **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) abaixo.

1. **objectId** (`YOUR_USER_DEFINED_FUNCTION_ID`) será o ID de função definida pelo utilizador que foi criado anteriormente.
1. Encontrar o valor de **caminho** (`YOUR_ACCESS_CONTROL_PATH`) ao consultar seus espaços com `fullpath`.
1. Copiar retornado `spacePaths` valor. Usará que abaixo. Fazer um pedido de HTTP GET autenticado para:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Value | Substituir |
    | --- | --- |
    | YOUR_SPACE_NAME | O nome do espaço de que pretende utilizar |

1. Cole retornado `spacePaths` valor para **caminho** para criar uma atribuição de função da função definida pelo utilizador ao fazer um pedido de HTTP POST autenticado para:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Com o corpo JSON:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Value | Substituir |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | O identificador para a função desejada |
    | YOUR_USER_DEFINED_FUNCTION_ID | O ID para a função definida pelo utilizador que pretende utilizar |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | O ID de especificar o tipo de função definida pelo utilizador |
    | YOUR_ACCESS_CONTROL_PATH | O caminho de controlo de acesso |

>[!TIP]
> Leia o artigo [como criar e gerir atribuições de funções](./security-create-manage-role-assignments.md) para obter mais informações sobre pontos finais e as operações de API de gestão da função definida pelo utilizador.

## <a name="send-telemetry-to-be-processed"></a>Enviar telemetria para serem processados

O sensor definido no gráfico intelligence geográficos envia telemetria. Por sua vez, a telemetria aciona a execução da função definida pelo utilizador que foi carregada. O processador dos dados seleciona a telemetria. Em seguida, um plano de execução é criado para a invocação da função definida pelo utilizador.

1. Obter matchers para o leitura foi gerada a partir de sensor.
1. Dependendo de quais matchers foram avaliadas com êxito, obter as funções definidas pelo utilizador associadas.
1. Execute cada função definida pelo utilizador.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar pontos finais de duplos Digital do Azure](./how-to-egress-endpoints.md) para enviar eventos para.

- Para obter mais detalhes sobre o encaminhamento duplos Digital do Azure, leia [mensagens de eventos de roteamento e](./concepts-events-routing.md).

- Reveja os [documentação de referência da biblioteca de cliente](./reference-user-defined-functions-client-library.md).
