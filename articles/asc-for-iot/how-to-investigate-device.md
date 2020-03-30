---
title: Guia de investigação do Centro de Segurança Azure para o IoT Microsoft Docs
description: Isto como orientar explica como usar o Azure Security Center para ioT para investigar um dispositivo IoT suspeito usando log Analytics.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596246"
---
# <a name="investigate-a-suspicious-iot-device"></a>Investigue um dispositivo ioT suspeito

Os alertas de serviço do Azure Security Center para os alertas de serviço IoT fornecem indicações claras quando os dispositivos IoT são suspeitos de envolvimento em atividades suspeitas ou quando existem indícios de que um dispositivo está comprometido. 

Neste guia, utilize as sugestões de investigação fornecidas para ajudar a determinar os riscos potenciais para a sua organização, decidir como remediar e descobrir as melhores formas de prevenir ataques semelhantes no futuro.  

> [!div class="checklist"]
> * Encontre os dados do seu dispositivo
> * Investigar usando consultas de kql


## <a name="how-can-i-access-my-data"></a>Como posso aceder aos meus dados?

Por padrão, o Azure Security Center for IoT armazena os seus alertas de segurança e recomendações no seu espaço de trabalho Log Analytics. Também pode optar por armazenar os seus dados de segurança bruta.

Para localizar o seu espaço de trabalho de Log Analytics para armazenamento de dados:

1. Abra o seu centro de IoT, 
1. Em **Segurança,** clique em **visualização geral**e, em seguida, selecione **Definições**.
1. Altere os detalhes de configuração do espaço de trabalho log Analytics. 
1. Clique em **Guardar**. 

Após a configuração, faça o seguinte para aceder aos dados armazenados no seu espaço de trabalho Log Analytics:

1. Selecione e clique num Centro de Segurança Azure para alerta IoT no seu Hub IoT. 
1. Clique em **mais investigação**. 
1. Selecione **Para ver quais os dispositivos que têm este alerta clique aqui e veja a coluna DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Passos de investigação para dispositivos IoT suspeitos

Para visualizar insights e dados brutos sobre os seus dispositivos IoT, vá ao seu espaço de trabalho log Analytics [para aceder aos seus dados](#how-can-i-access-my-data).

Veja as consultas de kql abaixo para começar com alertas de investigação e atividades no seu dispositivo.

### <a name="related-alerts"></a>Alertas relacionados

Para saber se outros alertas foram desencadeados por volta da mesma altura, utilize a seguinte consulta de kql:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Utilizadores com acesso

Para saber quais os utilizadores que têm acesso a este dispositivo, utilize a seguinte consulta kql: 

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
Utilize estes dados para descobrir: 
- Que utilizadores têm acesso ao dispositivo?
- Os utilizadores com acesso têm os níveis de permissão esperados?

### <a name="open-ports"></a>Abrir portas

Para saber quais as portas do dispositivo que estão atualmente a ser utilizadas ou utilizadas, utilize a seguinte consulta de kql: 

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

Utilize estes dados para descobrir:
- Quais as tomadas de escuta que estão atualmente ativas no dispositivo?
- Devem ser permitidas as tomadas de escuta atualmente ativas?
- Há algum endereço remoto suspeito ligado ao dispositivo?

### <a name="user-logins"></a>Logins de utilizador

Para encontrar utilizadores que iniciaram sessão no dispositivo, utilize a seguinte consulta de kql: 
 
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

Utilize os resultados da consulta para descobrir:
- Que utilizadores iniciaram sessão no dispositivo?
- Os utilizadores que iniciaram o sessão, devem fazer login?
- Os utilizadores que iniciaram sessão ligaram-se a partir de endereços IP esperados ou inesperados?
  
### <a name="process-list"></a>Lista de processos

Para saber se a lista de processos é como esperado, utilize a seguinte consulta de kql: 

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

Utilize os resultados da consulta para descobrir:

- Houve algum processo suspeito a decorrer no dispositivo?
- Os processos foram executados por utilizadores apropriados?
- Alguma execução da linha de comando continha os argumentos corretos e esperados?

## <a name="next-steps"></a>Passos seguintes

Depois de investigar um dispositivo e de obter uma melhor compreensão dos seus riscos, poderá considerar [configurar alertas personalizados](quickstart-create-custom-alerts.md) para melhorar a sua postura de segurança de solução IoT. Se ainda não tiver um agente de dispositivos, considere [implementar um agente](how-to-deploy-agent.md) de segurança ou alterar a [configuração de um agente de dispositivoexistente](how-to-agent-configuration.md) para melhorar os seus resultados. 
