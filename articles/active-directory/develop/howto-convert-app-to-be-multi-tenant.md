---
title: Construa aplicações que assinam em utilizadores de Anúncios Azure
titleSuffix: Microsoft identity platform
description: Mostra como construir uma aplicação multi-inquilino que pode assinar um utilizador de qualquer inquilino do Azure Ative Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/19/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: b3338edf644aee8409cfca05d4ac801594cbf66b
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467764"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Como: Inscrever em qualquer utilizador do Diretório Ativo Azure utilizando o padrão de aplicação multi-inquilino

Se oferecer uma aplicação de Software como Serviço (SaaS) a muitas organizações, pode configurar a sua aplicação para aceitar inscrições de qualquer inquilino do Azure Ative Directory (Azure AD). Esta configuração *chama-se tornar a sua aplicação multi-arrendatária.* Os utilizadores de qualquer inquilino da AD Azure poderão iniciar sessão na sua aplicação depois de consentirem em utilizar a sua conta com a sua aplicação.

Se tiver uma aplicação existente que tenha o seu próprio sistema de conta, ou suporte outros tipos de inscrições de outros fornecedores de nuvem, adicionar entrada da Azure AD a qualquer inquilino é simples. Basta registar a sua aplicação, adicionar código de entrada via OAuth2, OpenID Connect ou SAML e colocar um [botão "Iniciar sessão com][AAD-App-Branding] a Microsoft" na sua aplicação.

> [!NOTE]
> Este artigo assume que já está familiarizado com a construção de um único pedido de inquilino para a Azure AD. Se não estiver, comece com uma das acelerações na [página inicial][AAD-Dev-Guide]do guia do desenvolvedor .

Existem quatro passos simples para converter a sua aplicação numa aplicação multi-inquilino da AD Azure:

1. [Atualize o seu registo de candidatura para ser multi-inquilino](#update-registration-to-be-multi-tenant)
2. [Atualize o seu código para enviar pedidos para o ponto final /ponto final comum](#update-your-code-to-send-requests-to-common)
3. [Atualize o seu código para lidar com vários valores emitentes](#update-your-code-to-handle-multiple-issuer-values)
4. [Compreender o consentimento do utilizador e da administração e fazer alterações de código apropriadas](#understand-user-and-admin-consent)

Vamos olhar cada passo em detalhe. Também pode saltar diretamente para a amostra [Construir uma aplicação web multi-inquilino SaaS que ligue para o Microsoft Graph usando O AD Azure e OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registo para ser multi-inquilino

Por predefinição, as inscrições de aplicação web/API em Azure AD são inquilinos únicos. Pode fazer o seu registo multi-inquilino encontrando os tipos de **conta suportada** ligarem o painel de **autenticação** do seu registo de candidatura no [portal Azure][AZURE-portal] e definindo-o em **Contas em qualquer diretório organizacional.**

Antes de uma aplicação poder ser feita multi-inquilino, a Azure AD exige que o APP ID URI da aplicação seja globalmente único. O URI do ID da Aplicação é uma das formas através das quais as aplicações são identificadas nas mensagens de protocolo. Relativamente às aplicações de inquilino único, basta que o URI do ID da Aplicação seja exclusivo nesse inquilino. Nas aplicações multi-inquilinos, tem de ser globalmente exclusivo, para que o Azure AD consiga encontrar a aplicação em todos os inquilinos. Para aplicar a exclusividade global, o URI do ID da App tem de ter um nome de anfitrião que corresponda a um domínio verificado do inquilino do Azure AD.

Por padrão, as aplicações criadas através do portal Azure têm um conjunto de Aplicações ID URI globalmente exclusivos na criação de apps, mas pode alterar este valor. Por exemplo, se o nome do seu inquilino fosse contoso.onmicrosoft.com então um App ID URI válido seria `https://contoso.onmicrosoft.com/myapp`. Se o seu inquilino tivesse um domínio verificado de `contoso.com`, então um App ID URI válido também seria `https://contoso.com/myapp`. Se o URI não seguir este padrão, a definição da aplicação como multi-inquilinos falha.

> [!NOTE]
> Os registos de clientes nativos, bem como [as aplicações](./active-directory-appmodel-v2-overview.md) da plataforma de identidade microsoft são multi-inquilinos por padrão. Você não precisa tomar nenhuma ação para fazer estas inscrições de inscrição multi-inquilinos.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualize o seu código para enviar pedidos para /comum

Num único pedido de inquilino, os pedidos de inscrição são enviados para o ponto final de inscrição do inquilino. Por exemplo, para contoso.onmicrosoft.com o ponto final seria: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Os pedidos enviados para o ponto final de um inquilino podem inscrever utilizadores (ou hóspedes) nesse inquilino para aplicações nesse inquilino.

Com uma aplicação multi-inquilino, a aplicação não sabe antecipadamente de que inquilino é o utilizador, pelo que não pode enviar pedidos para o ponto final de um inquilino. Em vez disso, os pedidos são enviados para um ponto final que multiplexes em todos os inquilinos da AD Azure: `https://login.microsoftonline.com/common`

Quando a plataforma de identidade da Microsoft recebe um pedido sobre o ponto final /ponto final comum, assina o utilizador e, consequentemente, descobre de que inquilino o utilizador é. O ponto final/ponto final comum trabalha com todos os protocolos de autenticação suportados pela Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 e WS-Federation.

A resposta de insessão à aplicação contém então um símbolo que representa o utilizador. O valor do emitente no token diz a uma aplicação de que inquilino o utilizador é. Quando uma resposta regressa do ponto final/ponto final comum, o valor do emitente no token corresponde ao inquilino do utilizador.

> [!IMPORTANT]
> O ponto final /comum não é um inquilino e não é um emitente, é apenas um multiplexer. Ao utilizar/comum, a lógica na sua aplicação para validar fichas tem de ser atualizada para ter isso em conta.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualize o seu código para lidar com vários valores emitentes

Aplicações web e APIs web recebem e validam tokens da plataforma de identidade da Microsoft.

> [!NOTE]
> Enquanto as aplicações de clientes nativos solicitam e recebem fichas da plataforma de identidade da Microsoft, fazem-no para enviá-las para APIs, onde são validadas. As aplicações nativas não validam fichas e devem tratá-las como opacas.

Vamos ver como uma aplicação valida fichas que recebe da plataforma de identidade da Microsoft. Uma única aplicação de inquilino normalmente tem um valor final como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e usa-o para construir um URL de metadados (neste caso, OpenID Connect) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

para descarregar duas peças críticas de informação que são usadas para validar fichas: as chaves de assinatura do inquilino e o valor do emitente. Cada inquilino da AD Azure tem um valor emitente único da forma:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

onde o valor GUID é a versão renome-safe da identificação do inquilino do inquilino. Se selecionar a ligação de metadados anterior para `contoso.onmicrosoft.com`, pode ver este valor emitente no documento.

Quando um único pedido de inquilino valida um símbolo, verifica a assinatura do símbolo com as chaves de assinatura do documento de metadados. Este teste permite-lhe certificar-se de que o valor do emitente no token corresponde ao que foi encontrado no documento de metadados.

Como o ponto final /comum não corresponde a um inquilino e não é um emitente, quando examina o valor emitente nos metadados para/comum tem um URL modelo em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Portanto, uma aplicação multi-inquilino não pode validar fichas apenas combinando o valor emitente nos metadados com o valor `issuer` no token. Uma aplicação multi-arrendatária precisa de lógica para decidir quais os valores emitentes válidos e que não se baseiam na parte de identificação do inquilino do valor emitente. 

Por exemplo, se um pedido multi-inquilino apenas permite o check-in de inquilinos específicos que se inscreveram para o seu serviço, então deve verificar o valor do emitente ou o valor de reclamação `tid` no símbolo para garantir que o inquilino está na sua lista de subscritores. Se um pedido multi-inquilino suver apenas com indivíduos e não tomar nenhuma decisão de acesso com base nos inquilinos, então pode ignorar completamente o valor do emitente.

Nas [amostras multi-inquilinos,][AAD-Samples-MT]a validação do emitente é desativada para permitir que qualquer inquilino da AD Azure assine.

## <a name="understand-user-and-admin-consent"></a>Compreender o consentimento do utilizador e da administração

Para que um utilizador assine uma aplicação em Azure AD, a aplicação deve estar representada no inquilino do utilizador. Isto permite que a organização faça coisas como aplicar políticas únicas quando os utilizadores do seu inquilino assinam a aplicação. Para um único pedido de inquilino, este registo é simples; é o que acontece quando se regista a aplicação no [portal Azure.][AZURE-portal]

Para um pedido de multi-inquilinos, o registo inicial para a candidatura vive no inquilino da AD Azure utilizado pelo promotor. Quando um utilizador de um inquilino diferente assina o pedido pela primeira vez, a Azure AD pede-lhes que consintam nas permissões solicitadas pelo pedido. Se consentirem, então uma representação da aplicação chamada *um diretor* de serviço é criada no inquilino do utilizador, e o inserimento pode continuar. É também criada uma delegação no diretório que regista o consentimento do utilizador para a aplicação. Para mais detalhes sobre os objetos de Aplicação e Serviço da aplicação, e como se relacionam uns com os outros, consulte objetos de [aplicação e objetos principais][AAD-App-SP-Objects]de serviço.

![Ilustra o consentimento para uma aplicação de nível único][Consent-Single-Tier]

Esta experiência de consentimento é afetada pelas permissões solicitadas pelo pedido. A plataforma de identidade da Microsoft suporta dois tipos de permissões, apenas de aplicações e delegadas.

* Uma permissão delegada concede a uma aplicação a capacidade de agir como um utilizador assinado para um subconjunto das coisas que o utilizador pode fazer. Por exemplo, pode conceder uma aplicação a autorização delegada para ler o assinado no calendário do utilizador.
* Uma permissão apenas para aplicações é concedida diretamente à identidade da aplicação. Por exemplo, pode conceder a uma aplicação apenas a permissão da aplicação para ler a lista de utilizadores de um inquilino, independentemente de quem esteja inscrito na aplicação.

Algumas permissões podem ser consentidas por um utilizador regular, enquanto outras requerem o consentimento de um administrador inquilino. 

### <a name="admin-consent"></a>Consentimento do administrador

As permissões apenas para aplicações requerem sempre o consentimento de um administrador inquilino. Se a sua aplicação solicitar uma permissão apenas para aplicações e um utilizador tentar iniciar sessão na aplicação, é apresentada uma mensagem de erro dizendo que o utilizador não pode consentir.

Algumas permissões delegadas também requerem o consentimento de um administrador inquilino. Por exemplo, a capacidade de reescrever para a AD Azure como o utilizador assinado requer o consentimento de um administrador inquilino. Tal como as permissões apenas para aplicações, se um utilizador comum tentar iniciar sessão numa aplicação que solicite uma permissão delegada que requer o consentimento do administrador, a sua aplicação recebe um erro. Se uma permissão requer consentimento administrativo é determinado pelo desenvolvedor que publicou o recurso, e pode ser encontrado na documentação para o recurso. A documentação de permissões para a API do [Gráfico AD Azure][AAD-Graph-Perm-Scopes] e [a API do Microsoft Graph][MSFT-Graph-permission-scopes] indicam quais as permissões que requerem consentimento administrativo.

Se o seu pedido utilizar permissões que requerem consentimento administrativo, precisa de ter um gesto como um botão ou link onde o administrador possa iniciar a ação. O pedido que o seu pedido envia para esta ação é o habitual pedido de autorização OAuth2/OpenID Connect que também inclui o parâmetro de corda de consulta `prompt=admin_consent`. Uma vez que o administrador tenha consentido e o diretor de serviço seja criado no inquilino do cliente, os pedidos subsequentes de inscrição não precisam do parâmetro `prompt=admin_consent`. Uma vez que o administrador decidiu que as permissões solicitadas são aceitáveis, nenhum outro utilizador no inquilino é solicitado para o consentimento a partir desse ponto.

Um administrador de inquilino pode desativar a capacidade de os utilizadores regulares consentirem com as candidaturas. Se esta capacidade for desativada, o consentimento do administrador é sempre necessário para que o pedido seja utilizado no inquilino. Se pretender testar a sua aplicação com o consentimento do utilizador final desativado, pode encontrar o interruptor de configuração no [portal Azure][AZURE-portal] na secção de **[definições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** do Utilizador nas **aplicações Enterprise**.

O parâmetro `prompt=admin_consent` também pode ser usado por pedidos que solicitam permissões que não requerem consentimento administrativo. Um exemplo de quando isso seria usado é se a aplicação requer uma experiência em que o administrador do inquilino "se inscreve" uma vez, e nenhum outro utilizador é solicitado para o consentimento a partir desse ponto.

Se um pedido necessitar de consentimento administrativo e um administrador entrar sem o `prompt=admin_consent` parâmetro, quando o administrador consentir com sucesso com a aplicação, aplicar-se-á **apenas à sua conta de utilizador**. Os utilizadores regulares ainda não poderão iniciar sessão ou consentir a aplicação. Esta funcionalidade é útil se pretender dar ao administrador inquilino a capacidade de explorar a sua aplicação antes de permitir o acesso a outros utilizadores.

> [!NOTE]
> Algumas aplicações querem uma experiência em que os utilizadores regulares possam consentir inicialmente, e mais tarde a aplicação pode envolver o administrador e solicitar permissões que requerem consentimento administrativo. Não há como fazê-lo com uma inscrição de pedido v1.0 hoje em Azure AD; no entanto, a utilização da plataforma de identidade da Microsoft (v2.0) permite que as aplicações solicitem permissões no prazo de execução em vez de no momento de registo, o que permite este cenário. Para mais informações, consulte o ponto final da [plataforma de identidade da Microsoft][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Consentimento e aplicações a vários níveis

A sua aplicação pode ter vários níveis, cada um representado pelo seu próprio registo em Azure AD. Por exemplo, uma aplicação nativa que chama a uma API web, ou uma aplicação web que chama uma API web. Em ambos os casos, o cliente (app nativa ou aplicação web) pede permissões para ligar para o recurso (Web API). Para que o cliente seja consentido com sucesso no inquilino de um cliente, todos os recursos a que solicita permissões já devem existir no inquilino do cliente. Se esta condição não for satisfeita, a AD Azure devolve um erro que o recurso deve ser adicionado primeiro.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Vários níveis em um único inquilino

Isto pode ser um problema se a sua aplicação lógica for composta por dois ou mais registos de candidaturas, por exemplo, um cliente e recurso separados. Como é que se consegue que o recurso entre o inquilino do cliente primeiro? A Azure AD cobre este caso permitindo que o cliente e o recurso sejam consentidos num único passo. O utilizador vê a soma total das permissões solicitadas tanto pelo cliente como pelo recurso na página de consentimento. Para permitir este comportamento, o registo de aplicação do recurso deve incluir o ID da aplicação do cliente como `knownClientApplications` no seu manifesto de [aplicação.][AAD-App-Manifest] Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Isto é demonstrado numa amostra de API de um cliente nativo multi-nível na secção de [conteúdo relacionado](#related-content) no final deste artigo. O diagrama seguinte fornece uma visão geral do consentimento para uma aplicação de vários níveis registada num único inquilino.

![Ilustra o consentimento para a aplicação de clientes conhecida seleções][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Vários níveis em vários inquilinos

Um caso semelhante acontece se os diferentes níveis de um pedido estiverem registados em diferentes inquilinos. Por exemplo, considere o caso da construção de uma aplicação de cliente nativo que ligue para o Office 365 Exchange Online API. Para desenvolver a aplicação nativa, e mais tarde para a aplicação nativa para funcionar no inquilino de um cliente, o diretor de serviço exchange online deve estar presente. Neste caso, o promotor e o cliente devem adquirir o Exchange Online para que o principal de serviço seja criado nos seus inquilinos.

Se for uma API construída por uma organização que não a Microsoft, o desenvolvedor da API precisa de fornecer uma forma de os seus clientes consentirem a aplicação nos inquilinos dos seus clientes. O design recomendado é para o desenvolvedor de terceiros construir a API de modo a que também possa funcionar como um cliente web para implementar a inscrição. Para efetuar este procedimento:

1. Siga as secções anteriores para garantir que a API implementa os requisitos de inscrição/código de pedido de multi-inquilinos.
2. Além de expor os âmbitos/funções da API, certifique-se de que o registo inclui a permissão "Iniciar sessão e ler o perfil do utilizador" (fornecida por predefinição).
3. Implemente uma página de inscrição/inscrição no cliente web e siga a orientação de consentimento do [administrador.](#admin-consent)
4. Uma vez que o utilizador consente com a aplicação, os links do diretor de serviço e da delegação de consentimento são criados no seu inquilino, e a aplicação nativa pode obter fichas para a API.

O diagrama seguinte fornece uma visão geral do consentimento para uma aplicação de vários níveis registada em diferentes inquilinos.

![Ilustra o consentimento para uma aplicação multipartidária de vários níveis][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Consentimento de revogação

Os utilizadores e administradores podem revogar o consentimento da sua aplicação a qualquer momento:

* Os utilizadores revogam o acesso a aplicações individuais, retirando-as da sua lista de Aplicações do Painel de [Acesso.][AAD-Access-Panel]
* Os administradores revogam o acesso às aplicações removendo-as utilizando a secção de [aplicações da Enterprise](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) do [portal Azure][AZURE-portal].

Se um administrador consentir com uma aplicação para todos os utilizadores de um inquilino, os utilizadores não podem revogar o acesso individualmente. Só o administrador pode revogar o acesso, e apenas para toda a aplicação.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicações multi-inquilinos e fichas de acesso ao cache

As aplicações multi-arrendatárias também podem ter acesso a fichas de acesso para chamadas APIs que são protegidas pela Azure AD. Um erro comum ao utilizar a Biblioteca de Autenticação do Diretório Ativo (ADAL) com uma aplicação multi-arrendatária é inicialmente solicitar um símbolo para um utilizador que utilize/comum, receber uma resposta, em seguida, solicitar um sinal subsequente para esse mesmo utilizador também usando/comum. Porque a resposta da Azure AD vem de um inquilino, não /comum, a ADAL caches o símbolo como sendo do inquilino. A chamada subsequente para/comum para obter um token de acesso para o utilizador falha a entrada da cache, e o utilizador é solicitado a iniciar o seu insessão novamente. Para evitar perder a cache, certifique-se de que as chamadas subsequentes para um utilizador já assinado são feitas para o ponto final do inquilino.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a construir uma aplicação que pode assinar num utilizador de qualquer inquilino da AD Azure. Depois de ativar o Single Sign-On (SSO) entre a sua aplicação e o Azure AD, também pode atualizar a sua aplicação para aceder a APIs expostos por recursos da Microsoft, como o Office 365. Isto permite-lhe oferecer uma experiência personalizada na sua aplicação, como mostrar informações contextuais aos utilizadores, como a sua imagem de perfil ou a sua próxima marcação de calendário. Para saber mais sobre a realização de chamadas API para serviços Azure AD e Office 365 como Exchange, SharePoint, OneDrive, OneNote e muito mais, visite o [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Conteúdo relacionado

* [Amostra de candidatura de vários inquilinos](https://github.com/mspnp/multitenant-saas-guidance)
* [Diretrizes de branding para aplicações][AAD-App-Branding]
* [Objetos de aplicação e objetos principais de serviço][AAD-App-SP-Objects]
* [Integração de aplicações com o Azure Active Directory][AAD-Integrating-Apps]
* [Visão geral do Quadro de Consentimento][AAD-Consent-Overview]
* [Âmbitos de permissão da Microsoft Graph API][MSFT-Graph-permission-scopes]
* [Âmbitos de permissão da AD Graph API azure][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://docs.microsoft.com/samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
