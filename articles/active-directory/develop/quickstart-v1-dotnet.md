---
title: Usuários de entrada & chamar Microsoft Graph APIs do .NET desktop (WPF) | Azure
description: Saiba como criar um aplicativo de área de trabalho do Windows .NET que se integre ao Azure AD para entrar e chame as APIs protegidas do Azure AD usando o OAuth 2,0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c35e44dff321c4a2f568855dacd827001a57f64a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703818"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Início rápido: conectar usuários e chamar a API de Microsoft Graph de um aplicativo de área de trabalho .NET (WPF)

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure Active Directory (Azure AD). Permite aos programadores criar aplicações que iniciam sessão em todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, como o Microsoft Graph, ou APIs criadas pelos programadores.

A [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma Microsoft Identity para acessar APIs da Web protegidas. O Biblioteca de Autenticação do Active Directory (ADAL) integra-se com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0), em que MSAL se integra com o ponto de extremidade da plataforma Microsoft Identity (v 2.0).

Para novos aplicativos de área de trabalho, recomendamos que você use a plataforma de identidade da Microsoft (v 2.0) e o MSAL para adquirir tokens e acessar APIs Web protegidas: [início rápido: adquirir um token e chamar Microsoft Graph API de um aplicativo de área de trabalho do Windows](quickstart-v2-windows-desktop.md)
