---
title: Compilar uma API Web .NET que se integra com o Azure AD para autenticação e autorização | Microsoft Docs
description: Como compilar uma API Web MVC .NET que se integra com o Azure AD para autenticação e autorização.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38728bbeb4376e1584b3cb22c9a93c4fb63efc1d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321079"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Início rápido: Compilar uma API Web .NET que se integre ao Azure AD para autenticação e autorização

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure Active Directory (Azure AD). Permite aos programadores criar aplicações que iniciam sessão em todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, como o Microsoft Graph, ou APIs criadas pelos programadores.

A [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma Microsoft Identity para acessar APIs da Web protegidas. O Biblioteca de Autenticação do Active Directory (ADAL) integra-se com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0), em que MSAL se integra com o ponto de extremidade da plataforma Microsoft Identity (v 2.0).

Para novas APIs Web, recomendamos que você use a plataforma de identidade da Microsoft (v 2.0) e a MSAL para adquirir tokens e acessar APIs da Web protegidas: [Quickstart: Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
