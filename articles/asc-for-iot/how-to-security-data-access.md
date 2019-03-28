---
title: Acesso a dados com o ASC para pré-visualização do IoT | Documentos da Microsoft
description: Saiba mais sobre como aceder aos seus dados de alerta e as recomendações de segurança ao utilizar o ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541996"
---
# <a name="access-your-security-data"></a>Aceder aos seus dados de segurança 

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC para IoT armazena dados de segurança bruto, recomendações e alertas de segurança (caso pretenda guardá-lo) na sua área de trabalho do Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar a área de trabalho do Log Analytics é utilizada:

1. Abra o seu hub IoT.
1. Clique em **segurança**
2. Clique em **definições**e altere a configuração de área de trabalho do Log Analytics.

Para acessar sua área de trabalho do Log Analytics após a configuração:

1. Escolha um alerta no ASC para IoT. 
2. Clique em **ainda mais investigação**, em seguida, clique em **para ver quais os dispositivos que têm este alerta, clique aqui e ver a coluna de DeviceId**.

Para obter detalhes sobre como consultar dados do Log Analytics, consulte [introdução às consultas no Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertas de segurança

Alertas de segurança são armazenados no **ASCforIoT.SecurityAlert** tabela dentro de sua área de trabalho do Log Analytics configurada.

Utilize as seguintes consultas de kql básica para começar a explorar os alertas de segurança.

### <a name="sample-records-query"></a>Consulta de registos de exemplo

Para selecionar registos alguns aleatoriamente: 

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo 

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Nome a Apresentar                           | Descrição                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Elevado          | Ataque de força bruta foi concluída com êxito           | Um ataque de força bruta no dispositivo foi com êxito        |    {"Endereço de origem completo": "[\"10.165.12.18:\"]", "Nomes de utilizador": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Elevado          | Início de sessão local com êxito no dispositivo      | Foi detetado um início de sessão local com êxito no dispositivo     | {"Endereço remoto": "?", "Porta remota": "", "Porta Local": "", "Início de sessão de Shell": "/ bin/su", "Id do processo de início de sessão": "28207", "nome de utilizador": "atacante", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Elevado          | Falha na tentativa de início de sessão local no dispositivo  | Foi detetada uma tentativa de início de sessão local no dispositivo |  {"Endereço remoto": "?", "Porta remota": "", "Porta Local": "", "Início de sessão de Shell": "/ bin/su", "Id do processo de início de sessão": "22644", "nome de utilizador": "atacante", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary-query"></a>Consulta de resumida do dispositivo

Utilize esta consulta kql para selecionar um número de alertas de segurança distintas detetada na semana passada por gravidade do alerta do dispositivo do IoT Hub, tipo de alerta.

```
// Select number of distinct security alerts detected last week by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

#### <a name="device-summary-query-results"></a>Resultados da consulta de resumo de dispositivo

| IoTHubId | DeviceId| AlertSeverity| Nome a Apresentar | Contagem |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Elevado          | Ataque de força bruta foi concluída com êxito           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Falha na tentativa de início de sessão local no dispositivo  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Elevado          | Início de sessão local com êxito no dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Moeda criptografia Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>Resumo do IoT Hub

Utilize esta consulta kql para selecionar um número de dispositivos distintos que tinha alertas na última semana, pelo IoT hub, gravidade do alerta, tipo de alerta:

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

#### <a name="iot-hub-summary-query-results"></a>Resultados de consulta de resumo do IoT Hub

| IoTHubId                                                                                                       | AlertSeverity | Nome a Apresentar                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Elevado          | Ataque de força bruta foi concluída com êxito           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Falha na tentativa de início de sessão local no dispositivo  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Elevado          | Início de sessão local com êxito no dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Moeda criptografia Miner                     | 1          |



## <a name="next-steps"></a>Passos Seguintes

- Leia o ASC para IoT [descrição geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Compreender e explorar [ASC para alertas de IoT](concept-security-alerts.md)
