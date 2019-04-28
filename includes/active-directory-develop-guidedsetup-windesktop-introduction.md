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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f0cc888eaf3724737e9c868c69a641094a19348c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60297687"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chamar o Microsoft Graph API a partir de uma aplicação de ambiente de trabalho do Windows

Este guia demonstra como um nativo Windows Desktop .NET (XAML) aplicativo pode obter um acesso de token e chamar a API do Microsoft Graph ou outras APIs que necessitam de acesso tokens a partir de uma plataforma de identidade da Microsoft para o ponto final de v2.0 desenvolvedores (anteriormente conhecido como com o nome do Azure AD).

Quando concluir o guia, seu aplicativo poderá chamar uma API protegida que utilize contas pessoais (incluindo o outlook.com, live.com e outros). O aplicativo também utilizar o trabalho e escolares contas a partir de qualquer empresa ou organização que utiliza o Azure Active Directory.  

> [!NOTE]
> O guia requer o Visual Studio 2015 Update 3 ou o Visual Studio 2017. Não tem qualquer uma dessas versões? [Transfira gratuitamente o Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de exemplo gerada por este guia

![Mostra como a aplicação de exemplo gerado por este funciona de tutoriais](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

O aplicativo de exemplo que criou com este guia permite que uma aplicação de ambiente de trabalho do Windows que consulta a Graph API da Microsoft ou uma API Web que aceita tokens de um ponto de extremidade de plataforma de identidade Microsoft. Para este cenário, é possível adicionar um token a pedidos de HTTP com o cabeçalho de autorização. Biblioteca de autenticação da Microsoft (MSAL) processa a aquisição do token e renovação.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Lidar com a aquisição do token para aceder ao protegidos APIs da Web

Depois do utilizador é autenticado, o aplicativo de exemplo recebe um token que pode ser usado para consultar o Graph API da Microsoft ou uma API Web que está protegida pela plataforma de identidade da Microsoft para desenvolvedores.

APIs, como o Microsoft Graph exigem um token para permitir o acesso a recursos específicos. Por exemplo, um token é necessário para ler o perfil de um utilizador, aceder ao calendário de um utilizador ou enviar mensagem de e-mail. Seu aplicativo pode solicitar um token de acesso através de MSAL para aceder a estes recursos, especificando os âmbitos da API. Esse token de acesso, em seguida, é adicionada ao cabeçalho de autorização de HTTP para cada chamada feita em relação ao recurso protegido.

A MSAL gere colocação em cache e atualizar os tokens de acesso para si, para que seu aplicativo não precisa.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de autenticação da Microsoft (MSAL.NET)|
