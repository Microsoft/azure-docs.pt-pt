---
title: Compilar aplicativos que se conectam a usuários do Azure AD
titleSuffix: Microsoft identity platform
description: Mostra como criar um aplicativo multilocatário que pode conectar um usuário de qualquer locatário Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73a5d30761b25f6233e298cac2602fb701a2987f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917782"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Como entrar em qualquer Azure Active Directory usuário usando o padrão de aplicativo multilocatário

Se você oferecer um aplicativo SaaS (software como serviço) para muitas organizações, poderá configurar seu aplicativo para aceitar entradas de qualquer locatário Azure Active Directory (Azure AD). Essa configuração é chamada *de tornar seu aplicativo multilocatário*. Os usuários em qualquer locatário do AD do Azure poderão entrar no seu aplicativo depois de consentirem para usar sua conta com seu aplicativo.

Se você tiver um aplicativo existente que tenha seu próprio sistema de conta, ou oferecer suporte a outros tipos de entradas de outros provedores de nuvem, a adição de entrada do Azure AD de qualquer locatário será simples. Basta registrar seu aplicativo, adicionar código de entrada via OAuth2, OpenID Connect ou SAML e colocar um [botão "entrar com a conta da Microsoft"][AAD-App-Branding] em seu aplicativo.

> [!NOTE]
> Este artigo pressupõe que você já esteja familiarizado com a criação de um aplicativo de locatário único para o Azure AD. Se não estiver, comece com um dos guias de início rápido na [Home Page do guia do desenvolvedor][AAD-Dev-Guide].

Há quatro etapas simples para converter seu aplicativo em um aplicativo multilocatário do Azure AD:

1. [Atualizar o registro do aplicativo para ser multilocatário](#update-registration-to-be-multi-tenant)
2. [Atualizar seu código para enviar solicitações para o ponto de extremidade/Common](#update-your-code-to-send-requests-to-common)
3. [Atualizar seu código para lidar com vários valores de emissor](#update-your-code-to-handle-multiple-issuer-values)
4. [Entender o consentimento do usuário e do administrador e fazer as alterações de código apropriadas](#understand-user-and-admin-consent)

Vamos examinar cada etapa detalhadamente. Você também pode ir diretamente para [esta lista de exemplos de multilocatários][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registro para ser multilocatário

Por padrão, os registros de aplicativo/API Web no Azure AD são locatário único. Você pode tornar seu registro multilocatário localizando a opção de **tipos de conta com suporte** no painel de **autenticação** do registro do aplicativo no [portal do Azure][AZURE-portal] e definindo-o como **contas em qualquer diretório organizacional**.

Antes que um aplicativo possa ser tornado multilocatário, o Azure AD exige que o URI da ID do aplicativo seja globalmente exclusivo. O URI do ID da Aplicação é uma das formas através das quais as aplicações são identificadas nas mensagens de protocolo. Relativamente às aplicações de inquilino único, basta que o URI do ID da Aplicação seja exclusivo nesse inquilino. Nas aplicações multi-inquilinos, tem de ser globalmente exclusivo, para que o Azure AD consiga encontrar a aplicação em todos os inquilinos. Para aplicar a exclusividade global, o URI do ID da App tem de ter um nome de anfitrião que corresponda a um domínio verificado do inquilino do Azure AD.

Por padrão, os aplicativos criados por meio do portal do Azure têm um URI de ID de aplicativo exclusivo globalmente definido na criação do aplicativo, mas você pode alterar esse valor. Por exemplo, se o nome do seu locatário fosse contoso.onmicrosoft.com, um URI de ID de aplicativo válido seria `https://contoso.onmicrosoft.com/myapp`. Se o locatário tiver um domínio verificado de `contoso.com`, um URI de ID de aplicativo válido também será `https://contoso.com/myapp`. Se o URI não seguir este padrão, a definição da aplicação como multi-inquilinos falha.

> [!NOTE]
> Os registros de cliente nativo, bem como [os aplicativos da plataforma Microsoft Identity](./active-directory-appmodel-v2-overview.md) , são multilocatários por padrão. Você não precisa realizar nenhuma ação para tornar esses registros de aplicativos multilocatários.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualizar seu código para enviar solicitações para/Common

Em um aplicativo de locatário único, as solicitações de entrada são enviadas para o ponto de extremidade de entrada do locatário. Por exemplo, para contoso.onmicrosoft.com, o ponto de extremidade seria: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. As solicitações enviadas para o ponto de extremidade de um locatário podem conectar usuários (ou convidados) nesse locatário a aplicativos nesse locatário.

Com um aplicativo multilocatário, o aplicativo não sabe antecipadamente de qual locatário o usuário é, portanto, você não pode enviar solicitações para o ponto de extremidade de um locatário. Em vez disso, as solicitações são enviadas para um ponto de extremidade que multiplexa entre todos os locatários do Azure AD: `https://login.microsoftonline.com/common`

Quando a plataforma de identidade da Microsoft recebe uma solicitação no ponto de extremidade/Common, ele assina o usuário em e, como consequência, descobre de qual locatário o usuário é. O ponto de extremidade/Common funciona com todos os protocolos de autenticação compatíveis com o Azure AD: OpenID Connect, OAuth 2,0, SAML 2,0 e WS-Federation.

A resposta de entrada para o aplicativo contém um token que representa o usuário. O valor do emissor no token informa a um aplicativo de qual locatário o usuário é. Quando uma resposta retorna do ponto de extremidade/Common, o valor do emissor no token corresponde ao locatário do usuário.

> [!IMPORTANT]
> O ponto de extremidade/Common não é um locatário e não é um emissor, é apenas um multiplexador. Ao usar/Common, a lógica em seu aplicativo para validar tokens precisa ser atualizada para levar isso em conta.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualizar seu código para lidar com vários valores de emissor

Aplicativos Web e APIs Web recebem e validam tokens da plataforma Microsoft Identity.

> [!NOTE]
> Embora aplicativos cliente nativos solicitem e recebam tokens da plataforma de identidade da Microsoft, eles fazem isso para enviá-los para APIs, onde eles são validados. Aplicativos nativos não validam tokens e devem tratá-los como opacos.

Vejamos como um aplicativo valida os tokens que ele recebe da plataforma de identidade da Microsoft. Um aplicativo de locatário único normalmente usa um valor de ponto de extremidade como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e o usa para construir uma URL de metadados (neste caso, OpenID Connect) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

para baixar duas partes críticas de informações que são usadas para validar tokens: as chaves de assinatura do locatário e o valor do emissor. Cada locatário do Azure AD tem um valor de emissor exclusivo do formulário:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

em que o valor de GUID é a versão de renomeação segura da ID de locatário do locatário. Se você selecionar o link de metadados anterior para `contoso.onmicrosoft.com`, poderá ver esse valor de emissor no documento.

Quando um aplicativo de locatário único valida um token, ele verifica a assinatura do token em relação às chaves de assinatura do documento de metadados. Esse teste permite que ele verifique se o valor do emissor no token corresponde ao que foi encontrado no documento de metadados.

Como o ponto de extremidade/Common não corresponde a um locatário e não é um emissor, quando você examina o valor do emissor nos metadados de/Common ele tem uma URL de modelo em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Portanto, um aplicativo multilocatário não pode validar tokens apenas combinando o valor do emissor nos metadados com o valor `issuer` no token. Um aplicativo multilocatário precisa de uma lógica para decidir quais valores de emissor são válidos e quais não se baseiam na parte da ID de locatário do valor do emissor. 

Por exemplo, se um aplicativo multilocatário só permitir a entrada de locatários específicos que se inscreveram para seu serviço, ele deverá verificar o valor do emissor ou o valor de declaração de `tid` no token para certificar-se de que o locatário está em sua lista de assinantes. Se um aplicativo multilocatário lida apenas com indivíduos e não toma decisões de acesso com base em locatários, ele pode ignorar o valor do emissor completamente.

Nos [exemplos de multilocatários][AAD-Samples-MT], a validação do emissor é desabilitada para habilitar qualquer locatário do Azure ad para entrar.

## <a name="understand-user-and-admin-consent"></a>Entender o consentimento do usuário e do administrador

Para que um usuário entre em um aplicativo no Azure AD, o aplicativo deve ser representado no locatário do usuário. Isso permite que a organização faça coisas como aplicar políticas exclusivas quando os usuários de seu locatário entram no aplicativo. Para um aplicativo de locatário único, esse registro é simples; é aquele que acontece quando você registra o aplicativo no [portal do Azure][AZURE-portal].

Para um aplicativo multilocatário, o registro inicial do aplicativo reside no locatário do Azure AD usado pelo desenvolvedor. Quando um usuário de um locatário diferente entra no aplicativo pela primeira vez, o Azure AD solicita que eles consentim nas permissões solicitadas pelo aplicativo. Se eles consentirem, uma representação do aplicativo chamado *entidade de serviço* será criada no locatário do usuário e a entrada poderá continuar. Uma delegação também é criada no diretório que registra o consentimento do usuário para o aplicativo. Para obter detalhes sobre o aplicativo e os objetos do serviço de aplicativo e como eles se relacionam entre si, consulte [objetos de aplicativo e objetos de entidade de segurança][AAD-App-SP-Objects].

![Ilustra o consentimento para o aplicativo de camada única][Consent-Single-Tier]

Essa experiência de consentimento é afetada pelas permissões solicitadas pelo aplicativo. A plataforma de identidade da Microsoft dá suporte a dois tipos de permissões, somente de aplicativo e delegadas.

* Uma permissão delegada concede a um aplicativo a capacidade de atuar como um usuário conectado para um subconjunto das coisas que o usuário pode fazer. Por exemplo, você pode conceder a um aplicativo a permissão delegada para ler o calendário do usuário conectado.
* Uma permissão somente de aplicativo é concedida diretamente à identidade do aplicativo. Por exemplo, você pode conceder a um aplicativo a permissão somente de aplicativo para ler a lista de usuários em um locatário, independentemente de quem está conectado ao aplicativo.

Algumas permissões podem ser consentidas por um usuário regular, enquanto outras exigem o consentimento de um administrador de locatários. 

### <a name="admin-consent"></a>Consentimento do administrador

Permissões somente de aplicativo sempre exigem o consentimento de um administrador de locatários. Se seu aplicativo solicitar uma permissão somente de aplicativo e um usuário tentar entrar no aplicativo, uma mensagem de erro será exibida informando que o usuário não é capaz de consentir.

Determinadas permissões delegadas também exigem o consentimento de um administrador de locatários. Por exemplo, a capacidade de fazer write-back para o Azure AD como o usuário conectado requer o consentimento de um administrador de locatários. Como as permissões somente de aplicativo, se um usuário comum tentar entrar em um aplicativo que solicita uma permissão delegada que exige o consentimento do administrador, seu aplicativo receberá um erro. Se uma permissão requer o consentimento do administrador é determinado pelo desenvolvedor que publicou o recurso e pode ser encontrado na documentação do recurso. A documentação de permissões para o [API do Graph do Azure ad][AAD-Graph-Perm-Scopes] e a [API do Microsoft Graph][MSFT-Graph-permission-scopes] indicam quais permissões exigem o consentimento do administrador.

Se seu aplicativo usa permissões que exigem o consentimento do administrador, você precisa ter um gesto, como um botão ou link no qual o administrador pode iniciar a ação. A solicitação que seu aplicativo envia para essa ação é a solicitação de autorização comum OAuth2/OpenID Connect que também inclui o parâmetro de cadeia de caracteres de consulta `prompt=admin_consent`. Depois que o administrador tiver consentido e a entidade de serviço for criada no locatário do cliente, as solicitações de entrada subsequentes não precisarão do parâmetro `prompt=admin_consent`. Como o administrador decidiu que as permissões solicitadas são aceitáveis, nenhum outro usuário no locatário é solicitado a fornecer consentimento desse ponto em diante.

Um administrador de locatários pode desabilitar a capacidade de usuários normais consentirem com os aplicativos. Se esse recurso estiver desabilitado, o consentimento do administrador sempre será necessário para que o aplicativo seja usado no locatário. Se você quiser testar seu aplicativo com o consentimento do usuário final desabilitado, poderá encontrar a opção de configuração no [portal do Azure][AZURE-portal] na seção **[configurações do usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** em **aplicativos empresariais**.

O parâmetro `prompt=admin_consent` também pode ser usado por aplicativos que solicitam permissões que não exigem o consentimento do administrador. Um exemplo de quando isso seria usado é se o aplicativo requer uma experiência em que o administrador de locatários "se inscreve" uma vez e nenhum outro usuário é solicitado a fornecer consentimento desse ponto em diante.

Se um aplicativo exigir o consentimento do administrador e um administrador entrar sem o parâmetro `prompt=admin_consent` que está sendo enviado, quando o administrador consentir com êxito para o aplicativo, ele será aplicado **somente à sua conta de usuário**. Os usuários regulares ainda não poderão entrar ou consentir o aplicativo. Esse recurso será útil se você quiser conceder ao administrador de locatários a capacidade de explorar seu aplicativo antes de permitir que outros usuários acessem.

> [!NOTE]
> Alguns aplicativos desejam uma experiência em que os usuários normais podem consentir inicialmente e, posteriormente, o aplicativo pode envolver o administrador e solicitar permissões que exigem o consentimento do administrador. Não há como fazer isso com um registro de aplicativo v 1.0 no Azure AD hoje mesmo; no entanto, o uso do ponto de extremidade da plataforma Microsoft Identity (v 2.0) permite que os aplicativos solicitem permissões em tempo de execução em vez de no momento do registro, o que habilita esse cenário. Para obter mais informações, consulte [ponto de extremidade da plataforma de identidade da Microsoft][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Aplicativos de consentimento e várias camadas

Seu aplicativo pode ter várias camadas, cada uma representada por seu próprio registro no Azure AD. Por exemplo, um aplicativo nativo que chama uma API da Web ou um aplicativo Web que chama uma API da Web. Em ambos os casos, o cliente (aplicativo nativo ou aplicativo Web) solicita permissões para chamar o recurso (API Web). Para que o cliente seja consentido com êxito no locatário de um cliente, todos os recursos aos quais ele solicita permissões já devem existir no locatário do cliente. Se essa condição não for atendida, o Azure AD retornará um erro informando que o recurso deve ser adicionado primeiro.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Várias camadas em um único locatário

Isso pode ser um problema se o aplicativo lógico consistir em dois ou mais registros de aplicativo, por exemplo, um cliente e recurso separados. Como você obtém o recurso no locatário do cliente primeiro? O Azure AD aborda esse caso, permitindo que o cliente e o recurso sejam consentidos em uma única etapa. O usuário vê a soma total das permissões solicitadas pelo cliente e pelo recurso na página de consentimento. Para habilitar esse comportamento, o registro do aplicativo do recurso deve incluir a ID do aplicativo do cliente como um `knownClientApplications` em seu [manifesto do aplicativo][AAD-App-Manifest]. Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Isso é demonstrado em um cliente nativo de várias camadas chamando o exemplo de API da Web na seção [conteúdo relacionado](#related-content) no final deste artigo. O diagrama a seguir fornece uma visão geral do consentimento para um aplicativo de várias camadas registrado em um único locatário.

![Ilustra o consentimento para o aplicativo cliente conhecido de várias camadas][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Várias camadas em vários locatários

Um caso semelhante acontece se as diferentes camadas de um aplicativo são registradas em locatários diferentes. Por exemplo, considere o caso da criação de um aplicativo cliente nativo que chama a API do Office 365 Exchange Online. Para desenvolver o aplicativo nativo e, posteriormente, para que o aplicativo nativo seja executado no locatário de um cliente, a entidade de serviço do Exchange Online deve estar presente. Nesse caso, o desenvolvedor e o cliente devem adquirir o Exchange Online para que a entidade de serviço seja criada em seus locatários.

Se for uma API criada por uma organização que não seja a Microsoft, o desenvolvedor da API precisará fornecer uma maneira para seus clientes consentirem o aplicativo nos locatários de seus clientes. O design recomendado é que o desenvolvedor de terceiros crie a API de modo que ela também possa funcionar como um cliente Web para implementar a inscrição. Para efetuar este procedimento:

1. Siga as seções anteriores para garantir que a API implemente os requisitos de registro/código do aplicativo multilocatário.
2. Além de expor os escopos/funções da API, verifique se o registro inclui a permissão "entrar e ler perfil do usuário" (fornecido por padrão).
3. Implemente uma página de entrada/inscrição no cliente Web e siga as diretrizes de [consentimento do administrador](#admin-consent) .
4. Depois que o usuário concorda com o aplicativo, a entidade de serviço e os links de delegação de consentimento são criados em seu locatário, e o aplicativo nativo pode obter tokens para a API.

O diagrama a seguir fornece uma visão geral do consentimento para um aplicativo de várias camadas registrado em locatários diferentes.

![Ilustra o consentimento para um aplicativo multicamadas de várias camadas][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Revogando consentimento

Os usuários e administradores podem revogar o consentimento para seu aplicativo a qualquer momento:

* Os usuários revogam o acesso a aplicativos individuais removendo-os da lista de [aplicativos do painel de acesso][AAD-Access-Panel] .
* Os administradores revogam o acesso a aplicativos removendo-os usando a seção [aplicativos empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) do [portal do Azure][AZURE-portal].

Se um administrador consentir um aplicativo para todos os usuários em um locatário, os usuários não poderão revogar o acesso individualmente. Somente o administrador pode revogar o acesso e apenas para o aplicativo inteiro.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicativos multilocatários e cache de tokens de acesso

Os aplicativos multilocatários também podem obter tokens de acesso para chamar APIs que são protegidas pelo Azure AD. Um erro comum ao usar o Biblioteca de Autenticação do Active Directory (ADAL) com um aplicativo multilocatário é solicitar inicialmente um token para um usuário usando/Common, receber uma resposta e solicitar um token subsequente para o mesmo usuário também usando/Common. Como a resposta do Azure AD vem de um locatário, não/Common, a ADAL armazena em cache o token como sendo do locatário. A chamada subsequente para/Common para obter um token de acesso para o usuário perde a entrada de cache e o usuário é solicitado a entrar novamente. Para evitar a ausência do cache, certifique-se de que as chamadas subsequentes para um usuário já conectado sejam feitas no ponto de extremidade do locatário.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar um aplicativo que pode conectar um usuário de qualquer locatário do Azure AD. Depois de habilitar o SSO (logon único) entre seu aplicativo e o Azure AD, você também pode atualizar seu aplicativo para acessar as APIs expostas por recursos da Microsoft, como o Office 365. Isso permite que você ofereça uma experiência personalizada em seu aplicativo, como mostrar informações contextuais para os usuários, como sua imagem de perfil ou seu próximo compromisso de calendário. Para saber mais sobre como fazer chamadas à API para os serviços do Azure AD e do Office 365, como o Exchange, SharePoint, OneDrive, OneNote e muito mais, visite [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Related content (Conteúdos relacionados)

* [Exemplos de aplicativos multilocatários][AAD-Samples-MT]
* [Diretrizes de identidade visual para aplicativos][AAD-App-Branding]
* [Objetos de aplicativo e objetos de entidade de serviço][AAD-App-SP-Objects]
* [Integração de aplicações com o Azure Active Directory][AAD-Integrating-Apps]
* [Visão geral da estrutura de consentimento][AAD-Consent-Overview]
* [Escopos de permissão Microsoft Graph API][MSFT-Graph-permission-scopes]
* [Escopos de permissão API do Graph do Azure AD][AAD-Graph-Perm-Scopes]

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
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
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
