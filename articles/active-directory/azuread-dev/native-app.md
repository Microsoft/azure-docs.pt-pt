---
title: Aplicativos nativos em Diretório Ativo Azure
description: Descreve quais são as aplicações nativas e o básico sobre fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: e65a31192be8b79720ea15d1721fbf37908a8cbe
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164425"
---
# <a name="native-apps"></a>Aplicativos nativos

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplicações nativas são aplicações que chamam uma API web em nome de um utilizador. Este cenário baseia-se no tipo de concessão de código de autorização OAuth 2.0 com um cliente público, conforme descrito na secção 4.1 da [especificação OAuth 2.0.](https://tools.ietf.org/html/rfc6749) A aplicação nativa obtém um sinal de acesso para o utilizador utilizando o protocolo OAuth 2.0. Este token de acesso é então enviado no pedido para a Web API, que autoriza o utilizador e devolve o recurso pretendido.

## <a name="diagram"></a>Diagrama

![Aplicação nativa do diagrama da Web API](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

Se estiver a utilizar as Bibliotecas de Autenticação AD, a maioria dos detalhes do protocolo descritos abaixo são tratados para si, como o pop-up do navegador, o cache de token e o manuseamento de fichas de atualização.

1. Utilizando um pop-up de navegador, a aplicação nativa faz um pedido para o ponto final de autorização em Azure AD. Este pedido inclui o ID de aplicação e o uri redirecionamento da aplicação nativa, como mostrado no portal Azure, e o ID de aplicação URI para a Web API. Se o utilizador ainda não assinou, é solicitado a assinar novamente
1. A Azure AD autentica o utilizador. Se for um pedido multi-inquilino e o consentimento for necessário para usar a aplicação, o utilizador será obrigado a consentir se ainda não o tiver feito. Após a concessão do consentimento e após a autenticação bem sucedida, a Azure AD emite uma resposta de código de autorização de volta ao redirecionamento URI da aplicação cliente.
1. Quando a Azure AD emite uma resposta de código de autorização de volta ao URI redirecionado, a aplicação do cliente para a interação do navegador e extrai o código de autorização da resposta. Utilizando este código de autorização, o pedido do cliente envia um pedido ao ponto final simbólico da Azure AD que inclui o código de autorização, detalhes sobre a aplicação do cliente (ID de aplicação e redirecionamento URI), e o recurso pretendido (ID de aplicação URI para o Web API).
1. O código de autorização e informações sobre a aplicação do cliente e a Web API são validados pela Azure AD. Após uma validação bem sucedida, a Azure AD devolve duas fichas: um símbolo de acesso JWT e um token de atualização JWT. Além disso, a Azure AD devolve informações básicas sobre o utilizador, tais como o nome de exibição e identificação do inquilino.
1. Ao longo do HTTPS, a aplicação do cliente utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Bearer" no cabeçalho de Autorização do pedido à Web API. A Web API valida então o símbolo JWT, e se a validação for bem sucedida, devolve o recurso desejado.
1. Quando o token de acesso expirar, a aplicação do cliente receberá um erro que indica que o utilizador precisa de autenticar novamente. Se a aplicação tiver um token de atualização válido, pode ser usado para adquirir um novo sinal de acesso sem pedir ao utilizador que volte a iniciar sessão. Se o token de atualização expirar, a aplicação terá de autenticar interactivamente o utilizador mais uma vez.

> [!NOTE]
> O token de atualização emitido pela Azure AD pode ser usado para aceder a vários recursos. Por exemplo, se tiver uma aplicação de cliente que tenha permissão para ligar para duas APIs web, o token de atualização pode ser usado para obter um sinal de acesso à outra API web também.

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para a Aplicação Nativa para cenários de API web. E, volte frequentemente. [Aplicação nativa da Web API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Registo da aplicação

Para registar uma aplicação no ponto final da AD Azure, consulte [O Registo de uma aplicação](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Inquilino único - Tanto a aplicação nativa como a Web API devem ser registadas no mesmo diretório em Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso da aplicação nativa aos seus recursos. A aplicação do cliente seleciona então as permissões desejadas do menu de entrega "Permissões para Outras Aplicações" no portal Azure.
* Multi-inquilino - Em primeiro lugar, a aplicação nativa só se registou no diretório do promotor ou da editora. Em segundo lugar, a aplicação nativa está configurada para indicar as permissões necessárias para ser funcional. Esta lista de permissões necessárias é mostrada num diálogo quando um utilizador ou administrador no diretório de destino dá consentimento à aplicação, o que a coloca à disposição da sua organização. Algumas aplicações apenas requerem permissões ao nível do utilizador, que qualquer utilizador da organização pode consentir. Outras aplicações requerem permissões de nível de administrador, que um utilizador da organização não pode consentir. Apenas um administrador de diretório pode dar consentimento a pedidos que requerem este nível de permissões. Quando o utilizador ou administrador consentir, apenas a API web está registada no seu diretório. 

## <a name="token-expiration"></a>Expiração do token

Quando a aplicação nativa usa o seu código de autorização para obter um sinal de acesso JWT, também recebe um token de atualização JWT. Quando o token de acesso expirar, o token de atualização pode ser usado para reautenticar o utilizador sem exigir que eles voltem a iniciar sessão. Este token de atualização é então usado para autenticar o utilizador, o que resulta num novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos e cenários](app-types.md) de aplicação
- Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação da AD Azure
