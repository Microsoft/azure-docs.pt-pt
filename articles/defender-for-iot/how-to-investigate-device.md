---
title: Investigue um dispositivo suspeito
description: Esta forma de orientar explica como usar o Defender para ioT para investigar um dispositivo IoT suspeito usando o Log Analytics.
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 32cc8d82a867ead533cbaa6802bffb4494398412
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782015"
---
# <a name="investigate-a-suspicious-iot-device"></a>Investigue um dispositivo IoT suspeito

Os alertas de serviço do Defender para IoT fornecem indicações claras quando os dispositivos IoT são suspeitos de envolvimento em atividades suspeitas ou quando existem indícios de que um dispositivo está comprometido.

Neste guia, utilize as sugestões de investigação fornecidas para ajudar a determinar os riscos potenciais para a sua organização, decida como remediar e descubra as melhores formas de prevenir ataques semelhantes no futuro.

> [!div class="checklist"]
> * Encontre os dados do seu dispositivo
> * Investigue usando consultas de kql

## <a name="how-can-i-access-my-data"></a>Como posso aceder aos meus dados?

Por predefinição, o Defender para IoT armazena os seus alertas de segurança e recomendações no seu espaço de trabalho Log Analytics. Também pode optar por armazenar os seus dados de segurança bruto.

Para localizar o seu espaço de trabalho Log Analytics para armazenamento de dados:

1. Abra o seu hub IoT,
1. Em **Segurança**, selecione **Definições** e, em seguida, selecione **Data Collection**.
1. Altere os detalhes da configuração do espaço de trabalho do Log Analytics.
1. Selecione **Guardar**.

Seguinte configuração, faça o seguinte para aceder aos dados armazenados no seu espaço de trabalho Log Analytics:

1. Selecione e selecione num alerta Defender para IoT no seu Hub IoT.
1. Selecione **mais investigação**.
1. **Selecione Para ver quais os dispositivos que têm este clique de alerta aqui e ver a coluna DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Etapas de investigação para dispositivos IoT suspeitos

Para visualizar insights e dados brutos sobre os seus dispositivos IoT, vá ao seu espaço de trabalho Log Analytics [para aceder aos seus dados.](#how-can-i-access-my-data)

Consulte as consultas de kql de amostra abaixo para começar com alertas e atividades de investigação no seu dispositivo.

### <a name="related-alerts"></a>Alertas relacionados

Pode descobrir se outros alertas foram desencadeados por volta da mesma hora através da seguinte consulta kql:

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

Para saber quais as portas do dispositivo que estão atualmente a ser utilizadas ou foram utilizadas, utilize a seguinte consulta kql:

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

- Que tomadas de escuta estão atualmente ativas no dispositivo?
- Devem ser permitidas as tomadas de escuta atualmente ativas?
- Existem endereços remotos suspeitos ligados ao dispositivo?

### <a name="user-logins"></a>Logins de utilizadores

Para encontrar utilizadores que iniciaram sessão no dispositivo, utilize a seguinte consulta kql:

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

- Que utilizadores se inscreveram no dispositivo?
- Os utilizadores que se inscreveram, devem entrar?
- Os utilizadores que assinaram em ligação a partir de endereços IP esperados ou inesperados?

### <a name="process-list"></a>Lista de processos

Para saber se a lista de processos é como esperado, utilize a seguinte consulta kql:

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

- Havia algum processo suspeito a decorrer no dispositivo?
- Os processos foram executados por utilizadores apropriados?
- Alguma execução de linha de comando continha os argumentos corretos e esperados?

## <a name="next-steps"></a>Passos seguintes

Depois de investigar um dispositivo e de obter uma melhor compreensão dos seus riscos, talvez deva considerar [configurar alertas personalizados](quickstart-create-custom-alerts.md) para melhorar a sua postura de segurança de solução IoT. Se ainda não tiver um agente de dispositivo, considere [implementar um agente](how-to-deploy-agent.md) de segurança ou alterar a [configuração de um agente de dispositivo existente](how-to-agent-configuration.md) para melhorar os seus resultados.
