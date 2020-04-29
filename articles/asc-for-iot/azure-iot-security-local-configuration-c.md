---
title: Configuração local do agente de segurança (C)
description: Conheça o Azure Security Center para configurações locais de agente para C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311696"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Compreender o ficheiro LocalConfiguration.json - Agente C

O Centro de Segurança Azure para o agente de segurança IoT utiliza configurações de um ficheiro de configuração local.
O agente de segurança lê a configuração uma vez, na start-up do agente.
A configuração encontrada no ficheiro de configuração local contém configuração de autenticação e outras configurações relacionadas com o agente.
O ficheiro contém configurações em pares "Key-Value" na notação JSON e as configurações são povoadas quando o agente é instalado.

Por predefinição, o ficheiro está localizado em: /var/ASCIoTAgent/LocalConfiguration.json

As alterações ao ficheiro de configuração ocorrem quando o agente é reiniciado.

## <a name="security-agent-configurations-for-c"></a>Configurações do agente de segurança para C

| Nome de configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| AgenteId | GUID | O identificador unique do agente |
| TriggerdEventsInterval | Cadeia ISO8601 | Intervalo de programadores para recolha de eventos desencadeados |
| Tempo de ligação | Cadeia ISO8601 | Período de tempo antes da ligação ao IoThub ser cronometrado |
| Autenticação | JsonObject | Configuração de autenticação. Este objeto contém todas as informações necessárias para a autenticação contra o IoTHub |
| Identidade | "DPS", "SecurityModule", "Device" | Identidade de autenticação - DPS se a autenticação for efetuada através de DPS, SecurityModule se a autenticação for efetuada através de credenciais de módulo de segurança ou dispositivo se a autenticação for feita com credenciais do Dispositivo |
| Método de Autenticação | "SasToken", "Auto-SignedCertificate" | o segredo do utilizador para a autenticação - Escolha SasToken se o segredo de utilização for uma chave simétrica, escolha um certificado auto-assinado se o segredo for um certificado auto-assinado  |
| Caminho de Arquivo | Caminho para arquivar (corda) | Caminho para o arquivo que contém o segredo de autenticação |
| NomedeAnfitrião | string | O nome anfitrião do centro azul. geralmente <meu hub>.azure-devices.net |
| DeviceId | string | A identificação do dispositivo (registada no Hub Azure IoT) |
| DPS | JsonObject | Configurações relacionadas com DPS |
| IDScope | string | Âmbito de id do DPS |
| RegistroId | string  | Id de registo de dispositivodPS DPS |
| Registo | JsonObject | Configurações relacionadas com o agente logger |
| SystemLoggerMinimumSeveridade | 0 <= número <= 4 | mensagens de registo iguais e acima desta gravidade serão registadas para /var/log/syslog (0 é a severidade mais baixa) |
| DiagnosticEventMinimumSeveridade | 0 <= número <= 4 | mensagens de registo iguais e acima desta gravidade serão enviadas como eventos de diagnóstico (0 é a severidade mais baixa) |

## <a name="security-agent-configurations-code-example"></a>Exemplo de código de configurações de agentes de segurança

```JSON
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

- Leia o Centro de Segurança Azure para a [visão geral](overview.md) do serviço IoT
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Ativar o Centro de Segurança Azure para [o serviço](quickstart-onboard-iot-hub.md) IoT
- Leia o Centro de Segurança Azure para o serviço IoT [FAQ](resources-frequently-asked-questions.md)
- Saiba como aceder a dados de [segurança bruta](how-to-security-data-access.md)
- Compreender [recomendações](concept-recommendations.md)
- Compreender [alertas](concept-security-alerts.md) de segurança
