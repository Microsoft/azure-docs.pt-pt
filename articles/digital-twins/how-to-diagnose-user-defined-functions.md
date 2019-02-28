---
title: Como depurar UDFs no duplos Digital do Azure | Documentos da Microsoft
description: Orientação sobre como depurar UDFs no duplos Digital do Azure.
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: 6122cd4507ed0883d1b78ca519269c25098e55ff
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961419"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Como depurar funções definidas pelo utilizador no duplos Digital do Azure

Este artigo resume como diagnosticar e depurar funções definidas pelo utilizador. Em seguida, ele identifica alguns dos cenários mais comuns encontrados quando depurá-los.

>[!TIP]
> Leia [como configurar a monitorização e registo](./how-to-configure-monitoring.md) para saber mais sobre como configurar as ferramentas no gémeos de Digital do Azure através de registos de atividades, registos de diagnóstico e o Azure Monitor de depuração.

## <a name="debug-issues"></a>Depurar problemas

Saber como diagnosticar quaisquer problemas que possam surgir dentro de sua instância de duplos Digital do Azure auxilia efetivamente identificar o problema, a causa do problema e uma solução.

### <a name="enable-log-analytics-for-your-instance"></a>Ativar a análise de registo para a sua instância

Os registos e métricas para a sua instância de duplos Digital do Azure são apresentadas no Azure Monitor. Esta documentação pressupõe que criou um [registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md) área de trabalho por meio dos [Portal do Azure](../azure-monitor/learn/quick-create-workspace.md), da funcionalidade [da CLI do Azure](../azure-monitor/learn/quick-create-workspace-cli.md), ou através de [ PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> Poderá haver um atraso de 5 minutos, ao enviar eventos para os registos do Azure Monitor pela primeira vez.

Para configurar a monitorização e registo de recursos de duplos Digital do Azure, leia [como configurar a monitorização e registo](./how-to-configure-monitoring.md).

Leia o artigo [recolher e consumir dados de registo dos seus recursos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md) para configurar definições de registo de diagnóstico num duplos Digital do Azure através do Portal do Azure, CLI do Azure ou do PowerShell.

>[!IMPORTANT]
> Certifique-se selecionar todas as categorias de registo, métricas e sua área de trabalho do Log Analytics do Azure.

### <a name="trace-sensor-telemetry"></a>Telemetria de sensores de rastreio

A telemetria de sensores de rastreio, certifique-se de que as definições de diagnóstico estão ativadas para a sua instância de duplos Digital do Azure. Em seguida, certifique-se de que pretendido todas as categorias de registo estão selecionadas. Por fim, confirme que os registos pretendidos estão a ser enviados para os registos do Azure Monitor.

Para fazer corresponder uma mensagem de telemetria de sensor para seus respectivos registos, pode especificar um ID de correlação nos dados de evento a ser enviados. Para tal, defina o `x-ms-client-request-id` propriedade para um GUID.

Depois de enviar telemetria, abra o log Analytics para consultar os registos com o conjunto de ID de correlação:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valor de consulta | Substituir |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | O ID de correlação que foi especificada nos dados de eventos |

Se ativar o registo para a função definida pelo usuário, esses registos são apresentados na sua instância de análise de registo com a categoria `UserDefinedFunction`. Para obtê-las, introduza a seguinte condição de consulta do log analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Para obter mais informações sobre as operações de consulta poderosa, leia [introdução às consultas](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identificar problemas comuns

Diagnóstico e a identificação de problemas comuns são importantes quando a sua solução de resolução de problemas. Vários problemas que normalmente são encontrados ao desenvolver funções definidas pelo utilizador estão resumidos nas subsecções seguintes.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Verifique se uma atribuição de função foi criada

Sem uma atribuição de função criada dentro da API de gestão, a função definida pelo utilizador não tem acesso a executar quaisquer ações como enviar notificações, obtenção de metadados, e calculada de definição de valores na topologia.

Verificar se uma atribuição de função que existe para a função definida pelo utilizador através da API de gestão:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Valor do parâmetro | Substituir |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | O ID da função definida pelo utilizador a obter atribuições de funções para|

Saiba mais [como criar uma atribuição de função para a função definida pelo utilizador](./how-to-user-defined-functions.md), se existirem não existem atribuições de função.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Verifique se na ferramenta de correspondência funciona para a telemetria de um sensor

Com a seguinte chamada em relação a API de gestão dos suas instâncias gémeos Digital do Azure, pode determinar se um determinado na ferramenta de correspondência aplica-se para o sensor de determinado.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | O ID da na ferramenta de correspondência que deseja avaliar |
| *YOUR_SENSOR_IDENTIFIER* | O ID de sensor que deseja avaliar |

Resposta:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Verifique o que dispara um sensor

Com a seguinte chamada contra as APIs de gestão de duplos Digital do Azure, pode determinar os identificadores das suas funções definidas pelo utilizador acionadas por telemetria de entrada do sensor determinado:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | O ID do sensor para enviar telemetria |

Resposta:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problema com a receção das notificações

Quando não está a receber notificações da função definida pelo utilizador acionada, confirme que o parâmetro de tipo de objeto de topologia corresponde ao tipo de identificador, que está a ser utilizado.

**Incorreto** exemplo:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Este cenário surge porque o identificador utilizado refere-se a um sensor enquanto o tipo de objeto de topologia especificado é `Space`.

**Correto** exemplo:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

A maneira mais fácil para não executar para este problema é usar o `Notify` método no objeto de metadados.

Exemplo:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Exceções comuns de diagnóstico

Se ativar as definições de diagnóstico, pode encontrar essas exceções comuns:

1. **Limitação**: se a sua função definida pelo utilizador excede a taxa de execução limites descritos na [limites do serviço](./concepts-service-limits.md) artigo, serão limitado. Não existem mais operações são executadas com êxito até expirarem os limites de limitação.

1. **Dados não encontrados**: se a função definida pelo utilizador tenta aceder a metadados que não existe, a falha da operação.

1. **Não autorizado**: se a sua função definida pelo utilizador não tiver uma atribuição de função definido ou não tem permissões suficientes para aceder a determinadas metadados da topologia de, a operação falhar.

## <a name="next-steps"></a>Passos Seguintes

- Aprenda a ativar [registos e monitorização](../azure-monitor/platform/activity-logs-overview.md) no duplos Digital do Azure.
