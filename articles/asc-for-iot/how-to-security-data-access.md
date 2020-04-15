---
title: Aceder aos dados de recomendação de & de segurança
description: Saiba como aceder ao seu alerta de segurança e dados de recomendação ao utilizar o Azure Security Center para ioT.
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
ms.openlocfilehash: bbea0accc79cafb6fea3f1438a71250dc02f4d62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311012"
---
# <a name="access-your-security-data"></a>Aceda aos seus dados de segurança

O Azure Security Center for IoT armazena alertas de segurança, recomendações e dados de segurança bruta (se optar por guardá-lo) no seu espaço de trabalho Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar qual o espaço de trabalho do Log Analytics:

1. Abra o seu centro de iôts.
1. Clique na lâmina **de visão geral** sob a secção de **segurança**
1. Clique em **Definições**e altere a configuração do espaço de trabalho Do Log Analytics.

Para aceder aos seus alertas e recomendações no seu espaço de trabalho Log Analytics após a configuração:

1. Escolha um alerta ou recomendação no Azure Security Center para IoT.
1. Clique em **mais investigações**e clique **em ver quais os dispositivos que têm este alerta clique aqui e veja a coluna DeviceId**.

Para mais detalhes sobre a consulta de dados do Log Analytics, consulte [Começar com consultas no Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertas de segurança

Os alertas de segurança são armazenados na tabela _AzureSecurityOfThings.SecurityAlert_ no espaço de trabalho log Analytics configurado para o Centro de Segurança Azure para a solução IoT.

Fornecemos uma série de consultas úteis para ajudá-lo a começar a explorar alertas de segurança.

### <a name="sample-records"></a>Registos de amostras

Selecione alguns registos aleatórios

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertasGravidade | DisplayName                           | Descrição                                             | Propriedades Estendidas                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Ataque à força bruta sucedeu           | Um ataque à força bruta no dispositivo foi bem sucedido        |    \"{ "Endereço fonte completa": "[10.165.12.18:\"]", "Nomes de utilizador": "[]",\"\""DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Login local bem sucedido no dispositivo      | Foi detetado um login local bem sucedido no dispositivo     | { "Endereço remoto": "?", "Porto Remoto": "Porto Local": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Tentativa de login local falhada no dispositivo  | Foi detetada uma tentativa de login local falhada para o dispositivo. |    { "Endereço remoto": "?", "Porto Remoto": "Porto Local": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de alertas de segurança distintos detetados na última semana, agrupados pelo IoT Hub, dispositivo, gravidade de alerta, tipo de alerta.

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

| IoTHubId                                                                                                       | DeviceId      | AlertasGravidade | DisplayName                           | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Ataque à força bruta sucedeu           | 9   |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Tentativa de login local falhada no dispositivo  | 242 |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Login local bem sucedido no dispositivo      | 31  |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Minera de Moedacripta                     | 4   |

### <a name="iot-hub-summary"></a>Resumo do hub ioT

Selecione uma série de dispositivos distintos que tiveram alertas na última semana, pelo IoT Hub, severidade de alerta, tipo de alerta

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

| IoTHubId                                                                                                       | AlertasGravidade | DisplayName                           | Dispositivos Cnt |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Ataque à força bruta sucedeu           | 1          |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Tentativa de login local falhada no dispositivo  | 1          |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Login local bem sucedido no dispositivo      | 1          |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Minera de Moedacripta                     | 1          |

## <a name="security-recommendations"></a>Recomendações de segurança

As recomendações de segurança são armazenadas na tabela _AzureSecurityOfThings.SecurityRecommendation_ no espaço de trabalho Log Analytics configurado para o Centro de Segurança Azure para a solução IoT.

Fornecemos uma série de consultas úteis para ajudá-lo a começar a explorar recomendações de segurança.

### <a name="sample-records"></a>Registos de amostras

Selecione alguns registos aleatórios

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

| TimeGenerated | IoTHubId | DeviceId | RecomendaçõesGravidadeidade | Estado de Recomendação | Nome de exibição de recomendações | Descrição | Recomendação Dados Adicionais |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio | Ativa | Regra de firewall permissiva na cadeia de entrada foi encontrada | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou Portas | {"Regras":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :\", DestinationPort : 1337 }"}}}}}}}}}}}}}}}}}}}}} |
| 2019-03-22T10:50:27.237 | /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio | Ativa | Regra de firewall permissiva na cadeia de entrada foi encontrada | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou Portas | {"Regras":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :\", DestinationPort : 1337 }"}}}}}}}}}}}}}}}}}}}}} |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de recomendações de segurança ativa distintas, agruparadas pelo IoT Hub, dispositivo, severidade de recomendação e tipo.

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

| IoTHubId                                                                                                       | DeviceId      | RecomendaçõesGravidadeidade | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | 2   |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | 1 |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | 1  |
| /subscrições/<subscription_id>/recursosGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | 4   |

## <a name="next-steps"></a>Passos seguintes

- Leia o Centro de Segurança Azure para [visão geral](overview.md) do IoT
- Conheça o Azure Security Center for IoT [Architecture](architecture.md)
- Compreender e explorar [o Azure Security Center para alertas ioT](concept-security-alerts.md)
- Compreender e explorar o [Azure Security Center para recomendação do IoT](concept-recommendations.md)
