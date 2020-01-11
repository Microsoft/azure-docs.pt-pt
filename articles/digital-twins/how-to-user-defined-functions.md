---
title: Como criar funções definidas pelo usuário – no Azure digital gêmeos | Microsoft Docs
description: Como criar funções definidas pelo usuário, correspondências e atribuições de função no Azure digital gêmeos.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 7334d4292db710a32b888d9a3ad4e78872d15227
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863517"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Como criar funções definidas pelo usuário no Azure digital gêmeos

As [funções definidas pelo usuário](./concepts-user-defined-functions.md) permitem que os usuários configurem a lógica personalizada a ser executada de mensagens de telemetria de entrada e metadados de grafo espaciais. Os usuários também podem enviar eventos para [pontos de extremidade](./how-to-egress-endpoints.md)predefinidos.

Este guia percorre um exemplo que demonstra como detectar e alertar sobre qualquer leitura que exceda uma determinada temperatura dos eventos de temperatura recebidos.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Referência da biblioteca de cliente

As funções disponíveis como métodos auxiliares no tempo de execução de funções definidas pelo usuário são listadas no documento de [referência da biblioteca do cliente](./reference-user-defined-functions-client-library.md) .

## <a name="create-a-matcher"></a>Criar um correspondente

Os correspondências são objetos de grafo que determinam quais funções definidas pelo usuário são executadas para uma determinada mensagem de telemetria.

- Comparações de condição de correspondência válidas:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Destinos de condição de correspondência válidos:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

O correspondente de exemplo a seguir é avaliado como true em qualquer evento de telemetria do sensor com `"Temperature"` como seu valor de tipo de dados. Você pode criar vários correspondentes em uma função definida pelo usuário fazendo uma solicitação HTTP POST autenticada para:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

Com o corpo JSON:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Qual sua instância estiver alojada num servidor a região |

## <a name="create-a-user-defined-function"></a>Criar uma função definida Pelo utilizador

A criação de uma função definida pelo usuário envolve fazer uma solicitação HTTP de várias partes para as APIs de gerenciamento de gêmeos digital do Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Depois que os correspondentes forem criados, carregue o trecho de função com a seguinte solicitação de HTTP POST autenticada de várias partes para:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Use o seguinte corpo:

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

| Valor | Substituir |
| --- | --- |
| USER_DEFINED_BOUNDARY | Um nome de limite de conteúdo com várias partes |
| YOUR_SPACE_IDENTIFIER | O identificador de espaço  |
| YOUR_MATCHER_IDENTIFIER | A ID do correspondente que você deseja usar |

1. Verifique se os cabeçalhos incluem: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Verifique se o corpo é multipart:

   - A primeira parte contém os metadados de função definidos pelo usuário necessários.
   - A segunda parte contém a lógica de computação do JavaScript.

1. Na seção **USER_DEFINED_BOUNDARY** , substitua os valores de **spaceid** (`YOUR_SPACE_IDENTIFIER`) e **correspondentes** (`YOUR_MATCHER_IDENTIFIER`).
1. Verifique se a função definida pelo usuário do JavaScript é fornecida como `Content-Type: text/javascript`.

### <a name="example-functions"></a>Funções de exemplo

Defina a telemetria do sensor lendo diretamente para o sensor com a **temperatura**do tipo de dados, que é `sensor.DataType`:

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

O parâmetro **telemetria** expõe os atributos **sensorid** e **Message** , correspondentes a uma mensagem enviada por um sensor. O parâmetro **ExecutionContext** expõe os seguintes atributos:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

No próximo exemplo, registraremos uma mensagem se a leitura de telemetria do sensor ultrapassar um limite predefinido. Se as configurações de diagnóstico estiverem habilitadas na instância do gêmeos digital do Azure, os logs de funções definidas pelo usuário também serão encaminhados:

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

O código a seguir disparará uma notificação se o nível de temperatura aumentar acima da constante predefinida:

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

Para obter um exemplo de código de função definido pelo usuário mais complexo, leia o guia de [início rápido de ocupação](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função

Crie uma atribuição de função na qual a função definida pelo usuário deve ser executada. Se nenhuma atribuição de função existir para a função definida pelo usuário, ela não terá as permissões apropriadas para interagir com a API de gerenciamento ou terá acesso para executar ações em objetos de grafo. Ações que uma função definida pelo usuário pode executar são especificadas e definidas por meio do controle de acesso baseado em função nas APIs de gerenciamento de gêmeos digital do Azure. Por exemplo, as funções definidas pelo usuário podem ser limitadas no escopo, especificando determinadas funções ou determinados caminhos de controle de acesso. Para obter mais informações, leia a documentação do [controle de acesso baseado em função](./security-role-based-access-control.md) .

1. [Consulte a API do sistema](./security-create-manage-role-assignments.md#retrieve-all-roles) para todas as funções para obter a ID da função que você deseja atribuir à sua função definida pelo usuário. Faça isso fazendo uma solicitação HTTP GET autenticada para:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Mantenha a ID de função desejada. Ele será passado como o atributo de corpo JSON **RoleID** (`YOUR_DESIRED_ROLE_IDENTIFIER`) abaixo.

1. **ObjectID** (`YOUR_USER_DEFINED_FUNCTION_ID`) será a ID da função definida pelo usuário que foi criada anteriormente.
1. Localize o valor de **path** (`YOUR_ACCESS_CONTROL_PATH`) consultando seus espaços com `fullpath`.
1. Copie o valor de `spacePaths` retornado. Você o usará abaixo. Faça uma solicitação HTTP GET autenticada para:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_SPACE_NAME | O nome do espaço que você deseja usar |

1. Cole o valor de `spacePaths` retornado em **path** para criar uma atribuição de função de função definida pelo usuário fazendo uma solicitação HTTP post autenticada para:

    ```URL
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

    | Valor | Substituir |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | O identificador para a função desejada |
    | YOUR_USER_DEFINED_FUNCTION_ID | A ID da função definida pelo usuário que você deseja usar |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | A ID que especifica o tipo de função definido pelo usuário (`UserDefinedFunctionId`) |
    | YOUR_ACCESS_CONTROL_PATH | O caminho de controle de acesso |

>[!TIP]
> Leia o artigo [como criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md) para obter mais informações sobre operações e pontos de extremidade da API de gerenciamento de funções definidas pelo usuário.

## <a name="send-telemetry-to-be-processed"></a>Enviar telemetria a ser processada

O sensor definido no grafo de inteligência espacial envia telemetria. Por sua vez, a telemetria dispara a execução da função definida pelo usuário que foi carregada. O processador de dados pega a telemetria. Em seguida, um plano de execução é criado para a invocação da função definida pelo usuário.

1. Recupere os correspondentes para o sensor do qual a leitura foi gerada.
1. Dependendo de quais correspondentes foram avaliados com êxito, recupere as funções definidas pelo usuário associadas.
1. Execute cada função definida pelo usuário.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar pontos de extremidade do Azure digital gêmeos](./how-to-egress-endpoints.md) para enviar eventos para o.

- Para obter mais detalhes sobre roteamento no Azure digital gêmeos, leia [eventos e mensagens de roteamento](./concepts-events-routing.md).

- Examine a [documentação de referência da biblioteca do cliente](./reference-user-defined-functions-client-library.md).