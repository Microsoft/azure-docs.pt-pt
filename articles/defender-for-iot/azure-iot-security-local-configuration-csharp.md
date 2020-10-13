---
title: Configuração local do agente de segurança IoT (C#)
description: Saiba mais sobre o Defender para o serviço de segurança IoT, ficheiro de configuração local do agente de segurança para C#.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 13c16407481d4fa6f7d468a73051cc4945e6314e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851238"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Compreender o ficheiro de configuração local (agente C#)

O agente de segurança Defender for IoT utiliza configurações a partir de um ficheiro de configuração local.

O agente de segurança lê o ficheiro de configuração uma vez quando o agente começa a funcionar. As configurações encontradas no ficheiro de configuração local contêm tanto a configuração de autenticação como outras configurações relacionadas com o agente.

O agente de segurança C# utiliza vários ficheiros de configuração:

- **General.config** - Configurações relacionadas com o agente.
- **Authentication.config** - Configuração relacionada com autenticação (incluindo detalhes de autenticação).
- **SecurityIotInterface.config** - Configurações relacionadas com IoT.

Os ficheiros de configuração contêm a configuração predefinida. A configuração de autenticação é povoada durante a instalação do agente e as alterações no ficheiro de configuração são efetuadas quando o agente é reiniciado.

## <a name="configuration-file-location"></a>Localização do ficheiro de configuração

Para Linux:

- Os ficheiros de configuração do sistema operativo estão localizados em `/var/ASCIoTAgent` .

Para Windows:

- Os ficheiros de configuração do sistema operativo estão localizados dentro do diretório do agente de segurança.

### <a name="generalconfig-configurations"></a>configurações General.config

| Nome de configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| agentId | GUID | Identificador único do agente |
| readRemoteConfigurationTimeout | TimeSpan | Período de tempo para a configuração remota do IoT Hub. Se o agente não conseguir obter a configuração dentro do tempo especificado, a operação irá esgotar-se.|
| schedulerInterval | TimeSpan | Intervalo interno do agendador. |
| produtorInterval | TimeSpan | Intervalo de trabalhador de produtor de evento. |
| consumidorInterval | TimeSpan | Intervalo de trabalho de consumo de evento. |
| highPriorityQueueSizePercentage | 0 < número < 1 | A parte da cache total dedicada a mensagens de alta prioridade. |
| logLevel | "Off", "Fatal", "Error", "Warning", "Information", "Debug"  | As mensagens de registo iguais e acima desta gravidade são registadas para depurar a consola (Syslog in Linux). |
| fileLogLevel |  "Off", "Fatal", "Error", "Warning", "Information", "Debug"| As mensagens de registo iguais e acima desta gravidade são registadas para arquivar (Syslog in Linux). |
| diagnósticoVerbosityLevel | "Nenhum", "Alguns", "Todos", | Nível de verbosidade de eventos de diagnóstico. Nenhum - eventos de diagnóstico não são enviados. Alguns - Apenas são enviados eventos de diagnóstico com grande importância. Todos - todos os registos também são enviados como eventos de diagnóstico. |
| logFilePath | Caminho para arquivar | Se o ficheiroLogLevel > Desligado, os registos são escritos neste ficheiro. |
| defaultEventPrioridade | "Alto", "Baixo", "Fora" | Prioridade do evento predefinido. |

### <a name="generalconfig-example"></a>General.config exemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Nome de configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| nome do módulo | string | Nome da identidade do módulo de segurança. Este nome deve corresponder ao nome de identidade do módulo no dispositivo. |
| deviceId | string | ID do dispositivo (conforme registado no Azure IoT Hub). |
| schedulerInterval | Cadeia TimeSpan | Intervalo interno do agendador. |
| gatewayHostname | string | Nome anfitrião do Azure Iot Hub. Normalmente <o meu hub>.azure-devices.net |
| filePath | string - caminho para arquivar | Caminho para o ficheiro que contém o segredo de autenticação.|
| tipo | "SimétricaKey", "SelfSignedCertificate" | O segredo do utilizador para a autenticação. Escolha *o SymmetricKey* se o segredo do utilizador for uma chave simétrica, escolha *o certificado auto-assinado* se o segredo for um certificado auto-assinado. |
| identidade | "DPS", "Módulo", "Dispositivo" | Identidade de autenticação - DPS se a autenticação for feita através de DPS, Módulo se a autenticação for feita com recurso a credenciais de módulos, ou dispositivo se a autenticação for feita com recurso a credenciais do dispositivo.
| certificadoLocalizaçãoKind |  "LocalFile", "Loja" | LocalFile se o certificado for armazenado num ficheiro, guarde se o certificado estiver localizado numa loja de certificados. |
| idScope | string | ID âmbito de DPS |
| registrationId | string  | Identificação de registo de dispositivos DPS. |
|

### <a name="authenticationconfig-example"></a>Authentication.config exemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Nome de configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| Type de transporte | "Ampq" "Mqtt" | Tipo de transporte IoT Hub. |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config exemplo

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Passos seguintes

- Leia o Defender para o serviço IoT [Visão geral](overview.md)
- Saiba mais sobre o Defender for IoT [Architecture](architecture.md)
- Ativar o [Serviço](quickstart-onboard-iot-hub.md) Defender para IoT
- Leia o Defender para o serviço IoT [FAQ](resources-frequently-asked-questions.md)
- Saiba como aceder aos [dados de segurança bruta](how-to-security-data-access.md)
- Compreender [recomendações](concept-recommendations.md)
- Compreender [alertas de](concept-security-alerts.md) segurança
