---
title: Compilar uma API Web .NET do Azure AD para autenticação & autorização | Microsoft Docs
description: Como compilar uma API Web MVC .NET que se integra com o Azure AD para autenticação e autorização.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b080ce3869bcdbbde9724a33433e5f0659b37e4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703835"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Início Rápido: compilar uma API Web .NET que se integra com o Azure AD para autenticação e autorização

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure Active Directory (Azure AD). Permite aos programadores criar aplicações que iniciam sessão em todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, como o Microsoft Graph, ou APIs criadas pelos programadores.

A [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma Microsoft Identity para acessar APIs da Web protegidas. O Biblioteca de Autenticação do Active Directory (ADAL) integra-se com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0), em que MSAL se integra com o ponto de extremidade da plataforma Microsoft Identity (v 2.0).

Para novas APIs Web, recomendamos que você use a plataforma de identidade da Microsoft (v 2.0) e o MSAL para adquirir tokens e acessar APIs Web protegidas: [início rápido: Adicionar entrada com a Microsoft a um aplicativo web ASP.net](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
