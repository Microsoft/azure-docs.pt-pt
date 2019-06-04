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
ms.openlocfilehash: 46247d42837f8ac181d33216d2b93d28e2533c09
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482320"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Adicionar início de sessão com a Microsoft a uma aplicação web ASP.NET

Este guia demonstra como implementar o início de sessão com a Microsoft com uma solução ASP.NET MVC tradicional baseada no browser e aplicações web utilizando OpenID Connect.

No final deste guia, seu aplicativo será capaz de aceitar inícios de sessão de contas pessoais, por exemplo outlook.com, live.com e outros. Estas contas também incluem o trabalho contas escolares ou profissionais de qualquer empresa ou organização que está integrado com o Azure Active Directory.

> Este guia requer o Visual Studio 2019.  Não o tem?  [Transfira gratuitamente o Visual Studio 2019](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de exemplo gerada por este guia

![Mostra como funciona a aplicação de exemplo gerada por este tutorial](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

O aplicativo de exemplo que cria baseia-se o cenário em que utiliza o browser para aceder a um web site ASP.NET que solicite um utilizador seja autenticado por meio de um botão de início de sessão. Neste cenário, a maior parte do trabalho para compor a página Web ocorre do lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia utiliza as seguintes bibliotecas:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que uma aplicação utilize o OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite que uma aplicação mantenha a sessão de utilizador com cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permite que as aplicações com base em OWIN sejam executados nos IIS através do pipeline de pedidos do ASP.NET|
