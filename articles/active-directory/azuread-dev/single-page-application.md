---
title: Aplicações de uma página única no Azure Ative Directory
description: Descreve o que são aplicações de uma página (SPAs) e o básico sobre o fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154751"
---
# <a name="single-page-applications"></a>Aplicações de uma página única

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

As aplicações de uma só página (SPAs) são tipicamente estruturadas como uma camada de apresentação JavaScript (frontal) que funciona no navegador, e uma parte traseira da API web que funciona num servidor e implementa a lógica de negócio da aplicação. Para saber mais sobre a concessão implícita de autorização, e ajudá-lo a decidir se é certo para o seu cenário de candidatura, consulte [compreender o fluxo de subvenção implícita OAuth2 no Diretório Ativo Azure](v1-oauth2-implicit-grant-flow.md).

Neste cenário, quando o utilizador assina, a extremidade frontal javaScript utiliza [a Biblioteca de Autenticação de Diretório Ativo para JavaScript (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) e o subsídio de autorização implícita para obter um token de ID (id_token) da Azure AD. O token é em cache e o cliente anexa-o ao pedido como símbolo do portador ao fazer chamadas para a sua parte traseira da Web API, que é segura usando o middleware OWIN.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicação de uma página](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

1. O utilizador navega para a aplicação web.
1. A aplicação devolve a extremidade frontal JavaScript (camada de apresentação) ao navegador.
1. O utilizador inicia o sing-in, por exemplo, clicando num link de inscrição. O navegador envia um GET para o ponto final de autorização AD Azure para solicitar um token de ID. Este pedido inclui o ID de aplicação e URL de resposta nos parâmetros de consulta.
1. A Azure AD valida o URL de resposta com o URL de resposta registado que foi configurado no portal Azure.
1. O utilizador assina na página de inscrição.
1. Se a autenticação for bem sucedida, a Azure AD cria um token de ID e devolve-o como fragmento de URL (#) ao URL de resposta da aplicação. Para um pedido de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui reclamações sobre o utilizador e a AD Azure que são necessárias pela aplicação para validar o token.
1. O código de cliente JavaScript que está a ser utilizado no navegador extrai o símbolo da resposta à utilização na obtenção de chamadas para o final de fundo da API web da aplicação.
1. O navegador chama a API web da aplicação de volta com o token de ID no cabeçalho de autorização. O serviço de autenticação AZure AD emite um token de ID que pode ser usado como símbolo portador se o recurso for o mesmo que o ID do cliente (neste caso, isso é verdade, uma vez que a API web é o próprio backend da app).

## <a name="code-samples"></a>Exemplos de código

Consulte as [amostras de código para cenários de aplicação de uma só página](sample-v1-code.md#single-page-applications). Certifique-se de que volta com frequência à medida que novas amostras são adicionadas com frequência.

## <a name="app-registration"></a>Registo da aplicação

* Inquilino único - Se estiver a construir uma aplicação apenas para a sua organização, deve estar registada no diretório da sua empresa utilizando o portal Azure.
* Multi-inquilino - Se estiver a construir uma aplicação que pode ser usada por utilizadores fora da sua organização, deve estar registada no diretório da sua empresa, mas também deve estar registada no diretório de cada organização que utilizará a aplicação. Para disponibilizar a sua candidatura no seu diretório, pode incluir um processo de inscrição para os seus clientes que lhes permite consentir a sua aplicação. Quando se inscreverem para a sua aplicação, serão apresentados com um diálogo que mostra as permissões que a aplicação requer e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador da outra organização pode ser obrigado a dar o seu consentimento. Quando o utilizador ou administrador consente, o pedido é registado no seu diretório.

Após o registo do pedido, deve ser configurado para utilizar o protocolo de concessão implícito OAuth 2.0. Por predefinição, este protocolo é desativado para aplicações. Para ativar o protocolo de concessão implícita OAuth2 para a sua aplicação, edite o seu manifesto de aplicação a partir do portal Azure e decrete o valor "oauth2AllowImplicitFlow" para verdadeiro. Para mais informações, consulte [o manifesto de aplicação.](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

## <a name="token-expiration"></a>Expiração simbólica

A utilização de ADAL.js ajuda:

* Refrescante um símbolo expirado
* Solicitando um token de acesso para chamar um recurso web API

Após uma autenticação bem sucedida, a Azure AD escreve um cookie no browser do utilizador para estabelecer uma sessão. Note que existe sessão entre o utilizador e a Azure AD (não entre o utilizador e a aplicação web). Quando um símbolo expira, ADAL.js usa esta sessão para obter silenciosamente outro token. ADAL.js usa um iFrame oculto para enviar e receber o pedido usando o protocolo de concessão implícita OAu. ADAL.js também podem usar este mesmo mecanismo para obter silenciosamente fichas de acesso para outros recursos da API web que a aplicação chama, desde que estes recursos suportem a partilha de recursos de origem cruzada (CORS), estejam registados no diretório do utilizador, e qualquer consentimento necessário foi dado pelo utilizador durante o início de sação.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [tipos e cenários de aplicações](app-types.md)
* Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação AD do Azure
