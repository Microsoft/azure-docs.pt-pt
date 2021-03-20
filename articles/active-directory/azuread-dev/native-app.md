---
title: Aplicativos nativos em Azure Ative Directory
description: Descreve o que são as aplicações nativas e o básico no fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154802"
---
# <a name="native-apps"></a>Aplicações nativas

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

As aplicações nativas são aplicações que chamam uma API web em nome de um utilizador. Este cenário baseia-se no tipo de concessão de código de autorização OAuth 2.0 com um cliente público, conforme descrito na secção 4.1 da [especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749). A aplicação nativa obtém um token de acesso para o utilizador utilizando o protocolo OAuth 2.0. Este token de acesso é então enviado no pedido para a API web, que autoriza o utilizador e devolve o recurso pretendido.

## <a name="diagram"></a>Diagrama

![Aplicação nativa para diagrama de API web](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

Se estiver a utilizar as Bibliotecas de Autenticação AD, a maioria dos detalhes do protocolo descritos abaixo são tratados por si, como o pop-up do navegador, o caching token e o manuseamento de tokens de atualização.

1. Utilizando um pop-up de navegador, a aplicação nativa faz um pedido para o ponto final de autorização em Azure AD. Este pedido inclui o ID de aplicação e o URI redirecionado da aplicação nativa, como mostrado no portal Azure, e o ID URI de aplicação para a API web. Se o utilizador ainda não se ter inscrito, é solicitado que volte a assinar.
1. A Azure AD autentica o utilizador. Se for um pedido multi-inquilino e o consentimento for necessário para usar a aplicação, o utilizador será obrigado a consentir se ainda não o fez. Após a concessão de consentimento e após a autenticação bem sucedida, a Azure AD emite uma resposta de código de autorização de volta ao URI redirecionado da aplicação do cliente.
1. Quando a Azure AD emite uma resposta de código de autorização de volta ao URI redirecionado, a aplicação do cliente para a interação do navegador e extrai o código de autorização da resposta. Utilizando este código de autorização, a aplicação do cliente envia um pedido ao ponto final simbólico da Azure AD que inclui o código de autorização, detalhes sobre a aplicação do cliente (ID de aplicação e redirecionamento URI), e o recurso pretendido (ID URI de aplicação para a API web).
1. O código de autorização e informações sobre a aplicação do cliente e a API web são validados pela Azure AD. Após a validação bem sucedida, a Azure AD devolve dois tokens: um token de acesso JWT e um token de atualização JWT. Além disso, a Azure AD devolve informações básicas sobre o utilizador, tais como o seu nome de exibição e iD do inquilino.
1. Em HTTPS, a aplicação do cliente utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Portador" no cabeçalho de autorização do pedido à API web. A API web valida então o token JWT, e se a validação for bem sucedida, devolve o recurso pretendido.
1. Quando o token de acesso expirar, a aplicação do cliente receberá um erro que indica que o utilizador precisa de autenticar novamente. Se a aplicação tiver um token de atualização válido, pode ser usado para adquirir um novo token de acesso sem pedir ao utilizador que volte a iniciar sedu. Se o token de atualização expirar, a aplicação terá de autenticar interativamente o utilizador mais uma vez.

> [!NOTE]
> O token de atualização emitido pela Azure AD pode ser usado para aceder a vários recursos. Por exemplo, se tiver uma aplicação de cliente que tenha permissão para chamar duas APIs web, o token de atualização pode ser usado para obter um token de acesso à outra API web também.

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para os cenários native Application to Web API. E, verifique com frequência, adicionamos novas amostras frequentemente. [Aplicação nativa para a API Web.](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api)

## <a name="app-registration"></a>Registo de aplicações

Para registar uma aplicação no ponto final Azure AD v1.0, consulte [registar uma aplicação](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Inquilino único - Tanto a aplicação nativa como a API web devem ser registadas no mesmo diretório em Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso da aplicação nativa aos seus recursos. A aplicação do cliente seleciona então as permissões desejadas do menu de entrega "Permissões a Outras Aplicações" no portal Azure.
* Multi-inquilino - Em primeiro lugar, a aplicação nativa só se registou no programador ou diretório da editora. Em segundo lugar, a aplicação nativa está configurada para indicar as permissões que necessita para ser funcional. Esta lista de permissões necessárias é mostrada num diálogo quando um utilizador ou administrador no diretório de destino dá consentimento à aplicação, o que a coloca à disposição da sua organização. Algumas aplicações apenas requerem permissões ao nível do utilizador, que qualquer utilizador da organização pode consentir. Outras aplicações requerem permissões ao nível do administrador, que um utilizador na organização não pode consentir. Apenas um administrador de diretório pode dar consentimento a aplicações que requerem este nível de permissões. Quando o utilizador ou administrador consente, apenas a API web está registada no seu diretório. 

## <a name="token-expiration"></a>Expiração simbólica

Quando a aplicação nativa utiliza o seu código de autorização para obter um token de acesso JWT, também recebe um token de atualização JWT. Quando o token de acesso expirar, o token de atualização pode ser usado para reautenticar o utilizador sem que os exija que voltem a fazer o seu sucção. Esta ficha de atualização é então usada para autenticar o utilizador, o que resulta num novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos e cenários de aplicações](app-types.md)
- Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação AD do Azure
