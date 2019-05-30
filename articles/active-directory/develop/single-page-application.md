---
title: Aplicativos de página única no Azure Active Directory
description: Descreve quais aplicativos de página única (SPAs) são e as noções básicas sobre fluxo de protocolo, registo e expiração do token para este tipo de aplicação.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f6f66779bec9ed4e38e5a662c2d3728ba2034b6
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545303"
---
# <a name="single-page-applications"></a>Aplicativos de página única

Página única (SPAs aplicativos) são estruturados geralmente como uma camada de apresentação de JavaScript (front-end) que é executado no navegador e um web API back-end que é executado num servidor e implementa a lógica comercial do aplicativo. Para obter mais informações sobre a concessão de autorização implícita e ajudar a decidir se é adequada para o seu cenário de aplicação, veja [Noções básicas sobre o OAuth2 implícita conceder o fluxo no Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

Neste cenário, quando o utilizador inicia sessão, o JavaScript de front-end utiliza [Active Directory Authentication Library para JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) e a concessão de autorização implícita para obter um token de ID (id_token) do Azure AD. O token é colocado em cache e o cliente liga-ao pedido que o token de portador ao efetuar chamadas para a sua API Web back-end, que está protegida com o middleware da OWIN.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicação de página única](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

1. O utilizador navega para a aplicação web.
1. A aplicação devolve o front-end JavaScript (camada de apresentação) para o navegador.
1. O utilizador inicia sessão, por exemplo, clicando num link de início de sessão. O browser envia um GET para o ponto final de autorização do Azure AD para pedir um token de ID. Este pedido inclui o URL de ID e a resposta do aplicativo nos parâmetros de consulta.
1. O Azure AD valida o URL de resposta contra o URL de resposta registado, que foi configurada no portal do Azure.
1. O utilizador inicia sessão na página de início de sessão.
1. Se a autenticação for bem-sucedida, cria um token de ID do Azure AD e o retorna como um fragmento de URL (#) para o URL de resposta do aplicativo. Para uma aplicação de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui declarações sobre o utilizador e o Azure AD, que são necessários pelo aplicativo para validar o token.
1. O código de cliente JavaScript em execução no browser extrai o token da resposta a utilizar na proteção de chamadas para web da aplicação que API volta termina.
1. O navegador chama web da aplicação API volta terminar com o token de ID no cabeçalho de autorização. O serviço de autenticação do Azure AD emite um token de ID que pode ser utilizado como um token de portador, se o recurso é o mesmo que o ID de cliente (neste caso, isso é verdadeiro como a API web é o back-end da aplicação).

## <a name="code-samples"></a>Exemplos de código

Consulte a [exemplos de código para cenários de aplicação de página única](sample-v1-code.md#single-page-applications). Certifique-se de que verifique novamente com a frequência com novos exemplos são adicionados com frequência.

## <a name="app-registration"></a>Registo de aplicações

* Inquilino único, se estiver a criar uma aplicação apenas para a sua organização, tem de ser registado no diretório da sua empresa com o portal do Azure.
* Multi-inquilino - se estiver criando um aplicativo que pode ser utilizado por utilizadores fora da sua organização, ela deve ser registrada no diretório da sua empresa, mas também tem de estar registrada no diretório de cada organização que irá utilizar a aplicação. Para disponibilizar a aplicação no seu diretório, pode incluir um processo de inscrição para os seus clientes, que permite autorizar a aplicação. Quando se inscrevem no seu aplicativo, serão apresentadas com uma caixa de diálogo que mostra as permissões que a aplicação requer e, em seguida, a opção para dar consentimento. Consoante as permissões necessárias, um administrador da outra organização poderá ser necessário para dar consentimento. Quando o utilizador ou administrador autorizar, o aplicativo está registado no seu diretório.

Depois de registar a aplicação, tem de ser configurado para utilizar o protocolo de concessão implícita OAuth 2.0. Por predefinição, este protocolo está desabilitado para aplicativos. Para ativar o protocolo de concessão implícita OAuth2 para a sua aplicação, editar o manifesto da aplicação do portal do Azure e defina o valor de "oauth2AllowImplicitFlow" como true. Para mais informações, veja [manifesto do aplicativo](reference-app-manifest.md).

## <a name="token-expiration"></a>Expiração do token

Usar ADAL.js ajuda com:

* atualizar um token expirado
* pedir um token de acesso para chamar um recurso de API web

Após uma autenticação com êxito, o Azure AD escreve um cookie no navegador do usuário para estabelecer uma sessão. Tenha em atenção de que a sessão existe entre o utilizador e o Azure AD (não entre o utilizador e a aplicação web). Quando um token expira, ADAL.js utiliza esta sessão silenciosamente obter outro token. ADAL.js utiliza um iFrame oculto para enviar e receber a solicitação usando o protocolo de concessão implícita OAuth. ADAL.js também pode utilizar esse mesmo mecanismo para obter silenciosamente os tokens de acesso para outros recursos de web API, o aplicativo chama, desde que esses recursos suportam os recursos de várias origens (CORS), de partilha são registadas no diretório do usuário, e qualquer consentimento necessário foi especificado pelo utilizador durante o início de sessão.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [tipos de aplicativos e cenários](app-types.md)
* Saiba mais sobre o Azure AD [Noções básicas de autenticação](authentication-scenarios.md)
