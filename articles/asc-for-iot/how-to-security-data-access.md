---
title: Acesso a dados com o Centro de segurança do Azure para pré-visualização do IoT | Documentos da Microsoft
description: Saiba mais sobre como aceder aos seus dados de alerta e as recomendações de segurança ao utilizar o Centro de segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 1ec6a174d05f8707bbffcc9fb013a98c2eb9196c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200551"
---
# <a name="access-your-security-data"></a>Aceder aos seus dados de segurança 

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Centro de segurança do Azure (ASC) para IoT armazena dados de segurança bruto, recomendações e alertas de segurança (caso pretenda guardá-lo) na sua área de trabalho do Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar a área de trabalho do Log Analytics é utilizada:

1. Abra o seu hub IoT.
1. Clique em **segurança**
2. Clique em **definições**e altere a configuração de área de trabalho do Log Analytics.

Para acessar sua área de trabalho do Log Analytics após a configuração:

1. Escolha um alerta ou uma recomendação no ASC para IoT. 
2. Clique em **ainda mais investigação**, em seguida, clique em **para ver quais os dispositivos que têm este alerta, clique aqui e ver a coluna de DeviceId**.

Para obter detalhes sobre como consultar dados do Log Analytics, consulte [introdução às consultas no Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertas de segurança

Alertas de segurança são armazenados no _AzureSecurityOfThings.SecurityAlert_ tabela na área de trabalho do Log Analytics configurada para o ASC para solução de IoT.

Fornecemos um número de consultas úteis para ajudá-lo a começar a explorar os alertas de segurança.

### <a name="sample-records"></a>Registos de exemplo

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | displayName                           | Descrição                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Ataque de força bruta foi concluída com êxito           | Um ataque de força bruta no dispositivo foi com êxito        |    {"Endereço de origem completo": "[\"10.165.12.18:\"]", "Nomes de utilizador": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Início de sessão local com êxito no dispositivo      | Foi detetado um início de sessão local com êxito no dispositivo     | {"Endereço remoto": "?", "Porta remota": "", "Porta Local": "", "Início de sessão de Shell": "/ bin/su", "Id do processo de início de sessão": "28207", "nome de utilizador": "atacante", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Falha na tentativa de início de sessão local no dispositivo  | Foi detetada uma tentativa de início de sessão local no dispositivo |  {"Endereço remoto": "?", "Porta remota": "", "Porta Local": "", "Início de sessão de Shell": "/ bin/su", "Id do processo de início de sessão": "22644", "nome de utilizador": "atacante", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Resumo do dispositivo

Selecione o número de alertas de segurança distintas detetada na semana passada por gravidade do alerta do dispositivo do IoT Hub, tipo de alerta.

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

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | displayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Ataque de força bruta foi concluída com êxito           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Falha na tentativa de início de sessão local no dispositivo  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Início de sessão local com êxito no dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Moeda criptografia Miner                     | 4   |

### <a name="iot-hub-summary"></a>Resumo de hub IoT

Selecione um número de dispositivos distintos que tinha alertas na última semana, pelo IoT Hub, gravidade do alerta, tipo de alerta

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

| IoTHubId                                                                                                       | AlertSeverity | displayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Ataque de força bruta foi concluída com êxito           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Falha na tentativa de início de sessão local no dispositivo  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Início de sessão local com êxito no dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Moeda criptografia Miner                     | 1          |

## <a name="security-recommendations"></a>Recomendações de segurança

Recomendações de segurança são armazenadas no _AzureSecurityOfThings.SecurityRecommendation_ tabela na área de trabalho do Log Analytics configurada para o ASC para solução de IoT.

Fornecemos um número de consultas úteis para ajudar a explorar as recomendações de segurança de início.

### <a name="sample-records"></a>Registos de exemplo

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
    
| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Descrição | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio | Ativa | Foi encontrada a regra de firewall permissiva na cadeia de entrada | Foi encontrada uma regra de firewall que contém um padrão de permissivo para uma vasta gama de endereços IP ou as portas | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio | Ativa | Foi encontrada a regra de firewall permissiva na cadeia de entrada | Foi encontrada uma regra de firewall que contém um padrão de permissivo para uma vasta gama de endereços IP ou as portas | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Resumo do dispositivo

Selecione o número de recomendações de segurança distintas de Active Directory ao IoT Hub, dispositivo, a gravidade de recomendação e tipo.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | 4   |


## <a name="next-steps"></a>Passos Seguintes

- Leia o ASC para IoT [descrição geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Compreender e explorar [ASC para alertas de IoT](concept-security-alerts.md)
- Compreender e explorar [ASC de recomendação de IoT](concept-recommendations.md)