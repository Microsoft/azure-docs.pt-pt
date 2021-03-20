---
title: Construa aplicativos que assinam em utilizadores AD Azure
titleSuffix: Microsoft identity platform
description: Mostra como construir uma aplicação multi-inquilino que pode assinar em um utilizador de qualquer inquilino do Azure Ative Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 825a7d8c53552120a861657c7f3df7ae8f488c18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581725"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>How to: Iniciar sessão de qualquer utilizador do Azure Active Directory com o padrão de aplicação multi-inquilino

Se oferecer uma aplicação de Software como Serviço (SaaS) a muitas organizações, pode configurar a sua aplicação para aceitar inscrições de qualquer inquilino do Azure Ative Directory (Azure AD). Esta configuração *chama-se tornar a sua aplicação multi-inquilina.* Os utilizadores de qualquer inquilino Azure AD poderão inscrever-se na sua aplicação depois de consentir em utilizar a sua conta com a sua aplicação.

Se tiver uma aplicação existente que tenha o seu próprio sistema de conta, ou suporta outros tipos de inscrições de outros fornecedores de nuvem, adicionar a Azure AD de qualquer inquilino é simples. Basta registar a sua aplicação, adicionar código de inscrição via OAuth2, OpenID Connect ou SAML, e colocar um [botão "Iniciar com a Microsoft"][AAD-App-Branding] na sua aplicação.

> [!NOTE]
> Este artigo assume que já está familiarizado com a construção de um pedido de inquilino único para a Azure AD. Se não estiver, comece com um dos arranques rápidos na [página inicial][AAD-Dev-Guide]do guia do desenvolvedor .

Existem quatro passos para converter a sua aplicação numa aplicação multi-inquilina Azure AD:

1. [Atualize o seu registo de inscrição para ser multi-inquilino](#update-registration-to-be-multi-tenant)
2. [Atualize o seu código para enviar pedidos para o ponto final /comum](#update-your-code-to-send-requests-to-common)
3. [Atualize o seu código para lidar com vários valores de emitente](#update-your-code-to-handle-multiple-issuer-values)
4. [Compreenda o consentimento do utilizador e da administração e faça alterações de código apropriadas](#understand-user-and-admin-consent)

Vamos olhar cada passo em detalhe. Também pode saltar diretamente para a amostra [Construa uma aplicação web SaaS multi-arrendatário que liga para o Microsoft Graph usando Azure AD e OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registo para ser multi-inquilino

Por padrão, os registos web app/API em Azure AD são inquilinos únicos. Pode fazer o seu registo multi-inquilino, encontrando os **tipos de conta suportados** ligar o painel de **autenticação** do seu registo de inscrição no [portal Azure][AZURE-portal] e defini-lo **para Contas em qualquer diretório organizacional.**

Antes de uma aplicação poder ser feita multi-inquilino, a Azure AD exige que a App ID URI da aplicação seja globalmente única. O URI do ID da Aplicação é uma das formas através das quais as aplicações são identificadas nas mensagens de protocolo. Relativamente às aplicações de inquilino único, basta que o URI do ID da Aplicação seja exclusivo nesse inquilino. Nas aplicações multi-inquilinos, tem de ser globalmente exclusivo, para que o Azure AD consiga encontrar a aplicação em todos os inquilinos. Para aplicar a exclusividade global, o URI do ID da App tem de ter um nome de anfitrião que corresponda a um domínio verificado do inquilino do Azure AD.

Por padrão, as aplicações criadas através do portal Azure têm um ID URI de aplicações globalmente único na criação de apps, mas pode alterar este valor. Por exemplo, se o nome do seu inquilino fosse contoso.onmicrosoft.com então um ID URI de aplicação válido seria `https://contoso.onmicrosoft.com/myapp` . Se o seu inquilino tivesse um domínio verificado `contoso.com` de, então um ID URI de aplicação válido também seria `https://contoso.com/myapp` . Se o URI não seguir este padrão, a definição da aplicação como multi-inquilinos falha.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualize o seu código para enviar pedidos para /comum

Num pedido de inquilino único, os pedidos de inscrição são enviados para o ponto final de inscrição do inquilino. Por exemplo, para contoso.onmicrosoft.com o ponto final seria: `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Os pedidos enviados para o ponto final de um inquilino podem inscrever-se nos utilizadores (ou hóspedes) nesse inquilino para as candidaturas nesse inquilino.

Com uma aplicação multi-inquilino, a aplicação não sabe adiantado de que inquilino é o utilizador, por isso não pode enviar pedidos para o ponto final de um inquilino. Em vez disso, os pedidos são enviados para um ponto final que multiplexes em todos os inquilinos da AD Azure: `https://login.microsoftonline.com/common`

Quando a plataforma de identidade da Microsoft recebe um pedido no ponto final /comum, assina o utilizador e, consequentemente, descobre de que inquilino o utilizador é. O ponto final /comum funciona com todos os protocolos de autenticação suportados pelo Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 e WS-Federation.

A resposta de inscrição à aplicação contém então um símbolo que representa o utilizador. O valor do emitente no token diz a uma aplicação de que inquilino o utilizador é. Quando uma resposta retorna do ponto final /comum, o valor do emitente no token corresponde ao inquilino do utilizador.

> [!IMPORTANT]
> O ponto final /comum não é um inquilino e não é um emitente, é apenas um multiplexer. Ao utilizar /comum, a lógica da sua aplicação para validar fichas precisa de ser atualizada para ter isso em conta.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualize o seu código para lidar com vários valores de emitente

As aplicações web e as APIs web recebem e validam fichas da plataforma de identidade da Microsoft.

> [!NOTE]
> Enquanto as aplicações de clientes nativos solicitam e recebem fichas da plataforma de identidade da Microsoft, fazem-no para enviá-las para APIs, onde são validadas. As aplicações nativas não validam os tokens de acesso e devem tratá-los como opacos.

Vejamos como uma aplicação valida os tokens que recebe da plataforma de identidade da Microsoft. Uma aplicação de um único inquilino normalmente tem um valor de ponto final como:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

... e usa-o para construir um URL de metadados (neste caso, OpenID Connect) como:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

para descarregar duas peças críticas de informação que são usadas para validar fichas: as chaves de assinatura do inquilino e o valor do emitente.

Cada inquilino da AD AZure tem um valor emitente único do formulário:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

... onde o valor GUID é a versão segura do nome do inquilino ID do inquilino. Se selecionar o link de metadados `contoso.onmicrosoft.com` anterior, pode ver este valor do emitente no documento.

Quando uma aplicação de um único inquilino valida um token, verifica a assinatura do token com as chaves de assinatura do documento de metadados. Este teste permite-lhe certificar-se de que o valor do emitente no token corresponde ao que foi encontrado no documento de metadados.

Como o ponto final /comum não corresponde a um inquilino e não é um emitente, quando examina o valor do emitente nos metadados para /comum tem um URL modelo em vez de um valor real:

```http
https://sts.windows.net/{tenantid}/
```

Portanto, uma aplicação multi-inquilino não pode validar fichas apenas combinando o valor do emitente nos metadados com o `issuer` valor no token. Uma aplicação multi-arrendatário precisa de lógica para decidir quais os valores do emitente válidos e que não se baseiam na parte de identificação do inquilino do valor do emitente.

Por exemplo, se uma aplicação multi-arrendatário apenas permite a entrada de inquilinos específicos que se inscreveram para o seu serviço, então deve verificar o valor do emitente ou o valor de `tid` reclamação no token para garantir que o inquilino está na sua lista de subscritores. Se uma aplicação multi-arrendatário apenas lida com indivíduos e não tomar nenhuma decisão de acesso baseada nos inquilinos, então pode ignorar completamente o valor do emitente.

Nas [amostras multi-arrendatários,][AAD-Samples-MT]a validação do emitente é desativada para permitir que qualquer inquilino da Azure AD entre.

## <a name="understand-user-and-admin-consent"></a>Compreenda o consentimento do utilizador e da administração

Para que um utilizador inscreva-se numa aplicação em Azure AD, a aplicação deve ser representada no arrendatário do utilizador. Isto permite que a organização faça coisas como aplicar políticas únicas quando os utilizadores do seu inquilino se inscrevem na aplicação. Para um pedido de único inquilino, este registo é mais fácil; é o que acontece quando regista a inscrição no [portal Azure.][AZURE-portal]

Para uma aplicação multi-arrendatário, o registo inicial para a aplicação vive no inquilino AZURE AD utilizado pelo promotor. Quando um utilizador de um inquilino diferente assina o pedido pela primeira vez, a Azure AD pede-lhes que concordem com as permissões solicitadas pelo pedido. Se consentirem, então uma representação da aplicação chamada *principal de serviço* é criada no inquilino do utilizador, e o sintôs-in pode continuar. É também criada uma delegação no diretório que regista o consentimento do utilizador para a aplicação. Para mais detalhes sobre os objetos da Aplicação e serviçoPrincipal, e como se relacionam entre si, consulte [objetos de aplicação e objetos principais de serviço][AAD-App-SP-Objects].

![Ilustra o consentimento para uma aplicação de nível único][Consent-Single-Tier]

Esta experiência de consentimento é afetada pelas permissões solicitadas pelo pedido. A plataforma de identidade da Microsoft suporta dois tipos de permissões, apenas para aplicações e delegadas.

* Uma permissão delegada concede a uma aplicação a capacidade de agir como um utilizador assinado no utilizador para um subconjunto das coisas que o utilizador pode fazer. Por exemplo, pode conceder a um pedido a permissão delegada para ler o assinado no calendário do utilizador.
* Uma permissão apenas para aplicações é concedida diretamente à identidade da aplicação. Por exemplo, pode conceder a uma aplicação a permissão apenas para ler a lista de utilizadores num inquilino, independentemente de quem esteja inscrito na aplicação.

Algumas permissões podem ser consentidas por um utilizador regular, enquanto outras requerem o consentimento de um administrador de inquilino.

Para saber mais sobre o consentimento do utilizador e da administração, consulte [configurar o fluxo de trabalho](../manage-apps/configure-admin-consent-workflow.md)de consentimento administrativo .

### <a name="admin-consent"></a>Consentimento do administrador

Permissões só de aplicativos requerem sempre o consentimento de um administrador do inquilino. Se a sua aplicação solicitar uma permissão apenas para aplicações e um utilizador tentar iniciar sposição na aplicação, é apresentada uma mensagem de erro dizendo que o utilizador não pode consentir.

Algumas permissões delegadas também requerem o consentimento de um administrador do inquilino. Por exemplo, a capacidade de responder à Azure AD como assinada no utilizador requer o consentimento de um administrador de inquilino. Como permissões apenas para aplicações, se um utilizador comum tentar iniciar sôms numa aplicação que solicita uma permissão delegada que requer o consentimento do administrador, a sua aplicação recebe um erro. Se uma permissão requer consentimento administrativo é determinado pelo desenvolvedor que publicou o recurso, e pode ser encontrado na documentação para o recurso. A documentação das permissões para a [API do Gráfico da Microsoft][MSFT-Graph-permission-scopes] indica quais as permissões que requerem consentimento administrativo.

Se a sua aplicação utilizar permissões que exijam consentimento administrativo, tenha um gesto como um botão ou ligação onde o administrador possa iniciar a ação. O pedido que o seu pedido envia para esta ação é o habitual pedido de autorização OAuth2/OpenID Connect que também inclui o parâmetro de `prompt=admin_consent` cadeia de consulta. Uma vez que o administrador tenha consentido e o diretor de serviço é criado no inquilino do cliente, os pedidos subsequentes de inscrição não precisam do `prompt=admin_consent` parâmetro. Uma vez que o administrador decidiu que as permissões solicitadas são aceitáveis, nenhum outro utilizadores no arrendatário é solicitado a pedir o consentimento a partir desse ponto.

Um administrador de inquilino pode desativar a capacidade de os utilizadores regulares consentirem com as aplicações. Se esta capacidade for desativada, o consentimento administrativo é sempre necessário para que o pedido seja utilizado no arrendatário. Se pretender testar a sua aplicação com o consentimento do utilizador final desativado, pode encontrar o interruptor de configuração no [portal Azure][AZURE-portal] na secção **[de definições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** do Utilizador sob **as aplicações da Enterprise**.

O `prompt=admin_consent` parâmetro também pode ser usado por aplicações que solicitam permissões que não requerem consentimento administrativo. Um exemplo de quando isso seria usado é se a aplicação requer uma experiência em que o administrador do inquilino "assina" uma vez, e nenhum outro utilizadores é solicitado para consentimento a partir desse ponto.

Se uma aplicação necessitar de consentimento administrativo e se entrar um administrador sem que o `prompt=admin_consent` parâmetro seja enviado, quando o administrador consentir com sucesso na aplicação, aplica-se **apenas à sua conta de utilizador.** Os utilizadores regulares ainda não poderão iniciar sôms ou consentimento para a aplicação. Esta funcionalidade é útil se quiser dar ao administrador inquilino a capacidade de explorar a sua aplicação antes de permitir o acesso de outros utilizadores.

### <a name="consent-and-multi-tier-applications"></a>Consentimento e aplicações multi-camadas

A sua aplicação pode ter vários níveis, cada um representado pelo seu próprio registo no Azure AD. Por exemplo, uma aplicação nativa que chama uma API web, ou uma aplicação web que chama uma API web. Em ambos os casos, o cliente (app nativa ou web app) solicita permissões para ligar para o recurso (Web API). Para que o cliente seja consentido com sucesso no inquilino de um cliente, todos os recursos a que solicita permissões já devem existir no inquilino do cliente. Se esta condição não for cumprida, a Azure AD retorna um erro que o recurso deve ser adicionado primeiro.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Vários níveis em um único inquilino

Isto pode ser um problema se a sua aplicação lógica consistir em dois ou mais registos de candidaturas, por exemplo um cliente e recurso separados. Como é que se consegue o recurso para o inquilino do cliente primeiro? A Azure AD cobre este caso, permitindo que o cliente e o recurso sejam consentidos num único passo. O utilizador vê a soma total das permissões solicitadas tanto pelo cliente como pelo recurso na página de consentimento. Para permitir este comportamento, o registo de candidatura do recurso deve incluir o ID da Aplicação do cliente como um `knownClientApplications` manifesto de [aplicação.][AAD-App-Manifest] Por exemplo:

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Isto é demonstrado num cliente nativo de vários níveis que chama amostra de API web na secção de [conteúdo relacionado](#related-content) no final deste artigo. O diagrama seguinte fornece uma visão geral do consentimento para uma aplicação multi-nível registada num único inquilino.

![Ilustra o consentimento para app de clientes conhecidos em vários níveis][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Vários níveis em vários inquilinos

Um caso semelhante acontece se os diferentes níveis de um pedido estiverem registados em diferentes inquilinos. Por exemplo, considere o caso da construção de uma aplicação de cliente nativo que ligue para a API Exchange Online. Para desenvolver a aplicação nativa, e mais tarde para que a aplicação nativa seja executada no inquilino de um cliente, o diretor de serviço exchange online deve estar presente. Neste caso, o promotor e o cliente devem adquirir o Exchange Online para que o principal de serviço seja criado nos seus inquilinos.

Se for uma API construída por outra organização que não a Microsoft, o desenvolvedor da API precisa de fornecer uma forma de os seus clientes consentirem a aplicação nos inquilinos dos seus clientes. O design recomendado é para o desenvolvedor de terceiros construir a API de modo que também pode funcionar como um cliente web para implementar a inscrição. Para efetuar este procedimento:

1. Siga as secções anteriores para garantir que a API implementa os requisitos de registo/código de pedidos de multi-inquilinos.
2. Além de expor os âmbitos/funções da API, certifique-se de que o registo inclui a permissão "Iniciar e ler o perfil do utilizador" (fornecida por padrão).
3. Implemente uma página de login/inscrição no cliente web e siga a orientação [de consentimento de administração.](#admin-consent)
4. Uma vez que o utilizador consinta com o pedido, o principal de serviço e as ligações da delegação de consentimento são criados no seu inquilino, e a aplicação nativa pode obter fichas para a API.

O diagrama seguinte fornece uma visão geral do consentimento para uma aplicação multi-nível registada em diferentes inquilinos.

![Ilustra o consentimento para app multi-partidárias multi-camadas][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Revogação do consentimento

Os utilizadores e administradores podem revogar o consentimento da sua aplicação a qualquer momento:

* Os utilizadores revogam o acesso a aplicações individuais, retirando-as da sua lista de [Aplicações do Painel de Acesso.][AAD-Access-Panel]
* Os administradores revogam o acesso às aplicações removendo-as utilizando a secção de [aplicações](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) da Enterprise do [portal Azure][AZURE-portal].

Se um administrador consentir com uma aplicação para todos os utilizadores de um inquilino, os utilizadores não podem revogar o acesso individualmente. Só o administrador pode revogar o acesso, e apenas para toda a aplicação.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicações multi-arrendadas e fichas de acesso a caching

As aplicações multi-arrendadas também podem ter acesso a fichas para chamar APIs que estão protegidas pela Azure AD. Um erro comum ao utilizar a Biblioteca de Autenticação da Microsoft (MSAL) com uma aplicação multi-arrendatário é solicitar inicialmente um token para um utilizador que utilize /comum, receber uma resposta e, em seguida, solicitar um token subsequente para esse mesmo utilizador também usando /comum. Porque a resposta da AZure AD vem de um inquilino, não /comum, MSAL caches o símbolo como sendo do inquilino. A chamada subsequente para /comum para obter um token de acesso para o utilizador falha a entrada de cache, e o utilizador é solicitado a iniciar novamente a sposição. Para evitar a falta da cache, certifique-se de que as chamadas subsequentes para um utilizador já assinado são feitas no ponto final do arrendatário.

## <a name="related-content"></a>Conteúdo relacionado

* [Amostra de aplicação de vários inquilinos](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Diretrizes de imagem corporativa para aplicações][AAD-App-Branding]
* [Objetos de aplicação e objetos principais de serviço][AAD-App-SP-Objects]
* [Integrar aplicações com o Azure Active Directory][AAD-Integrating-Apps]
* [Visão geral do Quadro de Consentimento][AAD-Consent-Overview]
* [Âmbitos de permissão da Microsoft Graph API][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a construir uma aplicação que pode assinar num utilizador de qualquer inquilino da AZure AD. Depois de ativar Sign-On (SSO) entre a sua aplicação e a AD Azure, também pode atualizar a sua aplicação para aceder a APIs expostas por recursos da Microsoft como o Microsoft 365. Isto permite-lhe oferecer uma experiência personalizada na sua aplicação, como mostrar informações contextuais aos utilizadores, como a sua imagem de perfil ou a sua próxima marcação de calendário.

Para saber mais sobre a realização de chamadas API para serviços AZure AD e Microsoft 365 como Exchange, SharePoint, OneDrive, OneNote e muito mais, visite [a Microsoft Graph API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

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
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken