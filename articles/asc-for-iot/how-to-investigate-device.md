---
title: Central de segurança do Azure para guia de investigação de dispositivos IoT | Microsoft Docs
description: Este guia de instruções explica como usar a central de segurança do Azure para IoT para investigar um dispositivo IoT suspeito usando o Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596246"
---
# <a name="investigate-a-suspicious-iot-device"></a>Investigar um dispositivo IoT suspeito

A central de segurança do Azure para alertas do serviço de IoT fornece indicações claras quando os dispositivos IoT têm a suspeita de envolvimento em atividades suspeitas ou quando há indicações de que um dispositivo está comprometido. 

Neste guia, use as sugestões de investigação fornecidas para ajudar a determinar os riscos potenciais para sua organização, decidir como corrigir e descobrir as melhores maneiras de evitar ataques semelhantes no futuro.  

> [!div class="checklist"]
> * Localizar os dados do dispositivo
> * Investigar usando consultas kql


## <a name="how-can-i-access-my-data"></a>Como posso acessar meus dados?

Por padrão, a central de segurança do Azure para IoT armazena seus alertas de segurança e recomendações em seu espaço de trabalho Log Analytics. Você também pode optar por armazenar seus dados brutos de segurança.

Para localizar seu espaço de trabalho do Log Analytics para armazenamento de dados:

1. Abra o Hub IoT, 
1. Em **segurança**, clique em **visão geral**e, em seguida, selecione **configurações**.
1. Altere os detalhes de configuração do Log Analytics espaço de trabalho. 
1. Clique em **Guardar**. 

Após a configuração, faça o seguinte para acessar os dados armazenados em seu espaço de trabalho do Log Analytics:

1. Selecione e clique em um alerta da central de segurança do Azure para IoT em seu hub IoT. 
1. Clique em **investigação adicional**. 
1. Selecione **para ver quais dispositivos têm este alerta clique aqui e exiba a coluna**DeviceID.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Etapas de investigação para dispositivos IoT suspeitos

Para exibir informações e dados brutos sobre seus dispositivos IoT, acesse o espaço de trabalho Log Analytics [para acessar seus dados](#how-can-i-access-my-data).

Consulte as consultas kql de exemplo abaixo para começar a investigar alertas e atividades em seu dispositivo.

### <a name="related-alerts"></a>Alertas relacionados

Para descobrir se outros alertas foram disparados ao mesmo tempo, use a seguinte consulta kql:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Usuários com acesso

Para descobrir quais usuários têm acesso a esse dispositivo, use a seguinte consulta kql: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Use estes dados para descobrir: 
- Quais usuários têm acesso ao dispositivo?
- Os usuários com acesso têm os níveis de permissão esperados?

### <a name="open-ports"></a>Abrir portas

Para descobrir quais portas no dispositivo estão atualmente em uso ou foram usadas, use a seguinte consulta kql: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Use estes dados para descobrir:
- Quais soquetes de escuta estão ativos no momento no dispositivo?
- Os soquetes de escuta que estão ativos atualmente devem ser permitidos?
- Há algum endereço remoto suspeito conectado ao dispositivo?

### <a name="user-logins"></a>Logons de usuário

Para localizar os usuários que fizeram logon no dispositivo, use a seguinte consulta kql: 
 
 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Use os resultados da consulta para descobrir:
- Quais usuários fizeram logon no dispositivo?
- Os usuários que fizeram logon devem fazer logon?
- Os usuários que fizeram logon se conectavam a partir de endereços IP esperados ou inesperados?
  
### <a name="process-list"></a>Lista de processos

Para descobrir se a lista de processos é como esperado, use a seguinte consulta kql: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Use os resultados da consulta para descobrir:

- Há algum processo suspeito em execução no dispositivo?
- Os processos foram executados pelos usuários apropriados?
- As execuções de linha de comando contêm os argumentos corretos e esperados?

## <a name="next-steps"></a>Passos Seguintes

Depois de investigar um dispositivo e obter uma compreensão melhor dos seus riscos, convém considerar a [configuração de alertas personalizados](quickstart-create-custom-alerts.md) para melhorar a postura de segurança da solução de IOT. Se você ainda não tiver um agente de dispositivo, considere [implantar um agente de segurança](how-to-deploy-agent.md) ou [alterar a configuração de um agente de dispositivo existente](how-to-agent-configuration.md) para melhorar os resultados. 
