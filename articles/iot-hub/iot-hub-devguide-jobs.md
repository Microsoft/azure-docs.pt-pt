---
title: Compreender as tarefas do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – agendamento de trabalhos para execução em vários dispositivos ligados ao seu hub IoT. Tarefas podem atualizar as etiquetas e propriedades pretendidas e invocar métodos diretos em vários dispositivos.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2018
ms.openlocfilehash: c2b05b1854b4f1d7ee4ac65ebe635330ba8c604e
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011369"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Programar tarefas em vários dispositivos

O IoT Hub do Azure permite que um número de blocos de construção como [etiquetas e propriedades dos dispositivos duplos](iot-hub-devguide-device-twins.md) e [métodos diretos](iot-hub-devguide-direct-methods.md). Normalmente, as aplicações de back-end permitem operadores e administradores de dispositivos atualizar e interagir com dispositivos de IoT em massa e num horário agendado. Tarefas de atualizações de dispositivo duplo e métodos diretos em relação a um conjunto de dispositivos de execução num horário agendado. Por exemplo, um operador utilizar uma aplicação de back-end que inicia e regista uma tarefa para reiniciar um conjunto de dispositivos na criação de 43 e andar 3 cada vez que não seria perturbador para as operações de compilação.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Considere a utilização de tarefas quando precisa para agendar e controlar o progresso qualquer uma das seguintes atividades num conjunto de dispositivos:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

## <a name="job-lifecycle"></a>Ciclo de vida de tarefa

Tarefas são iniciadas pela solução de back-end e mantidas pelo IoT Hub. Pode iniciar uma tarefa através de um URI de serviço com acesso à (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) e a consulta para o progresso numa tarefa de execução através de um URI de serviço com acesso à (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Para atualizar o estado de tarefas em execução depois de uma tarefa é iniciada, execute uma consulta da tarefa.

> [!NOTE]
> Quando seleciona uma tarefa, os nomes das propriedades e valores podem apenas conter US-ASCII imprimível alfanumérico, exceto as no conjunto de seguinte: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Tarefas para executar métodos diretos

O fragmento seguinte mostra os detalhes da solicitação HTTPS 1.1 para a execução de um [método direto](iot-hub-devguide-direct-methods.md) num conjunto de dispositivos através de uma tarefa:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleDirectMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

A condição de consulta também pode ser um ID de dispositivo único ou numa lista de identificações, conforme mostrado nos exemplos seguintes de dispositivo:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[Linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md) aborda a linguagem de consulta do IoT Hub em detalhes adicionais.

## <a name="jobs-to-update-device-twin-properties"></a>Tarefas para atualizar as propriedades dos dispositivos duplos

O fragmento seguinte mostra os detalhes da solicitação HTTPS 1.1 para atualizar as propriedades dos dispositivos duplos usar um trabalho de:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleTwinUpdate",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

## <a name="querying-for-progress-on-jobs"></a>Consultar o progresso das tarefas de

O fragmento seguinte mostra os detalhes da solicitação HTTPS 1.1 para consultar tarefas:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>
```

O continuationToken é fornecido da resposta.

Pode consultar o estado de execução da tarefa em cada dispositivo a utilizar o [linguagem de consulta do IoT Hub para dispositivos duplos, tarefas e encaminhamento de mensagens](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Propriedades de tarefas

A lista seguinte mostra as propriedades e as descrições correspondentes, que podem ser utilizadas ao consultar para tarefas ou resultados das tarefas.

| Propriedade | Descrição |
| --- | --- |
| **jobId** |Aplicação fornecida ID para a tarefa. |
| **startTime** |Aplicação fornecida a hora de início (ISO-8601) para a tarefa. |
| **endTime** |IoT Hub fornecidos data (ISO-8601) para quando a tarefa é concluída. Válido apenas depois que a tarefa de atinge o estado "concluído". |
| **tipo** |Tipos de tarefas: |
| | **scheduledUpdateTwin**: Uma tarefa utilizada para atualizar um conjunto de propriedades pretendidas ou etiquetas. |
| | **scheduledDeviceMethod**: Uma tarefa utilizada para invocar um método de dispositivo num conjunto de dispositivos duplos. |
| **status** |Estado atual da tarefa. Valores possíveis para obter o estado: |
| | **Pendente**: Agendada e a aguardar a ser escolhidas pelo serviço de tarefa. |
| | **scheduled**: Agendada para um momento no futuro. |
| | **Executar**: Tarefa atualmente ativa. |
| | **canceled**: Tarefa foi cancelada. |
| | **failed**: Falha na tarefa. |
| | **Concluído**: Tarefa foi concluída. |
| **deviceJobStatistics** |Estatísticas sobre a execução da tarefa. |
| | **deviceJobStatistics** propriedades: |
| | **deviceJobStatistics.deviceCount**: Número de dispositivos no trabalho. |
| | **deviceJobStatistics.failedCount**: Número de dispositivos em que a tarefa falhou. |
| | **deviceJobStatistics.succeededCount**: Número de dispositivos em que a tarefa foi concluída com êxito. |
| | **deviceJobStatistics.runningCount**: Número de dispositivos que estão atualmente a executar a tarefa. |
| | **deviceJobStatistics.pendingCount**: Número de dispositivos que estão pendentes para executar a tarefa. |

### <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador do IoT Hub incluem:

* [Pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descreve vários pontos de extremidade que cada hub IoT expõe para operações de tempo de execução e gestão.

* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md) descreve as quotas que se aplicam ao serviço IoT Hub e o comportamento da limitação esperar ao utilizar o serviço.

* [Azure SDKs de dispositivo e de serviços de IoT](iot-hub-devguide-sdks.md) indica o idioma de vários SDKs, pode utilizar ao desenvolver aplicações de dispositivos e de serviços que interagem com o IoT Hub.

* [Linguagem de consulta do IoT Hub para dispositivos duplos, tarefas e encaminhamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta do IoT Hub. Utilize essa linguagem de consulta para obter informações a partir do IoT Hub sobre os seus dispositivos duplos e trabalhos.

* [Suporte para MQTT do IoT Hub](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos Seguintes

Para experimentar alguns dos conceitos descritos neste artigo, consulte o tutorial seguinte do IoT Hub:

* [Agendar e difundir tarefas](iot-hub-node-node-schedule-jobs.md)