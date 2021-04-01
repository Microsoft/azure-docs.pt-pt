---
title: Fluxo de entrada de aplicativos com a plataforma de identidade microsoft | Rio Azure
titleSuffix: Microsoft identity platform
description: Conheça o fluxo de entrada de aplicações web, desktop e mobile na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1f9f330ab140fa66b5a66a112c47ca2a68ba56bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755705"
---
# <a name="app-sign-in-flow-with-the-microsoft-identity-platform"></a>Fluxo de entrada de aplicativos com a plataforma de identidade da Microsoft

Este tópico discute o fluxo básico de entrada para aplicações web, desktop e mobile usando a plataforma de identidade da Microsoft. Consulte [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicações para conhecer os cenários de entrada suportados pela plataforma de identidade da Microsoft.

## <a name="web-app-sign-in-flow"></a>Fluxo de entrada de aplicativo web

Quando um utilizador navega no navegador para uma aplicação web, o seguinte acontece:

* A aplicação web determina se o utilizador está autenticado.
* Se o utilizador não for autenticado, a aplicação web delega no Azure AD para assinar no utilizador. Esse sinal será conforme com a política da organização, o que pode significar pedir ao utilizador que introduza as suas credenciais, utilizando [a autenticação multi-factor](../authentication/concept-mfa-howitworks.md) (por vezes designada como autenticação de dois fatores ou 2FA), ou não utilizar uma palavra-passe (por exemplo, utilizando o Windows Hello).
* Pede-se ao utilizador que consinta no acesso de que a app do cliente necessita. É por isso que as aplicações dos clientes precisam de ser registadas no AZure AD, para que a plataforma de identidade da Microsoft possa entregar fichas que representem o acesso a que o utilizador consentiu.

Quando o utilizador tiver autenticado com sucesso:

* A plataforma de identidade da Microsoft envia um símbolo para a aplicação web.
* Um cookie é guardado, associado ao domínio do Azure AD, que contém a identidade do utilizador no frasco de cookies do navegador. Da próxima vez que uma aplicação utilizar o navegador para navegar para o ponto final de autorização da plataforma de identidade da Microsoft, o navegador apresenta o cookie para que o utilizador não tenha de iniciar novamente o sação. É também assim que se consegue o SSO. O cookie é produzido pela Azure AD e só pode ser entendido pela Azure AD.
* A aplicação web valida então o token. Se a validação for bem sucedida, a aplicação web exibe a página protegida e guarda um cookie de sessão no frasco de cookies do navegador. Quando o utilizador navega para outra página, a aplicação web sabe que o utilizador é autenticado com base no cookie da sessão.

O seguinte diagrama de sequência resume esta interação:

![processo de autenticação de aplicativos web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Como uma aplicação web determina se o utilizador é autenticado

Os desenvolvedores de aplicações web podem indicar se todas ou apenas determinadas páginas requerem autenticação. Por exemplo, no ASP.NET/ASP.NET Core, isto é feito adicionando o `[Authorize]` atributo às ações do controlador.

Este atributo faz com que ASP.NET verifique a presença de um cookie de sessão que contenha a identidade do utilizador. Se um cookie não estiver presente, ASP.NET redireciona a autenticação para o fornecedor de identidade especificado. Se o fornecedor de identidade for Azure AD, a aplicação web redireciona a autenticação para `https://login.microsoftonline.com` , que exibe um diálogo de inscrição.

### <a name="how-a-web-app-delegates-sign-in-to-the-microsoft-identity-platform-and-obtains-a-token"></a>Como uma aplicação web delega o acesso à plataforma de identidade da Microsoft e obtém um token

A autenticação do utilizador acontece através do browser. O protocolo OpenID utiliza mensagens de protocolo HTTP padrão.

* A aplicação web envia um HTTP 302 (redirecionamento) para o navegador para usar a plataforma de identidade da Microsoft.
* Quando o utilizador é autenticado, a plataforma de identidade da Microsoft envia o token para a aplicação web utilizando um redirecionamento através do navegador.
* O redirecionamento é fornecido pela aplicação web sob a forma de um URI de redirecionamento. Este URI de redirecionamento está registado com o objeto de aplicação Azure AD. Pode haver vários URIs de redirecionamento porque a aplicação pode ser implementada em vários URLs. Assim, a aplicação web também terá de especificar o URI de redirecionamento para usar.
* A Azure AD verifica que o redirecionamento URI enviado pela aplicação web é um dos URIs de redirecionamento registados para a aplicação.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Fluxo de entrada de aplicativos de ambiente de trabalho e aplicativo móvel

O fluxo acima descrito aplica-se, com ligeiras diferenças, às aplicações de ambiente de trabalho e móveis.

Aplicações de desktop e mobile podem usar um controlo Web incorporado, ou um navegador de sistema, para autenticação. O diagrama seguinte mostra como um desktop ou aplicativo móvel usa a biblioteca de autenticação microsoft (MSAL) para adquirir tokens de acesso e chamadas de APIs web.

![Aplicativo de ambiente de trabalho como parece ser](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

A MSAL usa um navegador para obter fichas. Tal como acontece com as aplicações web, a autenticação é delegada na plataforma de identidade da Microsoft.

Como o Azure AD guarda o mesmo cookie de identidade no navegador como para aplicações web, se a aplicação nativa ou móvel utilizar o navegador do sistema, obterá imediatamente SSO com a aplicação web correspondente.

Por padrão, o MSAL utiliza o navegador do sistema. A exceção são aplicações de ambiente de trabalho .NET Framework onde um controlo incorporado é usado para proporcionar uma experiência de utilizador mais integrada.

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos que abranjam os fundamentos básicos de autenticação e autorização:

* Consulte [a autenticação vs. autorização](authentication-vs-authorization.md) para conhecer os conceitos básicos de autenticação e autorização na plataforma de identidade da Microsoft.
* Consulte [fichas de segurança](security-tokens.md) para saber como os tokens de acesso, tokens de atualização e fichas de identificação são usados na autenticação e autorização.
* Consulte o [modelo de Aplicação](application-model.md) para saber mais sobre o processo de registo da sua aplicação para que possa integrar-se com a plataforma de identidade da Microsoft.

Para saber mais sobre o fluxo de inscrição de aplicativos:

* Consulte [fluxos de autenticação e cenários de aplicações](authentication-flows-app-scenarios.md) para saber mais sobre outros cenários para autenticar utilizadores suportados pela plataforma de identidade microsoft.
* Consulte [as bibliotecas MSAL](msal-overview.md) para aprender sobre as bibliotecas da Microsoft que o ajudam a desenvolver aplicações que funcionam com contas microsoft, contas AD AZure e utilizadores AD B2C todos num único modelo de programação simplificado.
