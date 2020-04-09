---
title: Construa uma API web que chama APIs web - plataforma de identidade microsoft / Azure
description: Aprenda a construir uma API web que chama APIs web a jusante (visão geral).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 88a0177755fbd913bdaaf0ecf3e12c62dee294c1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885077"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Cenário: Uma API web que chama APIs web

Saiba o que precisa de saber para construir uma API web que chama APIs web.

## <a name="prerequisites"></a>Pré-requisitos

Este cenário, no qual uma API web protegida chama APIs web, baseia-se no cenário "Proteger uma API web". Para saber mais sobre este cenário fundacional, consulte [Cenário: API web protegida](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Descrição geral

- Um cliente de aplicação web, desktop, mobile ou de uma página única (não representado no diagrama de acompanhamento) chama uma API web protegida e fornece um token portador da Web Token (JWT) no seu cabeçalho HTTP "Authorization".
- A API web protegida valida o símbolo e utiliza o método `AcquireTokenOnBehalfOf` Microsoft Authentication Library (MSAL) para solicitar outro símbolo do Azure Ative Directory (Azure AD) para que a API da web protegida possa chamar uma segunda API web, ou API web a jusante, em nome do utilizador.
- A API da web `AcquireTokenSilent`protegida também pode ligar mais tarde para solicitar tokens para outras APIs a jusante em nome do mesmo utilizador. `AcquireTokenSilent`refresca o símbolo quando necessário.

![Diagrama de uma API web chamando uma API web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Especificidades

A parte de registo da aplicação que está relacionada com permissões DaPI é clássica. A configuração da aplicação envolve a utilização do fluxo OAuth 2.0 On-Behalf-Of para trocar o símbolo do portador jWT contra um símbolo para uma API a jusante. Este token é adicionado à cache simbólica, onde está disponível nos controladores da Web API, e pode então adquirir um símbolo silenciosamente para chamar APIs a jusante.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-web-api-call-api-app-registration.md)
