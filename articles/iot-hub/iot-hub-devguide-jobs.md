---
title: Compreender os empregos do Azure IoT Hub [ Microsoft Docs
description: Guia de desenvolvedores - agendamento de trabalhos para executar em vários dispositivos ligados ao seu hub IoT. Os trabalhos podem atualizar etiquetas e propriedades desejadas e invocar métodos diretos em vários dispositivos.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 147dd0f454bd85673bcba5cd6148c5da9716c580
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65409041"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Programar tarefas em vários dispositivos

O Azure IoT Hub permite uma série de blocos de construção como [propriedades gémeas e etiquetas de dispositivos e métodos diretos.](iot-hub-devguide-device-twins.md) [direct methods](iot-hub-devguide-direct-methods.md) Normalmente, as aplicações de back-end permitem aos administradores e operadores de dispositivos atualizar e interagir com dispositivos IoT a granel e a uma hora programada. Os trabalhos executam atualizações duplas do dispositivo e métodos diretos contra um conjunto de dispositivos numa hora programada. Por exemplo, um operador usaria uma aplicação de back-end que inicia e rastreia um trabalho para reiniciar um conjunto de dispositivos no edifício 43 e no piso 3 de cada vez que não seria perturbador para as operações do edifício.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Considere utilizar empregos quando necessitar de agendar e acompanhar o progresso de qualquer uma das seguintes atividades num conjunto de dispositivos:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

## <a name="job-lifecycle"></a>Ciclo de vida profissional

Os postos de trabalho são iniciados pela solução na parte de trás e mantidos pelo IoT Hub. Você pode iniciar um trabalho através`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`de um URI virado para o serviço e consulta`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`para o progresso em um trabalho de execução através de um URI virado para o serviço ( ). Para refrescar o estatuto de trabalho de funcionamento uma vez iniciado um emprego, executar uma consulta de trabalho.

> [!NOTE]
> Quando inicia um trabalho, os nomes e valores de propriedade só podem conter alfanumérico imprimível US-ASCII, exceto qualquer um no seguinte conjunto:`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Empregos para executar métodos diretos

O seguinte corte mostra os detalhes do pedido HTTPS 1.1 para executar um [método direto](iot-hub-devguide-direct-methods.md) num conjunto de dispositivos utilizando um trabalho:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
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

A condição de consulta também pode estar num único dispositivo ou numa lista de IDs do dispositivo, como mostram os seguintes exemplos:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT Hub Consulta Language](iot-hub-devguide-query-language.md) cobre a linguagem de consulta IoT Hub em detalhes adicionais.

O seguinte corte mostra o pedido e a resposta para um trabalho programado para chamar um método direto chamado testMethod em todos os dispositivos em contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Empregos para atualizar propriedades gémeas do dispositivo

O seguinte snippet mostra o pedido HTTPS 1.1 para atualizar propriedades gémeas do dispositivo usando um trabalho:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> A *propriedade updateTwin* requer uma correspondência de etag válida; por exemplo, `etag="*"`.

O seguinte corte mostra o pedido e a resposta para um trabalho programado para atualizar propriedades gémeas do dispositivo para dispositivo de teste no contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Consulta de progresso saquede empregos

O seguinte corte mostra o pedido HTTPS 1.1 para consultas de emprego:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

A continuação Token é fornecida a partir da resposta.

Pode consultar o estado de execução do emprego em cada dispositivo utilizando a linguagem de [consulta IoT Hub para gémeos de dispositivos, empregos e encaminhamento de mensagens.](iot-hub-devguide-query-language.md)

## <a name="jobs-properties"></a>Propriedades de Emprego

A lista que se segue mostra as propriedades e as descrições correspondentes, que podem ser utilizadas quando se consulta maquea para empregos ou resultados de emprego.

| Propriedade | Descrição |
| --- | --- |
| **jobId** |A aplicação forneceu identificação para o trabalho. |
| **startTime** |A aplicação proporcionou o tempo de início (ISO-8601) para o trabalho. |
| **endTime** |O IoT Hub forneceu a data (ISO-8601) para quando o trabalho foi concluído. Válido apenas depois de o trabalho chegar ao estado 'concluído'. |
| **tipo** |Tipos de empregos: |
| | **horárioSUpdateTwin**: Um trabalho usado para atualizar um conjunto de propriedades ou etiquetas desejadas. |
| | **horárioSDeviceMethod**: Um trabalho utilizado para invocar um método de dispositivo num conjunto de gémeos dispositivos. |
| **status** |Estado atual do trabalho. Valores possíveis para o estado: |
| | **pendente**: Agendado e à espera de ser recolhido pelo serviço de trabalho. |
| | **programado**: Agendado para um tempo no futuro. |
| | **funcionamento**: Atualmente trabalho ativo. |
| | **cancelado**: O trabalho foi cancelado. |
| | **falhado**: Trabalho falhou. |
| | **concluído**: O trabalho está concluído. |
| **dispositivoJobStatistics** |Estatísticas sobre a execução do trabalho. |
| | **dispositivoPropriedades JobStatistics:** |
| | **dispositivoJobStatistics.deviceCount**: Número de dispositivos em função. |
| | **dispositivoJobStatistics.failedCount**: Número de dispositivos onde o trabalho falhou. |
| | **dispositivoJobStatistics.succeededCount**: Número de dispositivos onde o trabalho foi bem sucedido. |
| | **dispositivoJobStatistics.runningCount**: Número de dispositivos que estão atualmente a executar o trabalho. |
| | **dispositivoJobStatistics.pendenteCount**: Número de dispositivos pendentes para executar o trabalho. |

### <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de execução e gestão.

* [O estrangulamento e](iot-hub-devguide-quotas-throttling.md) as quotas descrevem as quotas aplicáveis ao serviço IoT Hub e o comportamento de estrangulamento que se espera quando se utiliza o serviço.

* O [dispositivo e o serviço Azure IoT](iot-hub-devguide-sdks.md) listam os vários SDKs linguísticos que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* [IoT Hub consulta linguagem para gémeos dispositivos, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta IoT Hub. Use esta linguagem de consulta para obter informações do IoT Hub sobre os seus gémeos e empregos.

* [O suporte IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Para experimentar alguns dos conceitos descritos neste artigo, consulte o seguinte tutorial IoT Hub:

* [Agendar e difundir tarefas](iot-hub-node-node-schedule-jobs.md)