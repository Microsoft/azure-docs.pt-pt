---
title: Acessando dados usando a central de segurança do Azure para IoT | Microsoft Docs
description: Saiba mais sobre como acessar seus dados de alerta e recomendação de segurança ao usar a central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597172"
---
# <a name="access-your-security-data"></a>Acessar seus dados de segurança 

A central de segurança do Azure para IoT armazena alertas de segurança, recomendações e dados brutos de segurança (se você optar por salvá-lo) em seu espaço de trabalho do Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar qual espaço de trabalho do Log Analytics é usado:

1. Abra o Hub IoT.
1. Clique na folha **visão geral** na seção **segurança**
2. Clique em **configurações**e altere a configuração do log Analytics espaço de trabalho.

Para acessar seus alertas e recomendações em seu espaço de trabalho do Log Analytics após a configuração:

1. Escolha um alerta ou recomendação na central de segurança do Azure para IoT. 
2. Clique em **investigação adicional**e, em seguida, clique **para ver quais dispositivos têm esse alerta clique aqui e exiba a coluna**DeviceID.

Para obter detalhes sobre como consultar dados de Log Analytics, consulte Introdução [às consultas no log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertas de segurança

Os alertas de segurança são armazenados na tabela _AzureSecurityOfThings. SecurityAlert_ no espaço de trabalho log Analytics configurado para a solução da central de segurança do Azure para IOT.

Fornecemos várias consultas úteis para ajudá-lo a começar a explorar os alertas de segurança.

### <a name="sample-records"></a>Registros de exemplo

Selecionar alguns registros aleatórios

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Descrição                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Alta          | Ataque de força bruta bem-sucedido           | Um ataque de força bruta no dispositivo foi bem-sucedido        |    {"Endereço de origem completo": "\"[10.165.12.18\":]", "nomes de usuário":\""[\"]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Alta          | Logon local bem-sucedido no dispositivo      | Foi detectado um logon local bem-sucedido no dispositivo     | {"Endereço remoto": "?", "porta remota": "", "porta local": "", "Shell de logon": "/bin/su", "ID do processo de logon": "28207", "nome de usuário": "invasor", "DeviceID": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Alta          | Falha na tentativa de logon local no dispositivo  | Foi detectada uma tentativa de logon local com falha no dispositivo |  {"Endereço remoto": "?", "porta remota": "", "porta local": "", "Shell de logon": "/bin/su", "ID do processo de logon": "22644", "nome de usuário": "invasor", "DeviceID": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de alertas de segurança distintos detectados na última semana, agrupados por Hub IoT, dispositivo, severidade do alerta, tipo de alerta.

```
// Get the number of distinct security alerts detected in the last week, grouped by 
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

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Alta          | Ataque de força bruta bem-sucedido           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Médio        | Falha na tentativa de logon local no dispositivo  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Alta          | Logon local bem-sucedido no dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Médio        | Miner de moeda de criptografia                     | 4   |

### <a name="iot-hub-summary"></a>Resumo do Hub IoT

Selecione um número de dispositivos distintos que tinham alertas na última semana, por Hub IoT, severidade do alerta, tipo de alerta

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

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Ataque de força bruta bem-sucedido           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Falha na tentativa de logon local no dispositivo  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Logon local bem-sucedido no dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Miner de moeda de criptografia                     | 1          |

## <a name="security-recommendations"></a>Recomendações de segurança

As recomendações de segurança são armazenadas na tabela _AzureSecurityOfThings. SecurityRecommendation_ no espaço de trabalho log Analytics configurado para a solução da central de segurança do Azure para IOT.

Fornecemos várias consultas úteis para ajudá-lo a começar a explorar as recomendações de segurança.

### <a name="sample-records"></a>Registros de exemplo

Selecionar alguns registros aleatórios

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | Recomendaçãostate | RecommendationDisplayName | Descrição | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Médio | Active | A regra de firewall permissiva na cadeia de entrada foi encontrada | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma vasta gama de Portas ou endereços IP | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Médio | Active | A regra de firewall permissiva na cadeia de entrada foi encontrada | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma vasta gama de Portas ou endereços IP | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de recomendações de segurança ativas distintas, agrupadas por Hub IoT, dispositivo, severidade de recomendação e tipo.

```
// Get the number of distinct active security recommendations, grouped by by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Alta          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Médio        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Alta          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_do_dispositivo > | Médio        | 4   |


## <a name="next-steps"></a>Passos Seguintes

- Leia a [visão geral](overview.md) da central de segurança do Azure para IOT
- Saiba mais sobre a [arquitetura](architecture.md) da central de segurança do Azure para IOT
- Entender e explorar [a central de segurança do Azure para alertas de IOT](concept-security-alerts.md)
- Entender e explorar [a central de segurança do Azure para a recomendação de IOT](concept-recommendations.md)
