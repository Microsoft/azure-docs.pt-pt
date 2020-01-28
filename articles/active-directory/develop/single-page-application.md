---
title: Aplicações de uma página única no Diretório Ativo do Azure
description: Descreve quais são as aplicações de uma só página (SPAs) e os fundamentos do fluxo de protocolo, registo e expiração do token para este tipo de aplicação.
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
ms.openlocfilehash: d956bdfe793f2e8fa3ce8ce83e03b9dae8033757
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701438"
---
# <a name="single-page-applications"></a>Aplicativos de página única

As aplicações de uma só página (SPAs) são tipicamente estruturadas como uma camada de apresentação JavaScript (frontal) que funciona no navegador, e uma extremidade traseira da Web API que funciona num servidor e implementa a lógica de negócio da aplicação. Para saber mais sobre a concessão implícita de autorização, e ajudá-lo a decidir se é certo para o seu cenário de candidatura, consulte [Understanding the OAuth2 implicit grant flow in Azure Ative Directory](v1-oauth2-implicit-grant-flow.md).

Neste cenário, quando o utilizador entra, a extremidade frontal JavaScript utiliza [a Biblioteca de Autenticação de Diretório Ativo para JavaScript (ADAL). JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) e a autorização implícita para obter um símbolo de identificação (id_token) da Azure AD. O token está em cache e o cliente anexa-o ao pedido como o token do portador ao fazer chamadas para a sua extremidade web API, que é segura usando o middleware OWIN.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicação de uma página única](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

1. O utilizador navega para a aplicação web.
1. A aplicação devolve a extremidade frontal JavaScript (camada de apresentação) ao navegador.
1. O utilizador inicia o início do início, por exemplo, clicando num link de início de sessão. O navegador envia um ponto final de autorização da AD Do Azure para solicitar um token de identificação. Este pedido inclui o ID de aplicação e o URL de resposta nos parâmetros de consulta.
1. A Azure AD valida o URL de resposta contra o URL de resposta registado que foi configurado no portal Azure.
1. O utilizador entra na página de inscrição.
1. Se a autenticação for bem sucedida, o Azure AD cria um token de identificação e devolve-o como um fragmento de URL (#) ao URL de resposta da aplicação. Para um pedido de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui reclamações sobre o utilizador e a AD Azure que são necessárias pela aplicação para validar o token.
1. O código do cliente JavaScript que funciona no navegador extrai o símbolo da resposta a utilizar na garantia de chamadas para a extremidade traseira da API web da aplicação.
1. O navegador chama a API web da aplicação de volta com o token id no cabeçalho de autorização. O serviço de autenticação Azure AD emite um símbolo de IDENTIFICAção que pode ser usado como um símbolo do portador se o recurso for o mesmo que o ID do cliente (neste caso, isso é verdade, uma vez que a Web API é o próprio backend da app).

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de [código para cenários de aplicação de uma página única](sample-v1-code.md#single-page-applications). Certifique-se de que volta frequentemente à medida que novas amostras são adicionadas com frequência.

## <a name="app-registration"></a>Registo da aplicação

* Inquilino único - Se estiver a construir uma candidatura apenas para a sua organização, deve estar registado no diretório da sua empresa utilizando o portal Azure.
* Multi-inquilino - Se estiver a construir uma aplicação que possa ser utilizada por utilizadores fora da sua organização, deve estar registada no diretório da sua empresa, mas também deve ser registada no diretório de cada organização que estará a usar a aplicação. Para disponibilizar a sua candidatura no seu diretório, pode incluir um processo de inscrição para os seus clientes que lhes permita consentir com a sua candidatura. Quando se inscreverem para a sua candidatura, serão apresentados com um diálogo que mostra as permissões que o pedido necessita e, em seguida, a opção de consentir. Dependendo das permissões necessárias, um administrador da outra organização pode ser obrigado a dar o seu consentimento. Quando o utilizador ou administrador consentir, o pedido está registado no seu diretório.

Após o registo do pedido, deve ser configurado para utilizar o protocolo de subvenção implícita OAuth 2.0. Por predefinição, este protocolo é desativado para aplicações. Para ativar o protocolo de subvenção implícita OAuth2 para a sua aplicação, edite o seu manifesto de aplicação a partir do portal Azure e detetete o valor "oauth2AllowImplicitFlow" para o verdadeiro. Para obter mais informações, consulte [manifesto do aplicativo](reference-app-manifest.md).

## <a name="token-expiration"></a>Expiração do token

A utilização do ADAL.js ajuda a:

* Refrescante um símbolo expirado
* Solicitando um sinal de acesso para chamar um recurso Web API

Após uma autenticação bem sucedida, a Azure AD escreve um cookie no navegador do utilizador para estabelecer uma sessão. Note que a sessão existe entre o utilizador e a AD Azure (não entre o utilizador e a aplicação web). Quando um símbolo expira, a ADAL.js usa esta sessão para obter silenciosamente outro símbolo. A ADAL.js usa um iFrame oculto para enviar e receber o pedido usando o protocolo de subvenção implícita OAuth. A ADAL.js também pode usar este mesmo mecanismo para obter silenciosamente fichas de acesso para outros recursos da Web API que a aplicação chama desde que estes recursos suportem a partilha de recursos de origem cruzada (CORS), estão registados no diretório do utilizador, e qualquer consentimento exigido foi dado pelo utilizador durante o sessão.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [tipos e cenários](app-types.md) de aplicação
* Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação da AD Azure
