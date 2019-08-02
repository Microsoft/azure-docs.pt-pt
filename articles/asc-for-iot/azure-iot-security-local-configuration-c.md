---
title: Noções básicas sobre a central de segurança do Azure para configurações locais do agente para C | Microsoft Docs
description: Saiba mais sobre a central de segurança do Azure para configurações locais do agente para C.
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
ms.openlocfilehash: 2725a824da26dafcbc215e4c302ec38ad4b5a699
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600543"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Noções básicas sobre o agente de arquivo LocalConfiguration. JSON-C

A central de segurança do Azure para o agente de segurança de IoT usa configurações de um arquivo de configuração local.
O agente de segurança lê a configuração uma vez, na inicialização do agente.
A configuração encontrada no arquivo de configuração local contém a configuração de autenticação e outras configurações relacionadas ao agente.
O arquivo contém configurações em pares de "chave-valor" na notação JSON e as configurações são preenchidas quando o agente é instalado. 

Por padrão, o arquivo está localizado em:/var/ASCIoTAgent/LocalConfiguration.json

As alterações feitas no arquivo de configuração ocorrem quando o agente é reiniciado. 

## <a name="security-agent-configurations-for-c"></a>Configurações do agente de segurança para C
| Nome da Configuração | Valores possíveis | Detalhes | 
|:-----------|:---------------|:--------|
| AgentId | GUID | O identificador exclusivo do agente |
| TriggerdEventsInterval | Cadeia de caracteres ISO8601 | Intervalo do Agendador para coleta de eventos disparados |
| ConnectionTimeout | Cadeia de caracteres ISO8601 | Período de tempo antes que a conexão com o IoThub receba tempo limite |
| Authentication | JsonObject | Configuração de autenticação. Este objeto contém todas as informações necessárias para a autenticação no IoTHub |
| identidade | "DPS", "SecurityModule", "dispositivo" | Identidade de autenticação – DPS se a autenticação for feita por meio do DPS, SecurityModule se a autenticação for feita por meio de credenciais do módulo de segurança ou dispositivo se a autenticação for feita com as credenciais do dispositivo |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | o segredo do usuário para autenticação-escolha SasToken se o segredo de uso for uma chave simétrica, escolha certificado autoassinado se o segredo for um certificado autoassinado  |
| FilePath | Caminho para o arquivo (cadeia de caracteres) | Caminho para o arquivo que contém o segredo de autenticação |
| nome de anfitrião | cadeia | O nome do host do Hub IOT do Azure. geralmente < meu Hub >. Azure-devices.net |
| DeviceId | Cadeia de caracteres | A ID do dispositivo (como registrado no Hub IoT do Azure) |
| DPS | JsonObject | Configurações relacionadas a DPS |
| IDScope | cadeia | Escopo da ID do DPS |
| RegistrationId | Cadeia de caracteres  | ID de registro do dispositivo DPS |
| Registo | JsonObject | Configurações relacionadas ao agente de log |
| SystemLoggerMinimumSeverity | 0 < = número < = 4 | mensagens de log iguais e superiores a essa gravidade serão registradas em/var/log/syslog (0 é a severidade mais baixa) |
| DiagnosticEventMinimumSeverity | 0 < = número < = 4 | as mensagens de log iguais e superiores a essa gravidade serão enviadas como eventos de diagnóstico (0 é a severidade mais baixa) |

## <a name="security-agent-configurations-code-example"></a>Exemplo de código de configurações do agente de segurança
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
- Leia a [visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba mais sobre a [arquitetura](architecture.md) da central de segurança do Azure para IOT
- Habilitar a central de segurança do Azure para o [serviço](quickstart-onboard-iot-hub.md) de IOT
- Leia as [perguntas frequentes sobre](resources-frequently-asked-questions.md) a central de segurança do Azure para o serviço IOT
- Saiba como acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entender as [recomendações](concept-recommendations.md)
- Entender os [alertas](concept-security-alerts.md) de segurança