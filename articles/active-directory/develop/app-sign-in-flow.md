---
title: Fluxo de entrada de aplicativos com plataforma de identidade microsoft / Azure
titleSuffix: Microsoft identity platform
description: Conheça o fluxo de entrada de aplicações web, desktop e mobile na plataforma de identidade da Microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 7b326e17611b5f4b9520d8218a28a67afe9a851a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584353"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>Fluxo de entrada de aplicativos com plataforma de identidade microsoft

Este tópico discute o fluxo básico de entrada para aplicações web, desktop e mobile usando a plataforma de identidade da Microsoft. Consulte fluxos de [autenticação e cenários](authentication-flows-app-scenarios.md) de aplicações para conhecer cenários de entrada suportados pela plataforma de identidade da Microsoft.

## <a name="web-app-sign-in-flow"></a>Fluxo de entrada de aplicativo web

Quando um utilizador navega no navegador para uma aplicação web, acontece o seguinte:

* A aplicação web determina se o utilizador é autenticado.
* Se o utilizador não for autenticado, a aplicação web delega à Azure AD para assinar no utilizador. Esse sinal de inscrição estará em conformidade com a política da organização, o que pode significar pedir ao utilizador que introduza as suas credenciais, utilizando a autenticação de vários fatores, ou não utilizando uma palavra-passe (por exemplo, utilizando o Windows Hello).
* Pede-se ao utilizador que consinta no acesso de que a aplicação do cliente necessita. É por isso que as aplicações de clientes precisam de ser registadas com a Azure AD, para que a plataforma de identidade da Microsoft possa fornecer fichas que representem o acesso que o utilizador consentiu.

Quando o utilizador tiver autenticado com sucesso:

* A plataforma de identidade da Microsoft envia um token para a aplicação web.
* Um cookie é guardado, associado ao domínio da Azure AD, que contém a identidade do utilizador no frasco de cookies do navegador. Da próxima vez que uma aplicação usar o navegador para navegar para o ponto final da plataforma de identidade da Microsoft, o navegador apresenta o cookie para que o utilizador não tenha de voltar a fazer sessão. É também assim que o SSO é alcançado. O cookie é produzido pela Azure AD e só pode ser entendido pela Azure AD.
* A aplicação web valida então o símbolo. Se a validação for bem sucedida, a aplicação web exibe a página protegida e guarda um cookie de sessão no frasco de cookies do navegador. Quando o utilizador navega para outra página, a aplicação web sabe que o utilizador é autenticado com base no cookie da sessão.

O diagrama de sequência seguinte resume esta interação:

![processo de autenticação de aplicações web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Como uma aplicação web determina se o utilizador é autenticado

Os desenvolvedores de aplicações web podem indicar se todas ou apenas determinadas páginas requerem autenticação. Por exemplo, em ASP.NET/ASP.NET Core, isto `[Authorize]` é feito adicionando o atributo às ações do controlador.

Este atributo faz com que ASP.NET verifique a presença de um cookie de sessão contendo a identidade do utilizador. Se um cookie não estiver presente, ASP.NET redireciona a autenticação para o fornecedor de identidade especificado. Se o fornecedor de identidade for Azure AD, `https://login.microsoftonline.com`a aplicação web redireciona a autenticação para , que apresenta um diálogo de entrada.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Como uma aplicação web delega iniciar sessão na plataforma de identidade da Microsoft e obtém um símbolo

A autenticação do utilizador acontece através do browser. O protocolo OpenID utiliza mensagens padrão de protocolo HTTP.

* A aplicação web envia um HTTP 302 (redirecionamento) para o navegador para usar a plataforma de identidade da Microsoft.
* Quando o utilizador é autenticado, a plataforma de identidade da Microsoft envia o token para a aplicação web utilizando um redirecionamento através do navegador.
* O redirecionamento é fornecido pela aplicação web sob a forma de um URI redirecionado. Este URI redirecionado está registado no objeto de aplicação Azure AD. Pode haver vários URIs redirecionados porque a aplicação pode ser implementada em vários URLs. Assim, a aplicação web também terá de especificar o uri redirecionamento para usar.
* A Azure AD verifica que o URI redirecionado enviado pela aplicação web é um dos URIs de redirecionamento registados para a aplicação.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Fluxo de sinal de aplicativo seleção e aplicativo móvel

O fluxo acima descrito aplica-se, com ligeiras diferenças, às aplicações de desktop e mobile.

As aplicações de desktop e mobile podem usar um controlo Web incorporado, ou um navegador de sistema, para autenticação. O diagrama seguinte mostra como uma aplicação de desktop ou móvel utiliza a biblioteca de autenticação da Microsoft (MSAL) para adquirir fichas de acesso e chamadas apis web.

![Aplicativo de desktop como parece ser](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

A MSAL usa um navegador para obter fichas. Tal como acontece com as aplicações web, a autenticação é delegada na plataforma de identidade da Microsoft.

Uma vez que o Azure AD guarda o mesmo cookie de identidade no navegador como faz para aplicações web, se a aplicação nativa ou móvel utilizar o navegador do sistema, receberá imediatamente o SSO com a aplicação web correspondente.

Por padrão, a MSAL utiliza o navegador do sistema. A exceção são as aplicações de ambiente de trabalho .NET Framework onde é utilizado um controlo incorporado para proporcionar uma experiência de utilizador mais integrada.

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos que abranjam a autenticação e os fundamentos de autorização:

* Consulte a [Autenticação vs. autorização](authentication-vs-authorization.md) para conhecer os conceitos básicos de autenticação e autorização na plataforma de identidade da Microsoft.
* Consulte [fichas](security-tokens.md) de segurança para saber como os tokens de acesso, fichas de atualização e fichas de identificação são usados na autenticação e autorização.
* Consulte o [modelo de Aplicação](application-model.md) para conhecer o processo de registo da sua aplicação para que possa integrar-se na plataforma de identidade da Microsoft.

Para saber mais sobre o fluxo de inscrição de aplicativos:

* Consulte fluxos de [autenticação e cenários](authentication-flows-app-scenarios.md) de aplicações para saber mais sobre outros cenários para autenticar utilizadores suportados pela plataforma de identidade da Microsoft.
* Consulte [as bibliotecas MSAL](msal-overview.md) para conhecer as bibliotecas da Microsoft que o ajudam a desenvolver aplicações que trabalham com contas Microsoft, contas AD Azure e utilizadores de Azure AD B2C num único modelo de programação simplificado.
