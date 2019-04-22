---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 86113246fab399e8364653198e9a6971317e3f2c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59505811"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Adicionar início de sessão com a Microsoft a uma aplicação web ASP.NET

Este guia demonstra como implementar o início de sessão com a Microsoft com uma solução ASP.NET MVC tradicional baseada no browser e aplicações web utilizando OpenID Connect.

No final deste guia, seu aplicativo será capaz de aceitar inícios de sessão de contas pessoais (incluindo o outlook.com, live.com e outros), bem como o trabalho contas escolares ou profissionais de qualquer empresa ou organização que está integrado com o Azure Active Directory.

> Este guia requer o Visual Studio 2017.  Não o tem?  [Transferir o Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de exemplo gerada por este guia

![Mostra como a aplicação de exemplo gerado por este funciona de tutoriais](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

O aplicativo de exemplo criado por este guia baseia-se no cenário em que um utilizador usa o browser para aceder a um web site do ASP.NET a pedir um utilizador seja autenticado por meio de um botão de início de sessão. Neste cenário, a maior parte do trabalho para compor a página Web ocorre do lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia utiliza as seguintes bibliotecas:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que uma aplicação utilize o OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite que uma aplicação mantenha a sessão de utilizador com cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permite que as aplicações com base em OWIN sejam executados nos IIS através do pipeline de pedidos do ASP.NET|
