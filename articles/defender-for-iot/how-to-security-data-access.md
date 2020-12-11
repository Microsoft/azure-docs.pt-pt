---
title: Acesso segurança & dados de recomendação
description: Saiba como aceder aos dados de alerta de segurança e recomendação ao utilizar o Defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: c4f65da4f9a4f7ade94d76e99d57439e62b3c808
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092062"
---
# <a name="access-your-security-data"></a>Aceda aos seus dados de segurança

O Defender para ioT armazena alertas de segurança, recomendações e dados de segurança brutos (se optar por guardá-lo) no seu espaço de trabalho Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar qual o espaço de trabalho log Analytics é utilizado:

1. Abra o seu hub IoT.
1. Clique na lâmina **Definições** sob a secção **De Segurança.**
1. Clique em **Data Collection** e altere a configuração do espaço de trabalho do Log Analytics.

Para aceder aos seus alertas e recomendações no seu espaço de trabalho Log Analytics após a configuração:

1. Escolha um alerta ou recomendação no Defender para IoT.
1. Clique **em mais investigação,** em seguida, clique **em ver quais os dispositivos que têm este clique de alerta aqui e ver a coluna DeviceId**.

Para obter detalhes sobre a consulta de dados do Log Analytics, consulte [Começar com consultas no Log Analytics](/azure/azure-monitor/log-query/get-started-queries).

## <a name="security-alerts"></a>Alertas de segurança

Os alertas de segurança são armazenados na tabela _AzureSecurityOfThings.SecurityAlert_ no espaço de trabalho Log Analytics configurado para a solução Defender for IoT.

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | Alertaseverity | DisplayName                           | Descrição                                             | Extensões                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Ataque de força bruta conseguiu           | Um ataque da força bruta no dispositivo foi bem sucedido        |    { "Endereço de origem completa": "[ \" 10.165.12.18: \" ]", "Nomes de utilizador": \" \" "[]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Login local bem-sucedido no dispositivo      | Foi detetado um login local bem sucedido no dispositivo     | { "Endereço remoto": ""Porta remota": "Porta Local": "Porta de login": "Caixa de entrada/su", "Processo de Login Id": "28207", "Nome de utilizador": "atacante", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Tentativa de login local falhada no dispositivo  | Uma tentativa de login local falhada para o dispositivo foi detetada |    { "Endereço remoto": ""Porta remota": "Porta Local": "Porta de login": "Caixa de entrada/su", "Acesso ao processo de acesso": "22644", "Nome de utilizador": "atacante", "DeviceId": "IoT-Device-Linux" } |

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

| IoTHubId                                                                                                       | DeviceId      | Alertaseverity | DisplayName                           | de palavras |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Ataque de força bruta conseguiu           | 9   |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Tentativa de login local falhada no dispositivo  | 242 |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Login local bem-sucedido no dispositivo      | 31  |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Minerador de moedas cripto                     | 4   |

### <a name="iot-hub-summary"></a>Resumo do hub IoT

Selecione uma série de dispositivos distintos que tiveram alertas na última semana, por IoT Hub, gravidade de alerta, tipo de alerta

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

| IoTHubId                                                                                                       | Alertaseverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Ataque de força bruta conseguiu           | 1          |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Tentativa de login local falhada no dispositivo  | 1          |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Login local bem-sucedido no dispositivo      | 1          |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Minerador de moedas cripto                     | 1          |

## <a name="security-recommendations"></a>Recomendações de segurança

As recomendações de segurança são armazenadas na tabela _AzureSecurityOfThings.SecurityRecommendation_ no espaço de trabalho Log Analytics configurado para a solução Defender for IoT.

Fornecemos uma série de perguntas úteis para ajudá-lo a começar a explorar recomendações de segurança.

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

| TimeGenerated | IoTHubId | DeviceId | RecomendaçõesEverity | Estado de Recomendação | RecomendaçãoDisplayName | Descrição | RecomendaçõesAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio | Ativo | Foi encontrada regra de firewall permissiva na cadeia de entrada | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portos | {"Rules":"[{ \" SourceAddress \" : , \" \" \" SourcePort : , \" \" \" \" DestinationAddress \" : , \" \" \" DestinationPort : \" \" 1337 \" }}"} |
| 2019-03-22T10:50:27.237 | /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio | Ativo | Foi encontrada regra de firewall permissiva na cadeia de entrada | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portos | {"Rules":"[{ \" SourceAddress \" : , \" \" \" SourcePort : , \" \" \" \" DestinationAddress \" : , \" \" \" DestinationPort : \" \" 1337 \" }}"} |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de recomendações de segurança ativas distintas, agrupadas pelo IoT Hub, dispositivo, gravidade de recomendação e tipo.

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

| IoTHubId                                                                                                       | DeviceId      | RecomendaçõesEverity | de palavras |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | 2   |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | 1 |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | 1  |
| /subscrições/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | 4   |

## <a name="next-steps"></a>Passos seguintes

- Leia o Defender para a [Visão Geral do IoT](overview.md)
- Saiba mais sobre Defender para [Arquitetura](architecture.md) IoT
- Compreender e explorar [o Defender para alertas IoT](concept-security-alerts.md)
- Compreender e explorar [o Defender para recomendações de IoT](concept-recommendations.md)