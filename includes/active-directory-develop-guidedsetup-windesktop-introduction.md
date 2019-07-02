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
ms.openlocfilehash: 301abe95b245603e5414eef84ce74cdc8de01d19
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509904"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chamar o Microsoft Graph API a partir de uma aplicação de ambiente de trabalho do Windows

Este guia demonstra como um aplicativo nativo do .NET de Desktop do Windows (XAML) usa um token de acesso para chamar a API do Microsoft Graph. A aplicação também pode aceder a outras APIs que precisam de tokens de acesso de uma plataforma de identidade da Microsoft para o ponto final de v2.0 de desenvolvedores. Esta plataforma foi anteriormente denominada Azure AD.

Quando concluir o guia, seu aplicativo poderá chamar uma API protegida que utilize contas pessoais (incluindo o outlook.com, live.com e outros). O aplicativo também utilizar o trabalho e escolares contas a partir de qualquer empresa ou organização que utiliza o Azure Active Directory.  

> [!NOTE]
> O guia requer o Visual Studio 2015 Update 3, Visual Studio 2017 ou Visual Studio 2019. Não tem qualquer uma dessas versões? [Transfira gratuitamente o Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de exemplo gerada por este guia

![Mostra como funciona a aplicação de exemplo gerada por este tutorial](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

O aplicativo de exemplo que criou com este guia permite que uma aplicação de ambiente de trabalho do Windows que consulta a Graph API da Microsoft ou uma API Web que aceita tokens de um ponto de extremidade de plataforma de identidade Microsoft. Para este cenário, é possível adicionar um token a pedidos de HTTP com o cabeçalho de autorização. Biblioteca de autenticação da Microsoft (MSAL) processa a aquisição do token e renovação.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Lidar com a aquisição do token para aceder ao protegidos APIs da Web

Depois do utilizador é autenticado, o aplicativo de exemplo recebe um token que pode utilizar para consultar o Graph API da Microsoft ou uma API Web que está protegida pela plataforma de identidade da Microsoft para desenvolvedores.

APIs, como o Microsoft Graph exigem um token para permitir o acesso a recursos específicos. Por exemplo, um token é necessário para ler o perfil de um utilizador, aceder ao calendário de um utilizador ou enviar mensagem de e-mail. Seu aplicativo pode solicitar um token de acesso através de MSAL para aceder a estes recursos, especificando os âmbitos da API. Esse token de acesso, em seguida, é adicionada ao cabeçalho de autorização de HTTP para cada chamada feita em relação ao recurso protegido.

A MSAL gere colocação em cache e atualizar os tokens de acesso para si, para que seu aplicativo não precisa.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de autenticação da Microsoft (MSAL.NET)|
