---
title: Obter token & chamar Microsoft Graph APIs de aplicativos de console (v 1.0) | Azure
description: Criar um aplicativo de daemon do .NET que se integre ao Azure AD & chama APIs protegidas do Azure AD usando o OAuth 2,0
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2858a0b14863195ee0f56c89fe2c55c9e63873de
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965930"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-api-with-console-apps-identity-v10"></a>Início rápido: adquirir o token e chamar Microsoft Graph API com a identidade do aplicativo de console (v 1.0) 

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure Active Directory (Azure AD). Permite aos programadores criar aplicações que iniciam sessão em todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, como o Microsoft Graph, ou APIs criadas pelos programadores.

A [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma Microsoft Identity para acessar APIs da Web protegidas. O Biblioteca de Autenticação do Active Directory (ADAL) integra-se com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0), em que MSAL se integra com o ponto de extremidade da plataforma Microsoft Identity (v 2.0).

## <a name="next-steps"></a>Passos seguintes

Para novos aplicativos de daemon .NET, recomendamos que você use a plataforma de identidade da Microsoft (v 2.0) e o MSAL para adquirir tokens e acessar APIs Web protegidas: [início rápido: adquirir um token e chamar Microsoft Graph API de um aplicativo de console usando a identidade de um aplicativo](quickstart-v2-netcore-daemon.md).
