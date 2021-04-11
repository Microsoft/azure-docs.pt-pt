---
title: Equipas que cumprem interoperabilidade
titleSuffix: An Azure Communication Services concept document
description: Junte reuniões de equipas
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cf6553cd7c59febd19f9654e31188f127b8eb065
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276761"
---
# <a name="teams-interoperability"></a>Interoperabilidade com o Teams

[!INCLUDE [Public Preview](../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Para permitir/desativar [a interoperabilidade do inquilino das equipas,](../concepts/teams-interop.md)preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Os Serviços de Comunicação Azure podem ser usados para construir experiências de reunião personalizadas que interagem com as Equipas da Microsoft. Os utilizadores da sua solução de Serviços de Comunicação podem interagir com os participantes das Equipas em vez de voz, vídeo, chat e partilha de ecrãs.

A interoperabilidade das equipas permite criar aplicações personalizadas que ligam os utilizadores às reuniões das Equipas. Os utilizadores das suas aplicações personalizadas não precisam de ter identidades do Azure Ative Directory ou licenças de Equipas para experimentar esta capacidade. Isto é ideal para reunir funcionários (que podem estar familiarizados com equipas) e utilizadores externos (utilizando uma experiência de aplicação personalizada) numa experiência de reunião perfeita. Por exemplo:

1. Funcionários usam equipas para agendar uma reunião 
1. Os detalhes da reunião são partilhados com utilizadores externos através da sua aplicação personalizada.
   * **Usando API de gráfico** A sua aplicação de Serviços de Comunicação personalizada utiliza as APIs do Microsoft Graph para aceder aos detalhes da reunião a partilhar. 
   * **Usando outras opções** Por exemplo, o seu link de reunião pode ser copiado do seu calendário em Equipas microsoft.
1. Os utilizadores externos utilizam a sua aplicação personalizada para se juntarem à reunião das Equipas (através dos Serviços de Comunicação Que Ligam e Chat SDKs)

A arquitetura de alto nível para este caso de uso é assim: 

![Arquitetura para Equipas interop](./media/call-flows/teams-interop.png)

Enquanto certas funcionalidades de reunião de equipas, como a mão levantada, o modo em conjunto e as salas de fuga só estarão disponíveis para os utilizadores das Equipas, a sua aplicação personalizada terá acesso às capacidades de áudio, vídeo, chat e partilha de ecrãs da reunião. O chat de reuniões será acessível ao utilizador de aplicações personalizadas enquanto estiverem na chamada. Não poderão enviar ou receber mensagens antes de se juntarem ou depois de deixarem a chamada. 

Quando um utilizador dos Serviços de Comunicação se juntar à reunião de Equipas, o nome de exibição fornecido através do Call SDK será mostrado aos utilizadores das Equipas. O utilizador dos Serviços de Comunicação será tratado como um utilizador anónimo em Equipas.  A sua aplicação personalizada deve considerar a autenticação do utilizador e outras medidas de segurança para proteger as reuniões das Equipas. Esteja atento às implicações de segurança de permitir que utilizadores anónimos se juntem às reuniões e utilize o [guia de segurança das Equipas](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) para configurar as capacidades disponíveis para utilizadores anónimos.

Equipas de Serviços de Comunicação Interop está atualmente em pré-visualização privada. Quando geralmente disponíveis, os utilizadores dos Serviços de Comunicação serão tratados como "utilizadores de acesso externo". Saiba mais sobre o acesso externo em [Call, chat e colabore com pessoas fora da sua organização em Microsoft Teams.](/microsoftteams/communicate-with-users-from-other-organizations)

Os utilizadores dos Serviços de Comunicação podem participar em reuniões agendadas de equipas desde que as junções anónimas estejam ativadas nas definições de [reunião](/microsoftteams/meeting-settings-in-teams). Se a reunião estiver agendada para um canal, os utilizadores dos Serviços de Comunicação não poderão aderir ao chat ou enviar e receber mensagens.

## <a name="teams-in-government-clouds-gcc"></a>Equipas em Nuvens do Governo (GCC)
A interoperabilidade dos Serviços de Comunicação Azure não é compatível com as implementações das Equipas usando [as nuvens governamentais do Microsoft 365 (GCC)](/MicrosoftTeams/plan-for-government-gcc) neste momento. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Associe a sua aplicação de chamadas a uma reunião do Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
