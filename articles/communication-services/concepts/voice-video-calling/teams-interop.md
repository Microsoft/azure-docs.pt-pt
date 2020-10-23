---
title: Equipas que cumprem interoperabilidade
titleSuffix: An Azure Communication Services concept document
description: Junte reuniões de equipas
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a7e2240e3f74f82186827ec82bb1aa39a5b93f6c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123839"
---
# <a name="teams-interoperability"></a>Interoperabilidade das equipas

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Os Serviços de Comunicação Azure podem ser usados para construir experiências de reunião personalizadas que interagem com as Equipas da Microsoft. Os utilizadores da sua solução de Serviços de Comunicação podem interagir com os participantes das Equipas em vez da partilha de voz, vídeo e ecrã.

Esta interoperabilidade permite-lhe criar aplicações Azure personalizadas que ligam os utilizadores às reuniões das Equipas. Os utilizadores das suas aplicações personalizadas não precisam de ter identidades do Azure Ative Directory ou licenças de Equipas para experimentar esta capacidade. Isto é ideal para reunir funcionários (que podem estar familiarizados com equipas) e utilizadores externos (utilizando uma experiência de aplicação personalizada) numa experiência de reunião perfeita. Isto permite-lhe construir experiências semelhantes às seguintes:

1. Funcionários usam equipas para agendar uma reunião
2. A sua aplicação de Serviços de Comunicação personalizada utiliza as APIs do Microsoft Graph para aceder a detalhes da reunião
3. Os detalhes da reunião são partilhados com utilizadores externos através da sua aplicação personalizada
4. Os utilizadores externos utilizam a sua aplicação personalizada para se juntarem à reunião das Equipas (através da biblioteca de clientes dos Serviços de Comunicação)

A arquitetura de alto nível para este caso de uso é assim: 

![Arquitetura para Equipas interop](..//media/call-flows/teams-interop.png)

Enquanto certas funcionalidades de reunião de equipas, como a mão levantada, o modo em conjunto e as salas de fuga só estarão disponíveis para os utilizadores das Equipas, a sua aplicação personalizada terá acesso às capacidades de áudio, vídeo e partilha de ecrãs do encontro.

Quando um utilizador dos Serviços de Comunicação se juntar à reunião de Equipas, o nome de exibição fornecido através da biblioteca do cliente Call será mostrado aos utilizadores das Equipas. O utilizador dos Serviços de Comunicação será tratado como um utilizador anónimo em Equipas. A sua aplicação personalizada deve considerar a autenticação do utilizador e outras medidas de segurança para proteger as reuniões das Equipas. Esteja atento às implicações de segurança de permitir que utilizadores anónimos se juntem às reuniões e utilize o [guia de segurança das Equipas](https://docs.microsoft.com/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) para configurar as capacidades disponíveis para utilizadores anónimos.

Os utilizadores dos Serviços de Comunicação podem participar em reuniões agendadas de equipas desde que as junções anónimas estejam ativadas nas definições de [reunião](https://docs.microsoft.com/microsoftteams/meeting-settings-in-teams).



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Junte-se ao seu aplicativo de chamada para uma reunião de equipas](../../quickstarts/voice-video-calling/get-started-teams-interop.md)
