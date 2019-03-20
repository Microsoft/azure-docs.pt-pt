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
ms.date: 03/20/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: bb28862ad6452eab3130eeb2dc0b4c269839d306
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203195"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chamar o Microsoft Graph API a partir de uma aplicação de ambiente de trabalho do Windows

Este guia demonstra como um aplicativo .NET de Desktop do Windows (XAML) nativo pode obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que precisam de tokens de acesso de um ponto de final de v2.0 do Azure Active Directory.

Quando concluir o guia, seu aplicativo poderá chamar uma API protegida que utilize contas pessoais (incluindo o outlook.com, live.com e outros). O aplicativo também utilizar o trabalho e escolares contas a partir de qualquer empresa ou organização que utiliza o Azure Active Directory.  

> [!NOTE]
> O guia requer o Visual Studio 2015 Update 3 ou o Visual Studio 2017. Não tem qualquer uma dessas versões? [Transfira gratuitamente o Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de exemplo gerada por este guia

![Mostra como a aplicação de exemplo gerado por este funciona de tutoriais](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks-updated.png)

O aplicativo de exemplo que criou com este guia permite que uma aplicação de ambiente de trabalho do Windows que consulta a Graph API da Microsoft ou uma API Web que aceita tokens de um ponto de final de v2.0 do Azure Active Directory. Para este cenário, é possível adicionar um token a pedidos de HTTP com o cabeçalho de autorização. Biblioteca de autenticação da Microsoft (MSAL) processa a aquisição do token e renovação.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Lidar com a aquisição do token para aceder ao protegidos APIs da Web

Depois do utilizador é autenticado, o aplicativo de exemplo recebe um token que pode ser usado para consultar o Graph API da Microsoft ou uma API Web que está protegida pelo Azure Active Directory v2.

APIs, como o Microsoft Graph exigem um token para permitir o acesso a recursos específicos. Por exemplo, um token é necessário para ler o perfil de um utilizador, aceder ao calendário de um utilizador ou enviar mensagem de e-mail. Seu aplicativo pode solicitar um token de acesso através de MSAL para aceder a estes recursos, especificando os âmbitos da API. Esse token de acesso, em seguida, é adicionada ao cabeçalho de autorização de HTTP para cada chamada feita em relação ao recurso protegido.

A MSAL gere colocação em cache e atualizar os tokens de acesso para si, para que seu aplicativo não precisa.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|
