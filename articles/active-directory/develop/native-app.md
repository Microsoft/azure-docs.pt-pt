---
title: Aplicações nativas no Azure Active Directory
description: Descreve o que são aplicações nativas e as noções básicas sobre fluxo de protocolo, registo e expiração do token para este tipo de aplicação.
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
ms.openlocfilehash: a6bf24124c4b072a64ef59500b2f723ff6abbb0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545848"
---
# <a name="native-apps"></a>Aplicações nativas

Aplicações nativas são aplicativos que chamam uma API web em nome de um utilizador. Este cenário baseia-se no tipo de concessão de código de autorização OAuth 2.0 com um cliente público, conforme descrito na secção 4.1 dos [especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749). O aplicativo nativo obtém um token de acesso do utilizador utilizando o protocolo de OAuth 2.0. Esse token de acesso, em seguida, é enviado no pedido para a web API, que autoriza o usuário e retorna o recurso pretendido.

## <a name="diagram"></a>Diagrama

![Aplicação nativa a diagrama da API da Web](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

Se estiver a utilizar as bibliotecas de autenticação do AD, a maioria dos detalhes descritos abaixo protocolo é processada para, como o pop-up do browser, tokens em cache e manipulação de tokens de atualização.

1. Usando um navegador pop-up, que o aplicativo nativo faz um pedido ao ponto final de autorização no Azure AD. Este pedido inclui o ID da aplicação e o URI de redirecionamento do aplicativo nativo, conforme mostrado no portal do Azure e o URI de ID da aplicação para a API web. Se o utilizador ainda não tiver iniciado sessão, é-lhes pedido para iniciar sessão novamente
1. O Azure AD autentica o utilizador. Se é uma aplicação multi-inquilino e é necessário usar o aplicativo o consentimento, o utilizador será necessário para dar consentimento se eles ainda não fez isso. Depois de conceder autorização e, após a autenticação com êxito, o Azure AD emite uma resposta de código de autorização volta para o URI de redirecionamento da aplicação cliente.
1. Quando o Azure AD emite uma resposta de código de autorização volta para o URI de redirecionamento, a aplicação cliente deixa de interação de navegador e extrai o código de autorização da resposta. Usando este código de autorização, a aplicação cliente envia um pedido ao ponto final de token do Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID da aplicação e URI de redirecionamento) e o recurso desejado (o URI de ID da aplicação para o Web API).
1. O código de autorização e as informações sobre a API web e de aplicação de cliente são validadas pelo Azure AD. Após a validação com êxito, o Azure AD devolve dois tokens: um token de acesso do JWT e um token de atualização do JWT. Além disso, o Azure AD devolve informações básicas sobre o usuário, como o respetivo ID de nome e de inquilino. exibição
1. Através de HTTPS, a aplicação cliente utiliza o token de acesso devolvido do JWT para adicionar a cadeia de caracteres do JWT com uma designação de "Bearer" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem-sucedida, retorna o recurso pretendido.
1. Quando o token de acesso expira, a aplicação cliente receberá um erro que indica que o utilizador tem de autenticar novamente. Se o aplicativo tem um token de atualização válido, ele pode ser usado para adquirir um novo token de acesso sem pedir ao utilizador que inicie sessão novamente. Se o token de atualização expirar, o aplicativo precisará de forma interativa autenticar o utilizador mais uma vez.

> [!NOTE]
> O token de atualização emitido pelo Azure AD pode ser utilizado para aceder a vários recursos. Por exemplo, se tiver uma aplicação de cliente que tenha permissão para chamar as APIs da web dois, o token de atualização pode servir-se para obter um acesso de token para a outra API web também.

## <a name="code-samples"></a>Exemplos de código

Veja os exemplos de código para o aplicativo nativo para cenários de Web API. Além disso, visite-a freqüentemente – adicionamos novos exemplos com frequência. [Aplicação nativa a Web API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Registo da aplicação

Registar uma aplicação com o ponto de final de versão 1.0 do Azure AD, consulte [registar uma aplicação](quickstart-register-app.md).

* Único inquilino - o aplicativo nativo e a API web têm de estar registrada no mesmo diretório no Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são utilizadas para limitar o acesso da aplicação nativa aos seus recursos. A aplicação de cliente, em seguida, seleciona as permissões pretendidas no menu pendente "Permissões para outras aplicações" no portal do Azure.
* Multi-inquilino - em primeiro lugar, o aplicativo nativo só registado no desenvolvedor ou diretório do publicador. Em segundo lugar, o aplicativo nativo está configurado para indicar as permissões que ele requer que seja funcional. Esta lista de permissões necessárias é mostrada numa caixa de diálogo quando um utilizador ou administrador no diretório de destino dá consentimento para a aplicação, o que torna disponível para a sua organização. Apenas algumas aplicações necessitam de permissões de nível de usuário, que pode consentir qualquer utilizador na organização. Outras aplicações requerem permissões de nível de administrador, que não pode autorizar um utilizador na organização. Apenas um administrador do diretório pode dar consentimento a aplicações que exigem esse nível de permissões. Quando o utilizador ou administrador autorizar, apenas a API de web está registada no respetivo diretório. 

## <a name="token-expiration"></a>Expiração do token

Quando o aplicativo nativo usa seu código de autorização para obter token de acesso um JWT, também recebe um token de atualização do JWT. Quando o token de acesso expira, o token de atualização pode servir-se novamente à autenticação do utilizador sem a necessidade de iniciar sessão novamente. Este token de atualização, em seguida, é utilizado para autenticar o utilizador, que resulta num novo token de acesso e o token de atualização.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre outras [tipos de aplicativos e cenários](app-types.md)
- Saiba mais sobre o Azure AD [Noções básicas de autenticação](authentication-scenarios.md)
