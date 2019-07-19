---
title: Adicionar início de sessão com a Microsoft a uma aplicação Web ASP.NET | Microsoft Docs
description: Saiba como adicionar a entrada da Microsoft em uma solução ASP.NET com um aplicativo tradicional baseado em navegador da Web usando o OpenID Connect Standard.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddf2b310a4faa3b13b7402fb67aeb3d1312ac4ac
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324539"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Início rápido: Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure Active Directory (Azure AD). Permite aos programadores criar aplicações que iniciam sessão em todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, como o Microsoft Graph, ou APIs criadas pelos programadores.

A [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma Microsoft Identity para acessar APIs da Web protegidas. O Biblioteca de Autenticação do Active Directory (ADAL) integra-se com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0), em que MSAL se integra com o ponto de extremidade da plataforma Microsoft Identity (v 2.0).

Para novos aplicativos Web, recomendamos que você use a plataforma de identidade da Microsoft (v 2.0) e o MSAL para adquirir tokens e acessar APIs da Web protegidas: [Quickstart: Adicione a entrada com a Microsoft a um aplicativo](quickstart-v2-aspnet-webapp.md)Web ASP.net.