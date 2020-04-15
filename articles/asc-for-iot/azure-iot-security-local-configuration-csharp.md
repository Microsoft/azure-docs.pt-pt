---
title: Configuração local do agente de segurança (C#)
description: Saiba mais sobre o Azure Security Center para o serviço de segurança IoT, ficheiro de configuração local do agente de segurança para C#.
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
ms.openlocfilehash: adf0d72763e0cb1892d64c68a6dce05abbf6f582
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311668"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Compreender o ficheiro de configuração local (agente C#)

O Centro de Segurança Azure para o agente de segurança IoT utiliza configurações de um ficheiro de configuração local.

O agente de segurança lê o ficheiro de configuração uma vez quando o agente começa. As configurações encontradas no ficheiro de configuração local contêm configuração de autenticação e outras configurações relacionadas com o agente.

O agente de segurança C# utiliza vários ficheiros de configuração:

- **General.config** - Configurações relacionadas com o agente.
- **Autenticação.config** - Configuração relacionada com autenticação (incluindo detalhes de autenticação).
- **SecurityIotInterface.config** - Configurações relacionadas com IoT.

Os ficheiros de configuração contêm a configuração predefinida. A configuração de autenticação é povoada durante a instalação do agente e as alterações no ficheiro de configuração são feitas quando o agente é reiniciado.

## <a name="configuration-file-location"></a>Localização do ficheiro de configuração

Para Linux:

- Os ficheiros de `/var/ASCIoTAgent`configuração do sistema operativo estão localizados em .

Para Windows:

- Os ficheiros de configuração do sistema operativo estão localizados dentro do diretório do agente de segurança.

### <a name="generalconfig-configurations"></a>Configurações Geral.config

| Nome de configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| agenteId | GUID | Identificador único agente |
| lerTempo de Configuração Remota | TimeSpan | Período de tempo para obter a configuração remota do IoT Hub. Se o agente não conseguir obter a configuração dentro do tempo especificado, a operação irá esgotar-se.|
| schedulerInterval | TimeSpan | Intervalo interno do programador. |
| produtorInterval | TimeSpan | Intervalo de trabalhador de produtor de eventos. |
| consumidorInterval | TimeSpan | Intervalo de trabalhador de consumo de eventos. |
| altaPrioridadeQueueSizePercentage | 0 número < < 1 | A parte da cache total dedicada a mensagens de alta prioridade. |
| logLevel | "Off", "Fatal", "Error", "Warning", "Information", "Debug"  | As mensagens de registo iguais e acima desta gravidade são registadas para depurar a consola (Syslog in Linux). |
| fileLogLevel |  "Off", "Fatal", "Error", "Warning", "Information", "Debug"| As mensagens de registo iguais e acima desta gravidade são registadas para arquivar (Syslog in Linux). |
| diagnósticoVerbosityLevel | "Nenhum", "alguns", "todos", | Nível de verbosidade de eventos de diagnóstico. Nenhum - eventos de diagnóstico não são enviados, Alguns - Apenas são enviados eventos de diagnóstico com grande importância, Todos - todos os registos também são enviados como eventos de diagnóstico. |
| logFilePath | Caminho para arquivar | Se o fileLogLevel > desligado, os registos são escritos neste ficheiro. |
| defaultEventPriority | "Alto", "Baixo", "Off" | Prioridade do evento padrão. |

### <a name="generalconfig-example"></a>Exemplo geral.config

```XML
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

### <a name="authenticationconfig"></a>Autenticação.config

| Nome de configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| móduloNome | string | Nome da identidade do módulo de segurança. Este nome deve corresponder ao nome de identidade do módulo no dispositivo. |
| deviceId | string | ID do dispositivo (registado no Hub Azure IoT). || schedulerInterval | Corda TimeSpan | Intervalo interno do programador. |
| gatewayHostname | string | Nome anfitrião do Hub Azure. Normalmente <my-hub>.azure-devices.net |
| filePath | cadeia - caminho para arquivar | Caminho para o ficheiro que contém o segredo de autenticação.|
| tipo | "SimétricaChave", "Auto-AssinadoCertificado" | O segredo do utilizador para a autenticação. Escolha *SymmetricKey* se o segredo do utilizador for uma chave simétrica, escolha *um certificado auto-assinado* se o segredo for um certificado auto-assinado. |
| identidade | "DPS", "Módulo", "Dispositivo" | Identidade de autenticação - DPS se a autenticação for feita através de DPS, Módulo se a autenticação for feita usando credenciais de módulo, ou dispositivo se a autenticação for feita usando credenciais do dispositivo.
| certificadoLocationKind |  "LocalFile", "Store" | LocalFile se o certificado for armazenado num ficheiro, guarde se o certificado estiver localizado numa loja de certificados. |
| idScope | string | Âmbito de id do DPS |
| registrationId | string  | Id de registo do dispositivo DPS. |
|

### <a name="authenticationconfig-example"></a>Autenticação.config exemplo

```XML
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
| transporteTipo | "Ampq" "Mqtt" | Tipo de transporte Do Hub IoT. |
|

### <a name="securityiotinterfaceconfig-example"></a>Exemplo securityIotInterface.config

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Passos seguintes

- Leia o Centro de Segurança Azure para a [visão geral](overview.md) do serviço IoT
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Ativar o Centro de Segurança Azure para [o serviço](quickstart-onboard-iot-hub.md) IoT
- Leia o Centro de Segurança Azure para o serviço IoT [FAQ](resources-frequently-asked-questions.md)
- Saiba como aceder a dados de [segurança bruta](how-to-security-data-access.md)
- Compreender [recomendações](concept-recommendations.md)
- Compreender [alertas](concept-security-alerts.md) de segurança
