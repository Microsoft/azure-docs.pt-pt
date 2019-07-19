---
title: Daemon do .NET Core da plataforma Microsoft Identity | Microsoft Docs
description: Saiba como criar um aplicativo de daemon do .NET que se integre ao Azure AD chama APIs protegidas do Azure AD usando o OAuth 2,0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f56f3f65919c4d1d9b59f7d0f67522a935579d64
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327186"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-an-apps-identity"></a>Início rápido: Adquirir um token e chamar Microsoft Graph API de um aplicativo de console usando a identidade de um aplicativo

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure Active Directory (Azure AD). Permite aos programadores criar aplicações que iniciam sessão em todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, como o Microsoft Graph, ou APIs criadas pelos programadores.

A [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma Microsoft Identity para acessar APIs da Web protegidas. O Biblioteca de Autenticação do Active Directory (ADAL) integra-se com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0), em que MSAL se integra com o ponto de extremidade da plataforma Microsoft Identity (v 2.0).

Para novos aplicativos de daemon .NET, recomendamos que você use a plataforma de identidade da Microsoft (v 2.0) e o MSAL para adquirir tokens e acessar APIs da Web protegidas: [Quickstart: Adquira um token e chame Microsoft Graph API de um aplicativo de console usando a identidade](quickstart-v2-netcore-daemon.md)de um aplicativo.
