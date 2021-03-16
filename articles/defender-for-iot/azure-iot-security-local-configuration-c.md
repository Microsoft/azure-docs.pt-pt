---
title: Configuração local do agente de segurança (C)
description: Saiba mais sobre o Defender para configurações locais de agente para C.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 161bc008076de2bba62cd65c6c9bb106f648aa47
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493242"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Compreender o ficheiro LocalConfiguration.json - Agente C

O agente de segurança Defender for IoT utiliza configurações a partir de um ficheiro de configuração local.
O agente de segurança lê a configuração uma vez, no arranque do agente.
A configuração encontrada no ficheiro de configuração local contém configuração de autenticação e outras configurações relacionadas com o agente.
O ficheiro contém configurações em pares "Valor-Chave" na notação JSON e as configurações são povoadas quando o agente é instalado.

Por predefinição, o ficheiro está localizado em: /var/ASCIoTAgent/LocalConfiguration.jsem

As alterações no ficheiro de configuração ocorrem quando o agente é reiniciado.

## <a name="security-agent-configurations-for-c"></a>Configurações de agente de segurança para C

| Nome de configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| AgentId | GUID | O identificador único do agente |
| TriggerdEventsInterval | Corda ISO8601 | Intervalo de programador para recolha de eventos desencadeados |
| Intervalo de conexão | Corda ISO8601 | Período de tempo antes da ligação ao IoThub ser cronometrado |
| Autenticação | JsonObject | Configuração de autenticação. Este objeto contém todas as informações necessárias para a autenticação contra o IoTHub |
| Identidade | "DPS", "SecurityModule", "Dispositivo" | Identidade de autenticação - DPS se a autenticação for feita através de DPS, SecurityModule se a autenticação for feita através de defender-ioT-micro-agentes ou dispositivo se a autenticação for feita com credenciais do Dispositivo |
| AutenticaçãoMethod | "SasToken", "SelfSignedCertificate" | o segredo do utilizador para a autenticação - Escolha SasToken se o segredo de uso for uma chave simétrica, escolha certificado auto-assinado se o segredo for um certificado auto-assinado  |
| FilePath | Caminho para arquivar (cadeia) | Caminho para o arquivo que contém o segredo de autenticação |
| Nome do Anfitrião | string | O nome anfitrião do hub Azure IoT. geralmente <meu hub>.azure-devices.net |
| DeviceId | string | O ID do dispositivo (conforme registado no Azure IoT Hub) |
| DPS | JsonObject | Configurações relacionadas com dPS |
| IDScope | string | ID âmbito de DPS |
| RegistroD | string  | ID de registo de dispositivos DPS |
| Registo | JsonObject | Configurações relacionadas com o agente logger |
| SystemLoggerMinimumSeverity | 0 <= número <= 4 | as mensagens de registo iguais e acima desta gravidade serão registadas para /var/log/syslog (0 é a menor gravidade) |
| DiagnósticoEventMinimumSeverity | 0 <= número <= 4 | registar mensagens iguais e acima desta gravidade será enviada como eventos de diagnóstico (0 é a menor gravidade) |

## <a name="security-agent-configurations-code-example"></a>Exemplo de código de configurações de agente de segurança

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- Leia o Defender para o serviço IoT [Visão geral](overview.md)
- Saiba mais sobre o Defender for IoT [Architecture](architecture.md)
- Ativar o [Serviço](quickstart-onboard-iot-hub.md) Defender para IoT
- Leia o Defender para o serviço IoT [FAQ](resources-frequently-asked-questions.md)
- Saiba como aceder aos [dados de segurança bruta](how-to-security-data-access.md)
- Compreender [recomendações](concept-recommendations.md)
- Compreender [alertas de](concept-security-alerts.md) segurança
