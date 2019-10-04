---
title: Como depurar UDFs no Azure digital gêmeos | Microsoft Docs
description: Diretrizes sobre como depurar UDFs no gêmeos digital do Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: df12d6866f5e9e6bf492e228e32b0b10f7266eb4
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71843838"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Como depurar funções definidas pelo usuário no Azure digital gêmeos

Este artigo resume como diagnosticar e depurar funções definidas pelo usuário no Azure digital gêmeos. Em seguida, ele identifica alguns dos cenários mais comuns encontrados ao depurá-los.

>[!TIP]
> Leia [como configurar o monitoramento e o log](./how-to-configure-monitoring.md) para saber mais sobre como configurar ferramentas de depuração no Azure digital gêmeos usando logs de atividade, logs de diagnóstico e Azure monitor.

## <a name="debug-issues"></a>Problemas de depuração

Saber como diagnosticar problemas no Azure digital gêmeos permite que você analise problemas com eficiência, identifique as causas dos problemas e forneça as soluções apropriadas para eles.

Uma variedade de ferramentas de log, análise e diagnóstico é fornecida para esse fim.

### <a name="enable-logging-for-your-instance"></a>Habilitar o registro em log para sua instância

O Azure digital gêmeos dá suporte ao registro em log, monitoramento e análise robustos. As soluções que os desenvolvedores podem usar Azure Monitor logs, logs de diagnóstico, logs de atividades e outros serviços para dar suporte às necessidades complexas de monitoramento de um aplicativo de IoT. As opções de log podem ser combinadas para consultar ou exibir registros em vários serviços e para fornecer cobertura de log granular para muitos serviços.

* Para a configuração de log específica do Azure digital gêmeos, leia [como configurar o monitoramento e o registro em log](./how-to-configure-monitoring.md).
* Consulte a visão geral de [Azure monitor](../azure-monitor/overview.md) para saber mais sobre configurações de log avançadas habilitadas por meio de Azure monitor.
* Examine o artigo [coletar e consumir dados de log dos recursos do Azure](../azure-monitor/platform/resource-logs-overview.md) para definir as configurações de log de diagnóstico no Azure digital gêmeos por meio do portal do azure, CLI do Azure ou PowerShell.

Uma vez configurado, você poderá selecionar todas as categorias de log, métricas e usar espaços de trabalho de Azure Monitor do log Analytics eficientes para dar suporte aos seus esforços de depuração.

### <a name="trace-sensor-telemetry"></a>Telemetria do sensor de rastreamento

Para rastrear a telemetria do sensor, verifique se as configurações de diagnóstico estão habilitadas para sua instância de gêmeos digital do Azure. Em seguida, verifique se todas as categorias de log desejadas estão selecionadas. Por fim, confirme se os logs desejados estão sendo enviados aos logs de Azure Monitor.

Para fazer a correspondência de uma mensagem de telemetria do sensor com seus respectivos logs, você pode especificar uma ID de correlação nos dados do evento que estão sendo enviados. Para fazer isso, defina a `x-ms-client-request-id` Propriedade como um GUID.

Depois de enviar telemetria, abra Azure Monitor log Analytics para consultar logs usando a ID de correlação do conjunto:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valor da consulta | Substituir |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | A ID de correlação especificada nos dados do evento |

Para ver todas as consultas de logs de telemetria recentes:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Se você habilitar o registro em log para a função definida pelo usuário, esses logs aparecerão na instância do log `UserDefinedFunction`Analytics com a categoria. Para recuperá-los, insira a seguinte condição de consulta no log Analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Para obter mais informações sobre operações de consulta avançadas, leia [introdução às consultas](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identificar problemas comuns

Tanto o diagnóstico quanto a identificação de problemas comuns são importantes ao solucionar problemas de sua solução. Vários problemas que normalmente são encontrados durante o desenvolvimento de funções definidas pelo usuário são resumidos nas subseções a seguir.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Verificar se uma atribuição de função foi criada

Sem uma atribuição de função criada dentro da API de gerenciamento, a função definida pelo usuário não tem acesso para executar nenhuma ação, como enviar notificações, recuperar metadados e definir valores computados na topologia.

Verifique se existe uma atribuição de função para sua função definida pelo usuário por meio da API de gerenciamento:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Valor do parâmetro | Substituir |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | A ID da função definida pelo usuário para a qual recuperar atribuições de função|

Saiba [como criar uma atribuição de função para sua função definida pelo usuário](./how-to-user-defined-functions.md), se não existir nenhuma atribuição de função.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Verificar se o correspondente funciona para a telemetria de um sensor

Com a seguinte chamada em relação à API de gerenciamento de instâncias do Azure digital gêmeos, você pode determinar se uma determinada correspondência se aplica ao sensor fornecido.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | A ID do correspondente que você deseja avaliar |
| *YOUR_SENSOR_IDENTIFIER* | A ID do sensor que você deseja avaliar |

Resposta:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Verificar o que um sensor dispara

Com a seguinte chamada em relação às APIs de gerenciamento de gêmeos digital do Azure, você pode determinar os identificadores de suas funções definidas pelo usuário disparadas pela telemetria de entrada do sensor fornecido:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | A ID do sensor para enviar telemetria |

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

### <a name="issue-with-receiving-notifications"></a>Problema com notificações de recebimento

Quando você não estiver recebendo notificações da função definida pelo usuário disparada, confirme se o parâmetro de tipo de objeto de topologia corresponde ao tipo de identificador que está sendo usado.

**Incorreto** Exemplo

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Esse cenário surge porque o identificador usado se refere a um sensor enquanto o tipo de objeto de topologia `Space`especificado é.

**Corrigir** Exemplo

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

A maneira mais fácil de não encontrar esse problema é usar o `Notify` método no objeto de metadados.

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

Se você habilitar as configurações de diagnóstico, poderá encontrar essas exceções comuns:

1. **Limitação**: se a função definida pelo usuário exceder os limites de taxa de execução descritos no artigo [limites de serviço](./concepts-service-limits.md) , ela será limitada. Nenhuma outra operação é executada com êxito até que os limites de limitação expirem.

1. **Dados não encontrados**: se a função definida pelo usuário tentar acessar metadados que não existem, a operação falhará.

1. **Não autorizado**: se sua função definida pelo usuário não tem uma atribuição de função definida ou não tem permissão suficiente para acessar determinados metadados da topologia, a operação falha.

## <a name="next-steps"></a>Passos seguintes

- Saiba como habilitar o [monitoramento e os logs](./how-to-configure-monitoring.md) no gêmeos digital do Azure.

- Leia o artigo [visão geral do log de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md) para obter mais opções de log do Azure.
