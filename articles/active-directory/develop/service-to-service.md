---
title: Aplicativos de serviço a serviço no Diretório Ativo do Azure
description: Descreve quais as aplicações serviço-a-serviço e os fundamentos sobre o fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
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
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: a94fcaffc190016a5377fe4b32484f84dc46ed25
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701506"
---
# <a name="service-to-service-apps"></a>Aplicativos de serviço a serviço

As aplicações de serviço ao serviço podem ser uma aplicação daemon ou servidor que precisa de obter recursos de uma API web. Há dois subcenários que se aplicam a esta secção:

- Um daemon que precisa de chamar uma API web, construída sobre o tipo de concessão de credenciais de cliente OAuth 2.0

    Neste cenário, é importante entender algumas coisas. Em primeiro lugar, a interação do utilizador não é possível com uma aplicação daemon, que requer que a aplicação tenha a sua própria identidade. Um exemplo de uma aplicação daemon é um trabalho de lote, ou um serviço de sistema operativo em execução em segundo plano. Este tipo de aplicação solicita um sinal de acesso utilizando a sua identidade de aplicação e apresentando o seu ID de aplicação, credencial (senha ou certificado) e id de aplicação URI à AD Azure. Após a autenticação bem sucedida, o daemon recebe um sinal de acesso da Azure AD, que é então usada para chamar a Web API.

- Uma aplicação de servidor (como uma API web) que precisa de chamar uma API web, construída em OAuth 2.0 Em nome da especificação de projeto

    Neste cenário, imagine que um utilizador se autenticou numa aplicação nativa, e esta aplicação nativa precisa de chamar uma API web. A Azure AD emite um sinal de acesso JWT para ligar para a Web API. Se a API web precisar de chamar outra API web a jusante, pode usar o fluxo em nome para delegar a identidade do utilizador e autenticar para a API web de segundo nível.

## <a name="diagram"></a>Diagrama

![Aplicação da Daemon ou servidor para o diagrama da Web API](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade de candidatura com bolsa de credenciais de cliente OAuth 2.0

1. Em primeiro lugar, a aplicação do servidor precisa de autenticar com a AD Azure como ela própria, sem qualquer interação humana, como um diálogo de sinalização interativo. Faz um pedido ao ponto final simbólico da Azure AD, fornecendo a credencial, id de aplicação e ID de aplicação URI.
1. A Azure AD autentica a aplicação e devolve um token de acesso JWT que é usado para chamar a Web API.
1. Ao longo do HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Bearer" no cabeçalho de autorização do pedido à Web API. A Web API valida então o símbolo JWT, e se a validação for bem sucedida, devolve o recurso desejado.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade de utilizador delegada com OAuth 2.0 Em nome do esboço de especificação

O fluxo discutido abaixo pressupõe que um utilizador foi autenticado noutra aplicação (como uma aplicação nativa), e a sua identidade de utilizador tem sido utilizada para adquirir um sinal de acesso à API web de primeiro nível.

1. A aplicação nativa envia o sinal de acesso à API web de primeiro nível.
1. A API web de primeiro nível envia um pedido ao ponto final simbólico da Azure AD, fornecendo o seu ID de aplicação e credenciais, bem como o sinal de acesso do utilizador. Além disso, o pedido é enviado com um parâmetro on_behalf_of que indica que a API da web está a solicitar novos tokens para chamar uma API web a jusante em nome do utilizador original.
1. A Azure AD verifica que a API web de primeiro nível tem permissões para aceder à API web de segundo nível e valida o pedido, devolvendo um token de acesso JWT e um token de atualização JWT para a API web de primeiro nível.
1. Ao longo do HTTPS, a API web de primeiro nível chama então a API web de segundo nível, através da adesão da cadeia simbólica no cabeçalho de Autorização no pedido. A API web de primeiro nível pode continuar a chamar a API web de segundo nível, desde que os tokens de acesso e tokens de atualização sejam válidos.

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para da daemon ou aplicação do servidor para cenários da Web API: [Servidor ou Aplicação Daemon para Web API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registo da aplicação

* Inquilino único - Tanto para a identidade de aplicação como para os casos de identidade de utilizador delegados, a aplicação daemon ou servidor deve ser registada no mesmo diretório em Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do daemon ou servidor aos seus recursos. Se estiver a ser utilizado um tipo de identidade de utilizador delegado, a aplicação do servidor tem de selecionar as permissões desejadas. Na página de **Permissão DaAPI** para o registo da candidatura, depois de ter selecionado **Adicionar uma permissão** e escolher a família API, escolher **permissões delegadas**e, em seguida, selecionar as suas permissões. Este passo não é necessário se o tipo de identidade da aplicação estiver a ser utilizado.
* Multi-inquilino - Em primeiro lugar, a aplicação daemon ou servidor está configurada para indicar as permissões que necessita para ser funcional. Esta lista de permissões necessárias é mostrada num diálogo quando um utilizador ou administrador no diretório de destino dá consentimento à aplicação, o que a coloca à disposição da sua organização. Algumas aplicações apenas requerem permissões ao nível do utilizador, que qualquer utilizador da organização pode consentir. Outras aplicações requerem permissões de nível de administrador, que um utilizador da organização não pode consentir. Apenas um administrador de diretório pode dar consentimento a pedidos que requerem este nível de permissões. Quando o utilizador ou administrador consentir, ambas as APIs web estão registadas no seu diretório.

## <a name="token-expiration"></a>Expiração do token

Quando a primeira aplicação usa o seu código de autorização para obter um sinal de acesso JWT, também recebe um token de atualização JWT. Quando o token de acesso expirar, o token de atualização pode ser usado para reautenticar o utilizador sem solicitar credenciais. Este token de atualização é então usado para autenticar o utilizador, o que resulta num novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos e cenários](app-types.md) de aplicação
- Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação da AD Azure
