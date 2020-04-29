---
title: Como depurar UDFs - Azure Digital Twins Microsoft Docs
description: Saiba mais sobre as abordagens recomendadas para depurar funções definidas pelo utilizador em Gémeos Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76511642"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Como depurar funções definidas pelo utilizador em Gémeos Digitais Azure

Este artigo resume como diagnosticar e depurar funções definidas pelo utilizador em Gémeos Digitais Azure. Depois, identifica alguns dos cenários mais comuns encontrados ao depura-los.

>[!TIP]
> Leia [Como configurar a monitorização e](./how-to-configure-monitoring.md) o registo de registos para saber mais sobre a criação de ferramentas de depuração em Gémeos Digitais Azure utilizando Registos de Atividade, Registos de Diagnóstico e Monitor Azure.

## <a name="debug-issues"></a>Problemas de depuração

Saber diagnosticar problemas dentro das Gémeas Digitais Azure permite-lhe analisar eficazmente os problemas, identificar as causas dos problemas e fornecer-lhes soluções adequadas.

Para o efeito, são fornecidas uma variedade de ferramentas de exploração madeireira, análise e diagnóstico.

### <a name="enable-logging-for-your-instance"></a>Ativar a exploração madeireira por exemplo

A Azure Digital Twins suporta a exploração madeireira, monitorização e análise robustas. Os desenvolvedores de soluções podem utilizar registos do Monitor Azure, registos de diagnóstico, registos de atividade e outros serviços para suportar as complexas necessidades de monitorização de uma aplicação IoT. As opções de exploração madeireira podem ser combinadas para consultar ou exibir registos em vários serviços e fornecer cobertura de registo granular para muitos serviços.

* Para registar uma configuração específica para as Gémeas Digitais Azure, leia [como configurar a monitorização e a exploração madeireira](./how-to-configure-monitoring.md).
* Consulte a visão geral do [Monitor Azure](../azure-monitor/overview.md) para saber sobre as configurações de registo poderosas ativadas através do Monitor Azure.
* Reveja o artigo [Recolher e consumir dados de registo dos seus recursos Azure](../azure-monitor/platform/platform-logs-overview.md) para configurar definições de registo de diagnóstico em Gémeos Digitais Azure através do portal Azure, Azure CLI ou PowerShell.

Uma vez configurado, poderá selecionar todas as categorias de registo, métricas e utilizar poderosos espaços de trabalho de análise de log Monitor Azure monitor para suportar os seus esforços de depuração.

### <a name="trace-sensor-telemetry"></a>Telemetria do sensor de vestígios

Para rastrear a telemetria do sensor, verifique se as definições de diagnóstico estão ativadas para a sua instância De Gémeos Digitais Azure. Em seguida, certifique-se de que todas as categorias de registo desejadas são selecionadas. Por último, confirme que os registos desejados estão a ser enviados para os registos do Monitor Azure.

Para combinar uma mensagem de telemetria do sensor com os respetivos registos, pode especificar um ID da Correlação nos dados do evento que estão a ser enviados. Para tal, coloque `x-ms-client-request-id` a propriedade num GUID.

Depois de enviar telemetria, abra a análise de registo do Monitor Azure para consultar os registos utilizando o id de correlação definido:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valor de consulta | Substituir |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | O ID da Correlação especificado nos dados do evento |

Para ler todas as consultas recentes de registos de telemetria:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Se ativar o registo da função definida pelo utilizador, esses `UserDefinedFunction`registos aparecem na sua instância de análise de registo com a categoria . Para recuperá-los, insira a seguinte condição de consulta na análise de registo:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Para mais informações sobre operações de consulta poderosas, leia [Começar com consultas](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identificar questões comuns

Tanto diagnosticar como identificar questões comuns são importantes para resolver problemas na sua solução. Várias questões que são comumente encontradas no desenvolvimento de funções definidas pelo utilizador são resumidas nas seguintes subsecções.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Verifique se foi criada uma atribuição de funções

Sem uma atribuição de funções criada dentro da API de Gestão, a função definida pelo utilizador não tem acesso a quaisquer ações como o envio de notificações, a recuperação de metadados e a definição de valores computacionais dentro da topologia.

Verifique se existe uma atribuição de funções para a sua função definida pelo utilizador através da Sua API de Gestão:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Valor do parâmetro | Substituir |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | A identificação da função definida pelo utilizador para recuperar atribuições de funções para|

Saiba como criar uma atribuição de [funções para a sua função definida pelo utilizador,](./how-to-user-defined-functions.md)caso não existam atribuições de funções.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Verifique se o matcher trabalha para a telemetria de um sensor

Com a seguinte chamada contra a API de Gestão de Gémeos Digitais Azure, é possível determinar se um dado matcher se aplica ao sensor dado.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | A identificação do mais compatível que deseja avaliar |
| *YOUR_SENSOR_IDENTIFIER* | A identificação do sensor que pretende avaliar |

Resposta:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Verifique o que um sensor dispara

Com a seguinte chamada contra as APIs de Gestão de Gémeos Digitais Azure, é possível determinar os identificadores das suas funções definidas pelo utilizador desencadeadas pela telemetria de entrada do sensor dada:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | A identificação do sensor para enviar telemetria |

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

### <a name="issue-with-receiving-notifications"></a>Problema com notificações de receção

Quando não estiver a receber notificações da função definida pelo utilizador, confirme que o parâmetro do tipo de objeto de topodeologia corresponde ao tipo de identificador que está a ser utilizado.

**Incorreto** Exemplo:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Este cenário surge porque o identificador usado refere-se a um `Space`sensor enquanto o tipo de objeto de topológico especificado é .

**Correto** Exemplo:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

A maneira mais fácil de não se `Notify` deparar com este problema é usar o método no objeto de metadados.

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

## <a name="common-diagnostic-exceptions"></a>Exceções de diagnóstico comuns

Se ativar as definições de diagnóstico, poderá encontrar estas exceções comuns:

1. **Estrangulamento:** se a sua função definida pelo utilizador exceder os limites de taxa de execução descritos no artigo limites de [serviço,](./concepts-service-limits.md) será estrangulado. Nenhuma outra operação é executada com sucesso até que os limites de estrangulamento expirem.

1. **Dados Não Encontrados**: se a função definida pelo utilizador tentar aceder a metadados que não existam, a operação falha.

1. **Não Autorizado**: se a sua função definida pelo utilizador não tiver um conjunto de tarefas ou não tiver autorização suficiente para aceder a certos metadados a partir da topologia, a operação falha.

## <a name="next-steps"></a>Passos seguintes

- Saiba como ativar [a monitorização e os registos](./how-to-configure-monitoring.md) em Gémeos Digitais Azure.

- Leia o artigo [de registo da Atividade Azure](../azure-monitor/platform/platform-logs-overview.md) para mais opções de exploração madeireira Azure.
