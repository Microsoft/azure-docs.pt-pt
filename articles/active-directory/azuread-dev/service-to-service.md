---
title: Aplicativos de serviço-a-serviço no Azure Ative Directory
description: Descreve quais as aplicações de serviço-a-serviço e os fundamentos sobre o fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154768"
---
# <a name="service-to-service-apps"></a>Aplicativos de serviço para serviço

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

As aplicações de serviço-a-serviço podem ser uma aplicação de daemon ou servidor que precisa de obter recursos a partir de uma API web. Existem dois sub-cenários que se aplicam a esta secção:

- Um daemon que precisa de chamar uma API web, construída em OAuth 2.0 de credenciais de clientes tipo de concessão

    Neste cenário, é importante entender algumas coisas. Em primeiro lugar, a interação do utilizador não é possível com uma aplicação daemon, que requer que a aplicação tenha a sua própria identidade. Um exemplo de uma aplicação daemon é um trabalho de lote, ou um serviço de sistema operativo em segundo plano. Este tipo de aplicação solicita um token de acesso utilizando a sua identidade de aplicação e apresentando o seu ID de aplicação, credencial (senha ou certificado) e iD URI de aplicação a Azure AD. Após a autenticação bem sucedida, o daemon recebe um token de acesso a partir de Azure AD, que é então usado para chamar a API web.

- Uma aplicação de servidor (como uma API web) que precisa de chamar uma API web, construída em OAuth 2.0 On-Behalf-Of especificação de projeto

    Neste cenário, imagine que um utilizador tenha autenticado uma aplicação nativa, e esta aplicação nativa precisa de chamar uma API web. A Azure AD emite um token de acesso JWT para ligar para a API web. Se a API web precisar de chamar outra API web a jusante, pode usar o fluxo em nome do fluxo para delegar a identidade do utilizador e autenticar para a API web de segundo nível.

## <a name="diagram"></a>Diagrama

![Aplicação de Daemon ou Servidor para diagrama de API web](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade de aplicação com bolsa de credenciais de cliente OAuth 2.0

1. Em primeiro lugar, a aplicação do servidor necessita de autenticar com a AD Azure como em si, sem qualquer interação humana, como um diálogo de sinalização interativa. Faz um pedido ao ponto final simbólico da Azure AD, fornecendo a credencial, iD de aplicação e ID URI de aplicação.
1. A Azure AD autentica a aplicação e devolve um token de acesso JWT que é usado para chamar a API web.
1. Em HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Portador" no cabeçalho de autorização do pedido à API web. A API web valida então o token JWT, e se a validação for bem sucedida, devolve o recurso pretendido.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade delegada do utilizador com OAuth 2.0 Em nome do projeto de especificação

O fluxo discutido abaixo pressupõe que um utilizador foi autenticado noutra aplicação (como uma aplicação nativa), e a sua identidade de utilizador foi usada para adquirir um token de acesso à API web de primeiro nível.

1. A aplicação nativa envia o token de acesso à API web de primeiro nível.
1. A API web de primeiro nível envia um pedido para o ponto final simbólico da Azure AD, fornecendo o seu ID de aplicação e credenciais, bem como o token de acesso do utilizador. Além disso, o pedido é enviado com um parâmetro on_behalf_of que indica que a API web está a solicitar novos tokens para ligar para uma API web a jusante em nome do utilizador original.
1. A Azure AD verifica que a API web de primeiro nível tem permissões para aceder à API web de segundo nível e valida o pedido, devolvendo um token de acesso JWT e um token de atualização JWT para a API web de primeiro nível.
1. Em HTTPS, a API web de primeiro nível chama então a API web de segundo nível, anexando a cadeia simbólica no cabeçalho de autorização no pedido. A API web de primeiro nível pode continuar a chamar a API web de segundo nível, desde que os tokens de acesso e a refresh sejam válidos.

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para Daemon ou Servidor Application para cenários de API Web: [Servidor ou Aplicação Daemon para API Web](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registo da aplicação

* Inquilino único - Tanto para a identidade da aplicação como para casos de identidade de utilizador delegados, a aplicação da daemon ou servidor deve ser registada no mesmo diretório em Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do daemon ou servidor aos seus recursos. Se estiver a ser utilizado um tipo de identidade de utilizador delegado, a aplicação do servidor tem de selecionar as permissões desejadas. Na página **API Permission** para o registo de candidaturas, depois de ter selecionado **Adicionar uma permissão** e escolher a família API, escolher **permissões delegadas**e, em seguida, selecionar as suas permissões. Este passo não é necessário se o tipo de identidade de aplicação estiver a ser utilizado.
* Multi-inquilino - Primeiro, a aplicação da daemon ou servidor está configurada para indicar as permissões que necessita para ser funcional. Esta lista de permissões necessárias é mostrada num diálogo quando um utilizador ou administrador no diretório de destino dá consentimento à aplicação, o que a coloca à disposição da sua organização. Algumas aplicações apenas requerem permissões ao nível do utilizador, que qualquer utilizador da organização pode consentir. Outras aplicações requerem permissões ao nível do administrador, que um utilizador na organização não pode consentir. Apenas um administrador de diretório pode dar consentimento a aplicações que requerem este nível de permissões. Quando o utilizador ou administrador consente, ambas as APIs da web estão registadas no seu diretório.

## <a name="token-expiration"></a>Expiração simbólica

Quando a primeira aplicação utiliza o seu código de autorização para obter um token de acesso JWT, também recebe um token de atualização JWT. Quando o token de acesso expirar, o token de atualização pode ser usado para reautenticar o utilizador sem solicitar credenciais. Esta ficha de atualização é então usada para autenticar o utilizador, o que resulta num novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos e cenários de aplicações](app-types.md)
- Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação AD do Azure
