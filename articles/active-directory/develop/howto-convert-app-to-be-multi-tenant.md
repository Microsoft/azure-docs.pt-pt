---
title: Como criar uma aplicação que pode iniciar a sessão de qualquer utilizador do Azure AD
description: Mostra como criar uma aplicação multi-inquilino que pode iniciar sessão um utilizador a partir de qualquer inquilino do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: justhu, elisol
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44feaecd42a8c3ce0ac0c712aa27b2480fd2a486
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806934"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Como: Iniciar sessão a qualquer utilizador do Azure Active Directory utilizando o padrão de aplicação multi-inquilino

Se oferecer um Software como um aplicativo de serviço (SaaS) a muitas organizações, pode configurar a sua aplicação para aceitar inícios de sessão de qualquer inquilino do Azure Active Directory (Azure AD). Esta configuração denomina *tornando sua multi-inquilino de aplicativo*. Os utilizadores em qualquer inquilino do Azure AD poderão iniciar sessão na sua aplicação depois de consentir utilizar a respetiva conta com a sua aplicação. 

Se tiver uma aplicação existente que tem seu próprio sistema de contas ou que suporta outros tipos de inícios de sessão a partir de outros fornecedores de cloud, a adição do Azure AD inícios de sessão de qualquer inquilino é simples. Apenas para registar a sua aplicação, adicione o código de início de sessão por meio do OAuth2, OpenID Connect ou SAML e colocar uma [botão "Sign in with Microsoft"] [ AAD-App-Branding] em seu aplicativo.

> [!NOTE] 
> Este artigo pressupõe que já estiver familiarizado com a criação de um aplicativo de inquilino único para o Azure AD. Se não tiver, comece com um dos inícios rápidos sobre o [home page de guia do programador][AAD-Dev-Guide].

Existem quatro passos simples para converter a sua aplicação numa aplicação multi-inquilino do Azure AD:

1. [Atualizar o registo de aplicação para ser o multi-inquilino](#update-registration-to-be-multi-tenant)
2. [Atualizar o seu código para enviar pedidos para o /common ponto final](#update-your-code-to-send-requests-to-common)
3. [Atualizar seu código para lidar com vários valores de emissor](#update-your-code-to-handle-multiple-issuer-values)
4. [Compreender o consentimento de utilizador e administrador e efetuar alterações de código adequado](#understand-user-and-admin-consent)

Vamos examinar cada etapa detalhadamente. Também pode ir diretamente para [esta lista de exemplos de multi-inquilinos][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registo ser multi-inquilino

Por predefinição, os registos de aplicação/API web no Azure AD são único inquilino. Pode efetuar o registo do multi-inquilino ao detetar o **com múltiplos inquilinos** alternar o **propriedades** painel do seu registo de aplicação no [portal do Azure] [ AZURE-portal] e defini-la como **Sim**.

Antes de um aplicativo pode ser feito a multi-inquilino, o Azure AD requer o URI de ID de aplicação do aplicativo para ser globalmente exclusivo. O URI do ID da Aplicação é uma das formas através das quais as aplicações são identificadas nas mensagens de protocolo. Relativamente às aplicações de inquilino único, basta que o URI do ID da Aplicação seja exclusivo nesse inquilino. Nas aplicações multi-inquilinos, tem de ser globalmente exclusivo, para que o Azure AD consiga encontrar a aplicação em todos os inquilinos. Para aplicar a exclusividade global, o URI do ID da App tem de ter um nome de anfitrião que corresponda a um domínio verificado do inquilino do Azure AD. 

Por predefinição, as aplicações criadas através do portal do Azure têm um URI de ID de aplicação globalmente exclusivo definido na criação de aplicações, mas pode alterar este valor. Por exemplo, se o nome do seu inquilino tiver contoso.onmicrosoft.com válido App ID URI seria `https://contoso.onmicrosoft.com/myapp`. Se o seu inquilino tinha um domínio verificiado `contoso.com`, em seguida, também seria um URI de ID de aplicação válido `https://contoso.com/myapp`. Se o URI não seguir este padrão, a definição da aplicação como multi-inquilinos falha.

> [!NOTE] 
> Registos de cliente nativo, bem como [v2.0 aplicativos](./active-directory-appmodel-v2-overview.md) são multi-inquilino por predefinição. Não precisa de fazer nada para tornar esses registos de aplicação multi-inquilino.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualizar o seu código para enviar pedidos para /common

Num aplicativo de inquilino único, os pedidos de início de sessão são enviados para início de sessão no ponto final o inquilino. Por exemplo, para contoso.onmicrosoft.com o ponto final seria: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Os pedidos enviados para o ponto final de um inquilino podem iniciar sessão em utilizadores (ou convidados) nesse inquilino para aplicações nesse inquilino. 

Com uma aplicação multi-inquilino, o aplicativo não sabe com antecedência quais inquilino, o utilizador é de, pelo que não é possível enviar pedidos ao ponto final de um inquilino. Em vez disso, as solicitações são enviadas para um ponto final que multiplexes em inquilinos de contas do Azure AD: `https://login.microsoftonline.com/common`

Quando o Azure AD recebe um pedido no /common ponto final,-lo se o utilizador e, como conseqüência, Deteta que o usuário é a partir de inquilino. O/ponto de extremidade comum funciona com todos os protocolos de autenticação suportados pelo Azure AD:  OpenID Connect, OAuth 2.0 e WS-Federation SAML 2.0.

A resposta de início de sessão para a aplicação, em seguida, contém um token que representa o usuário. O valor de emissor no token indica uma aplicação que é o utilizador a partir de inquilino. Quando uma resposta devolve a partir do /common ponto final, o valor de emissor no token corresponde ao inquilino do utilizador. 

> [!IMPORTANT]
> O/ponto de extremidade comum não é um inquilino e não é um emissor, é apenas um multiplexador. Quando utilizar /common, a lógica em seu aplicativo para validar tokens precisa ser atualizado para levar isso em conta. 

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualizar seu código para lidar com vários valores de emissor

Aplicações Web e web APIs receber e validar os tokens do Azure AD. 

> [!NOTE]
> Embora os aplicativos de cliente nativo pedir e recebem tokens do Azure AD, eles fazem-lo para enviá-los para APIs, em que eles sejam validados. Aplicativos nativos não validar os tokens e tem de tratá-los como opaco.

Vamos dar uma olhada em como uma aplicação valida tokens que recebe do Azure AD. Uma aplicação de inquilino único processo normalmente demora um valor de ponto de extremidade como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e o utiliza para construir um URL de metadados (no caso, OpenID Connect) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Para transferir as duas partes de informações críticas que são utilizados para validar os tokens: inquilino da assinatura de chaves e valor de emissor. Cada inquilino do Azure AD tem um valor de emissor exclusivo do formulário:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

em que o valor de GUID é a versão segura para mudar o nome do ID de inquilino do inquilino. Se selecionar a ligação anterior de metadados para `contoso.onmicrosoft.com`, pode ver este valor de emissor no documento.

Quando uma aplicação de inquilino único valida um token, ele verifica a assinatura do token contra as chaves de assinatura do documento de metadados. Este teste permite que ele para se certificar de que o valor de emissor no token corresponde ao que foi encontrada no documento de metadados.

Uma vez que o ponto de extremidade comum não corresponde a um inquilino e não é um emissor, quando examinar o valor de emissor dos metadados de/comuns tem um URL com modelos em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Por conseguinte, uma aplicação multi-inquilino não é possível validar os tokens comparando o valor de emissor nos metadados com o `issuer` valor no token. Uma aplicação multi-inquilino tem lógica para decidir quais valores de emissor são válidos e que não se baseiam na parte de ID de inquilino do valor de emissor. 

Por exemplo, se uma aplicação multi-inquilino só permite início de sessão a partir de inquilinos específicos que se inscreveram no seu serviço, em seguida, ele tem de verificar o valor de emissor ou o `tid` valor no token para se certificar de que esse inquilino está em sua lista de subscritores da afirmação. Se uma aplicação multi-inquilino apenas lida com pessoas e não faz quaisquer decisões de acesso com base em inquilinos, em seguida, ele pode ignorar o valor de emissor completamente.

Na [exemplos de multi-inquilinos][AAD-Samples-MT], validação do emissor está desativada para permitir que nenhum inquilino do Azure AD iniciar sessão.

## <a name="understand-user-and-admin-consent"></a>Compreender o consentimento de utilizador e administrador

Para um utilizador iniciar sessão a uma aplicação no Azure AD, o aplicativo deve ser representado no inquilino do utilizador. Isso permite que a organização fazer coisas como aplicar políticas únicas, quando os utilizadores do seu inquilino iniciar sessão na aplicação. Para uma aplicação de inquilino único, este registo é simples; ele é o que acontece quando registar a aplicação no [portal do Azure][AZURE-portal].

Para uma aplicação multi-inquilino, o registo inicial para o aplicativo reside no inquilino do Azure AD utilizado pelo programador. Quando um utilizador a partir de um inquilino diferente inicia sessão para o aplicativo pela primeira vez, o AD do Azure pede-los para dar consentimento às permissões solicitadas pela aplicação. Se eles consentimento, então uma representação do aplicativo chamado um *principal de serviço* é criado no inquilino do utilizador, e o início de sessão pode continuar. Uma delegação também é criada no diretório que regista o consentimento do utilizador para a aplicação. Para obter detalhes sobre a aplicação de aplicativos e ServicePrincipal objetos e como se relacionam entre si, consulte [objectos da aplicação e objetos de principal de serviço][AAD-App-SP-Objects].

![Consentir a aplicação de camada única][Consent-Single-Tier] 

Esta experiência de consentimento é afetada pelas permissões solicitadas pela aplicação. O Azure AD suporta dois tipos de permissões, só de aplicação e delegados.

* Uma permissão delegada atribui uma aplicação a capacidade para atuar como um utilizador com sessão iniciada para um subconjunto das coisas, o utilizador pode fazer. Por exemplo, pode conceder a um aplicativo a permissão delegada para ler o calendário do utilizador com sessão iniciada.
* Uma permissão só de aplicação é concedida diretamente para a identidade da aplicação. Por exemplo, pode conceder a um aplicativo a permissão só de aplicação para ler a lista de utilizadores num inquilino, independentemente do que tem sessão iniciada para a aplicação.

Algumas permissões podem ser permitidas por um utilizador normal, enquanto outras precisam de consentimento de um administrador de inquilino. 

### <a name="admin-consent"></a>Consentimento de admin

Permissões só de aplicação necessitam sempre de consentimento de um administrador de inquilino. Se a aplicação solicite uma permissão só de aplicação e um utilizador tenta iniciar sessão na aplicação, é apresentada uma mensagem de erro dizendo que o utilizador não é capaz de dar consentimento.

Determinadas permissões delegadas também requerem o consentimento de um administrador de inquilino. Por exemplo, a capacidade de efetuar um write back para o Azure AD como utilizador com sessão iniciada necessita de consentimento de um administrador de inquilino. Como as permissões só de aplicação, se um usuário comum tentar iniciar sessão para um aplicativo que solicita uma permissão delegada que necessita de consentimento de administrador, o seu aplicativo recebe um erro. Se uma permissão necessita de consentimento de administrador é determinado pelo programador que o recurso de publicado e pode ser encontrada na documentação para o recurso. A documentação de permissões para o [Azure AD Graph API] [ AAD-Graph-Perm-Scopes] e [Microsoft Graph API] [ MSFT-Graph-permission-scopes] indicar as permissões que requerem administração consentimento.

Se seu aplicativo utiliza as permissões que requerem o consentimento de administrador, tem de ter um gesto, como um botão ou uma ligação em que o administrador pode iniciar a ação. O pedido a sua aplicação envia para esta ação é habitual OAuth2/OpenID Connect pedido de autorização que também inclui o `prompt=admin_consent` parâmetro de cadeia de caracteres de consulta. Assim que o administrador já autorizou e o principal de serviço é criado no inquilino do cliente, os pedidos de início de sessão subsequentes não é necessário o `prompt=admin_consent` parâmetro. Uma vez que o administrador tenha decidido que as permissões solicitadas são aceitáveis, não existem outros utilizadores no inquilino são lhe seja pedidos consentimento a partir desse ponto.

Um administrador inquilino pode desativar a capacidade dos usuários regulares dar consentimento a aplicações. Se esta capacidade estiver desativada, o consentimento de administrador é sempre necessário para que o aplicativo a ser utilizado no inquilino. Se pretender testar a sua aplicação com o consentimento do utilizador final desativado, pode encontrar o comutador de configuração no [portal do Azure] [ AZURE-portal] no **[as definições de utilizador](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** secção sob **aplicações empresariais**.

O `prompt=admin_consent` parâmetro também pode ser utilizado por aplicações que solicitam permissões que não necessitam de consentimento de administrador. Um exemplo de quando isso seria usado é se a aplicação requer uma experiência em que o administrador de inquilino "se inscreve" é pedida aos utilizadores um de tempo e nenhum outro consentimento daí em diante.

Se um aplicativo exigir o consentimento de administrador e um administrador inicia sessão, sem o `prompt=admin_consent` parâmetro a ser enviado, quando o administrador autorizar com êxito a aplicação será aplicada **apenas para sua conta de utilizador**. Usuários regulares ainda não será capazes de iniciar sessão ou autorizar a aplicação. Esta funcionalidade é útil se quiser dar a capacidade de explorar a aplicação antes de permitir que outros utilizadores acesso de administrador de inquilinos.

> [!NOTE]
> Alguns aplicativos desejam uma experiência onde usuários regulares são capazes de dar consentimento inicialmente, e mais tarde o aplicativo pode envolver as permissões de administrador e a pedido que requerem o consentimento de administrador. Não é possível fazer isso com um registo de aplicação v1.0 atualmente; no Azure AD No entanto, usar o ponto final v2.0 permite que os aplicativos para pedir as permissões no tempo de execução, em vez de na hora do registo, que permite que este cenário. Para obter mais informações, consulte a [ponto final v2.0][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Aplicativos de consentimento e de várias camadas

A aplicação pode ter várias camadas, cada uma representada pelo seu próprio registo no Azure AD. Por exemplo, um aplicativo nativo que chama uma API web ou um aplicativo web que chama uma API web. Em ambos os casos, o cliente (aplicação nativa ou aplicação web) solicita as permissões para chamar o recurso (web API). Para o cliente com êxito possível dar consentimento ao inquilino de um cliente, todos os recursos aos quais ele solicita as permissões já devem existir no inquilino do cliente. Se esta condição não for cumprida, o AD do Azure devolve um erro que o recurso tem de ser adicionado pela primeira vez.

**Várias camadas num único inquilino**

Isso pode ser um problema se a sua aplicação lógica é composta por dois ou mais registos de acesso aplicação, por exemplo, um cliente separado e recursos. Como obtém o recurso para o inquilino do cliente primeiro? O Azure AD abrange neste caso, permitindo que o cliente e o recurso possível dar consentimento numa única etapa. O utilizador verá a soma total das permissões solicitadas pelo cliente e recursos na página de consentimento. Para habilitar esse comportamento, o registo de aplicação do recurso tem de incluir o ID da aplicação do cliente como um `knownClientApplications` no respetivo [manifesto do aplicativo][AAD-App-Manifest]. Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Isso é demonstrado num cliente nativo de várias camado chamada de exemplo de web API no [conteúdo relacionado](#related-content) secção no final deste artigo. O diagrama seguinte fornece uma descrição geral de consentimento para uma aplicação de várias camada registrada num único inquilino.

![Dar consentimento a aplicações cliente conhecidos de várias camadas][Consent-Multi-Tier-Known-Client] 

**Várias camadas em vários inquilinos**

Um caso semelhante acontece se as diferentes camadas de um aplicativo são registadas em inquilinos diferentes. Por exemplo, considere o caso de criação de um aplicativo de cliente nativo que chama a API do Office 365 Exchange Online. Para desenvolver a nativas da aplicação e mais tarde para o aplicativo nativo executar no inquilino de um cliente, o Exchange Online principal de serviço tem de estar presente. Neste caso, o desenvolvedor e o cliente tem de comprar Exchange Online para o serviço principal a ser criada em seus inquilinos. 

No caso de uma API criada por uma organização diferente da Microsoft, o desenvolvedor da API tem de fornecer uma forma para seus clientes dar consentimento o aplicativo em inquilinos dos clientes. É a estrutura recomendada para o desenvolvedor de terceiros criar a API de forma que ele também pode funcionar como um cliente web para implementar a inscrição. Para efetuar este procedimento:

1. Siga as secções anteriores para garantir que a API implementa os requisitos de registo/código de aplicação multi-inquilino.
2. Além de expor âmbitos/funções a API, certifique-se de que inclui o registo a "iniciar sessão e ler o perfil de usuário" permissão do Azure AD (fornecido por predefinição).
3. Implementar uma página início de sessão-em/inscrever-se do cliente web e siga os [consentimento de administrador](#admin-consent) orientações.
4. Depois do utilizador permitir à aplicação, links de delegação de principal e o consentimento de serviço são criados no seu inquilino e o aplicativo nativo pode obter tokens para a API.

O diagrama seguinte fornece uma descrição geral de consentimento para uma aplicação de várias camada registrada em inquilinos diferentes.

![Consentir a aplicação de múltiplos intervenientes de várias camada][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>A revogar o consentimento

Os utilizadores e administradores podem revogar a autorização ao seu aplicativo em qualquer altura:

* Os utilizadores revogarem o acesso a aplicativos individuais, removendo-os partir de seus [aplicativos de painel de acesso] [ AAD-Access-Panel] lista.
* Os administradores de revogarem o acesso a aplicações, removendo-os partir do Azure AD com o [aplicações empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) secção a [portal do Azure][AZURE-portal].

Se um administrador autorizar uma aplicação para todos os utilizadores num inquilino, os utilizadores não é possível revogar o acesso individualmente. Só o administrador pode revogar o acesso e apenas para toda a aplicação.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicações multi-inquilino e colocação em cache de tokens de acesso

Aplicações multi-inquilino também podem obter os tokens de acesso para chamar as APIs que estão protegidas pelo Azure AD. Um erro comum ao usar o Active Directory Authentication Library (ADAL) com uma aplicação multi-inquilino é inicialmente solicitar um token para um utilizador com o /common, recebeu uma resposta, em seguida, solicitar um token subsequente para esse mesmo usuário também usando /common. Uma vez que a resposta do Azure AD é proveniente de um inquilino, não/comuns, ADAL coloca em cache o token como sendo de inquilino. A chamada subseqüente a /common para obter um token de acesso para o utilizador erros de entrada de cache, e é pedido ao utilizador iniciar sessão novamente. Para evitar a falta de cache, certifique-se de que as chamadas subseqüentes para um utilizador já com sessão iniciada são feitas ao ponto de final do inquilino.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a criar uma aplicação que pode iniciar sessão um utilizador a partir de qualquer inquilino do Azure AD. Depois de ativar o único início de sessão (SSO) entre o aplicativo e do Azure AD, também pode atualizar a aplicação para aceder a APIs expostas por recursos de Microsoft como o Office 365. Isto permite-lhe oferecer uma experiência personalizada em seu aplicativo, como mostrar informações contextuais para os usuários, como sua imagem do perfil ou o seu compromisso de calendário seguinte. Para saber mais sobre a API de fazer chamadas para o Azure AD e Office 365 de serviços, como o Exchange, SharePoint, OneDrive, OneNote e muito mais, visite [API do Microsoft Graph][MSFT-Graph-overview].

## <a name="related-content"></a>Conteúdo relacionado

* [Exemplos de aplicação multi-inquilino][AAD-Samples-MT]
* [Diretrizes de imagem corporativa para aplicações][AAD-App-Branding]
* [Objetos de aplicação e objetos de principal de serviço][AAD-App-SP-Objects]
* [Integrar aplicações com o Azure Active Directory][AAD-Integrating-Apps]
* [Descrição geral da estrutura do consentimento][AAD-Consent-Overview]
* [Âmbitos de permissão da Graph API da Microsoft][MSFT-Graph-permission-scopes]
* [Âmbitos de permissão do Azure AD Graph API][AAD-Graph-Perm-Scopes]

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
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

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
