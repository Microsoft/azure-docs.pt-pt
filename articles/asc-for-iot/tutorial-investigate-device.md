---
title: ASC para o tutorial de investigação de dispositivo do IoT pré-visualização | Documentos da Microsoft
description: Este artigo explica como utilizar o ASC para IoT para investigar um dispositivo de IoT suspeito com o Log Analytics.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 5459c5a18742b92b77866241212f21c11d7851c9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541582"
---
# <a name="tutorial-investigate-a-suspicious-iot-device"></a>Tutorial: Investigar um dispositivo de IoT suspeito

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC para os alertas de serviço de IoT e evidências fornecem indicações claras quando os dispositivos IoT suspeitas de envolvimento em atividades suspeitas ou quando indicações existem que um dispositivo estiver comprometido. 

Neste tutorial, utilize as sugestões de investigação fornecidas para o ajudar a determinar os riscos potenciais para a sua organização, decida como pretende remediar e descubra melhores formas de impedir ataques semelhantes no futuro.  

> [!div class="checklist"]
> * Localizar os dados de dispositivo
> * Investigar através de consultas de kql


## <a name="where-can-i-access-my-data"></a>Onde posso acessar o meus dados

Por predefinição, o ASC para IoT armazena os alertas de segurança e as recomendações na sua área de trabalho do Log Analytics. Também pode optar por armazenar os dados de segurança bruto.

Para localizar a sua área de trabalho do Log Analytics para o armazenamento de dados:

1. Abra o hub IoT, 
1. Clique em **Security**, em seguida, selecione **definições**.
1. Altere os detalhes de configuração de área de trabalho do Log Analytics. 
1. Clique em **Guardar**. 

Após a configuração, efetue o seguinte procedimento para acessar dados armazenados na sua área de trabalho do Log Analytics:

1. Selecione e clique num ASC para alerta de IoT no seu IoT Hub. 
1. Clique em **ainda mais investigação**. 
1. Selecione **para ver quais os dispositivos que têm este alerta, clique aqui e ver a coluna de DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Passos de investigação para dispositivos de IoT suspeitos

Para aceder a informações e dados não processados sobre os seus dispositivos IoT, aceda à sua área de trabalho do Log Analytics [onde posso aceder a dados do meu](#where-can-i-access-my-data).

Verifique e investigar os dados do dispositivo para os seguintes detalhes e atividades usando as seguintes consultas de kql:

- Para saber se outros alertas foram acionados em torno do mesmo uso de tempo a seguinte consulta kql:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

- Para saber quais os utilizadores que têm acesso a esta utilização de dispositivo a seguinte consulta kql: 

  ~~~
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
  ~~~
Utilize estes dados para detetar: 
  1. Os utilizadores que têm acesso ao dispositivo?
  2. Os utilizadores com acesso tem os níveis de permissão esperados? 

- Para saber como os utilizadores conseguem aceder ao dispositivo, utilize a seguinte consulta de kql:

  ~~~
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
  ~~~

    Utilize estes dados para detetar:
  1. Os sockets de escuta estão atualmente ativos no dispositivo?
  2. Os sockets de escuta que estão atualmente devem estar ativos?

- Para saber quem com sessão iniciada dispositivo, utilize a seguinte consulta de kql: 
 
  ~~~
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
  ~~~

    Utilize os resultados da consulta para detetar:
  1. Quais os utilizadores com sessão iniciada no dispositivo?
  2. Os utilizadores que iniciou sessão ligar a partir de endereços IP esperados ou inesperados?
  
- Para saber se o dispositivo está se comportando como esperado, utilize a seguinte consulta de kql:

  ~~~
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
  ~~~

    Utilize os resultados da consulta para detetar:

  1. Havia nele qualquer processo suspeito em execução no dispositivo?
  2. Processos foram executados por usuários apropriados?
  3. Quaisquer execuções de linha de comandos continha os argumentos corretos e esperados?

## <a name="next-steps"></a>Passos Seguintes
Depois de investigar um dispositivo e a obtenção de uma melhor compreensão sobre os seus riscos, poderá querer considerar [configurar alertas personalizados](quickstart-create-custom-alerts.md) para melhorar a sua postura de segurança da solução de IoT. Se ainda não tiver um agente de dispositivo, considere [implementar um agente de segurança](select-deploy-agent.md) ou [alteração da configuração de um agente de dispositivo existentes](concept-agent-configuration.md) para melhorar os resultados. 
 