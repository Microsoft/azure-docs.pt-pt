---
title: Como criar funções definidas pelo utilizador - em Gémeos Digitais Azure [ Microsoft Docs
description: Como criar funções definidas pelo utilizador, matchers e atribuições de papéis em Gémeos Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276927"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Como criar funções definidas pelo utilizador em Gémeos Digitais Azure

[As funções definidas pelo utilizador](./concepts-user-defined-functions.md) permitem aos utilizadores configurar a lógica personalizada a ser executada a partir de mensagens de telemetria e metadados de gráficos espaciais. Os utilizadores também podem enviar eventos para [pontos finais predefinidos.](./how-to-egress-endpoints.md)

Este guia percorre um exemplo que demonstra como detetar e alertar para qualquer leitura que exceda uma certa temperatura dos eventos de temperatura recebidos.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Referência da biblioteca do cliente

As funções disponíveis como métodos auxiliares nas funções definidas pelo utilizador estão listadas no documento de referência da [biblioteca do cliente.](./reference-user-defined-functions-client-library.md)

## <a name="create-a-matcher"></a>Criar um matcher

Os matchers são objetos gráficos que determinam quais as funções definidas pelo utilizador para uma determinada mensagem de telemetria.

- Comparações de condições compatíveis válidas:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Alvos válidos de condição de fósforo:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

O seguinte exemplo matcher avalia para verdade em `"Temperature"` qualquer evento de telemetria de sensores com como o seu valor tipo de dados. Pode criar vários matchers numa função definida pelo utilizador, fazendo um pedido auferido http POST para:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

Com o corpo jSON:

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
| YOUR_SPACE_IDENTIFIER | Qual região de servidor a sua instância está hospedada em |

## <a name="create-a-user-defined-function"></a>Criar uma função definida Pelo utilizador

Criar uma função definida pelo utilizador envolve fazer um pedido http multipart para as APIs de Gestão de Gémeos Digitais Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Após a criação das partidares, faça o upload do corte de funções com o seguinte pedido de http post multipart autenticado para:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Utilize o seguinte corpo:

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
| USER_DEFINED_BOUNDARY | Um nome de limite de conteúdo multiparte |
| YOUR_SPACE_IDENTIFIER | O identificador de espaço  |
| YOUR_MATCHER_IDENTIFIER | A identificação do mais compatível que quer usar |

1. Verifique se os cabeçalhos incluem: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Verifique se o corpo é multiparte:

   - A primeira parte contém os metadados de função definidos pelo utilizador.
   - A segunda parte contém a lógica computacional JavaScript.

1. Na secção **USER_DEFINED_BOUNDARY,** substitua`YOUR_SPACE_IDENTIFIER`os valores`YOUR_MATCHER_IDENTIFIER` **spaceId** e **matchers.**
1. Verifique se a função definida pelo `Content-Type: text/javascript`utilizador JavaScript é fornecida como .

### <a name="example-functions"></a>Funções de exemplo

Detete a leitura da telemetria do sensor diretamente para o sensor com o tipo de dados **Temperatura,** que é: `sensor.DataType`

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

O parâmetro de **telemetria** expõe os atributos **SensorId** e **Message,** correspondentes a uma mensagem enviada por um sensor. O parâmetro **de execuçãoContext** expõe os seguintes atributos:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

No exemplo seguinte, registamos uma mensagem se a leitura da telemetria do sensor ultrapassar um limiar predefinido. Se as suas definições de diagnóstico estiverem ativadas na instância Das Gémeas Digitais Azure, os registos das funções definidas pelo utilizador também são reencaminhados:

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

O seguinte código desencadeia uma notificação se o nível de temperatura subir acima da constante predefinida:

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

Para obter uma amostra de código de função mais complexa definida pelo utilizador, leia o [quickstart](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)da Ocupação .

## <a name="create-a-role-assignment"></a>Criar uma atribuição de funções

Crie uma atribuição de função para a função definida pelo utilizador para ser executada. Se não existir nenhuma atribuição de funções para a função definida pelo utilizador, não terá as permissões adequadas para interagir com a API de Gestão ou ter acesso a ações em objetos gráficos. As ações que uma função definida pelo utilizador pode desempenhar são especificadas e definidas através do controlo de acesso baseado em funções dentro das APIs de Gestão de Gémeos Digitais Azure. Por exemplo, as funções definidas pelo utilizador podem ser limitadas no âmbito, especificando determinadas funções ou determinadas vias de controlo de acesso. Para mais informações, leia a documentação [de controlo de acesso baseado em Role.](./security-role-based-access-control.md)

1. [Consulta da API do Sistema](./security-create-manage-role-assignments.md#retrieve-all-roles) para todas as funções obter o ID de função que pretende atribuir à sua função definida pelo utilizador. Faça-o fazendo um pedido auferido HTTP GET para:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Mantenha a identificação do papel desejado. Será passado como o corpo JSON atribui`YOUR_DESIRED_ROLE_IDENTIFIER` **roleId** ( ) abaixo.

1. **objectId** `YOUR_USER_DEFINED_FUNCTION_ID`() será o ID de função definido pelo utilizador que foi criado anteriormente.
1. Encontre o valor`YOUR_ACCESS_CONTROL_PATH`do **caminho,** consultando `fullpath`os seus espaços com .
1. Copie `spacePaths` o valor devolvido. Vais usá-lo abaixo. Faça um pedido auferido HTTP GET para:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_SPACE_NAME | O nome do espaço que deseja usar |

1. Colar o `spacePaths` valor devolvido no **caminho** para criar uma atribuição de função definida pelo utilizador, fazendo um pedido auferido http POST para:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Com o corpo jSON:

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
    | YOUR_DESIRED_ROLE_IDENTIFIER | O identificador para o papel desejado |
    | YOUR_USER_DEFINED_FUNCTION_ID | O ID para a função definida pelo utilizador que pretende utilizar |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | O ID especificando o tipo`UserDefinedFunctionId`de função definido pelo utilizador () |
    | YOUR_ACCESS_CONTROL_PATH | A via de controlo de acesso |

>[!TIP]
> Leia o artigo [Como criar e gerir atribuições](./security-create-manage-role-assignments.md) de funções para mais informações sobre operações e pontos finais de gestão de funções definidas pelo utilizador.

## <a name="send-telemetry-to-be-processed"></a>Enviar telemetria para ser processado

O sensor definido no gráfico de inteligência espacial envia telemetria. Por sua vez, a telemetria desencadeia a execução da função definida pelo utilizador que foi carregada. O processador de dados capta a telemetria. Em seguida, é criado um plano de execução para a invocação da função definida pelo utilizador.

1. Recupere as fósforos para o sensor de onde a leitura foi gerada.
1. Dependendo do que os matchers foram avaliados com sucesso, recupere as funções definidas pelo utilizador associados.
1. Executar cada função definida pelo utilizador.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar pontos finais](./how-to-egress-endpoints.md) da Azure Digital Twins para enviar eventos.

- Para mais detalhes sobre o encaminhamento em Azure Digital Twins, leia [eventos e mensagens de encaminhamento.](./concepts-events-routing.md)

- Reveja a documentação de referência da [biblioteca do cliente.](./reference-user-defined-functions-client-library.md)