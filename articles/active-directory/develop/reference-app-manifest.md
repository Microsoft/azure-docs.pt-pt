---
title: Compreender o manifesto de aplicação do Azure Active Directory | Documentos da Microsoft
description: Abordagem detalhada do manifesto de aplicação do Azure Active Directory, que representa a configuração de identidade de uma aplicação no inquilino do Azure AD e é utilizado para facilitar a OAuth autorização, experiência de consentimento e muito mais.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2750de16c71e7d678810316f281e28ca8c40553d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403158"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto da aplicação do Azure Active Directory

O manifesto do aplicativo contém uma definição de todos os atributos de um objeto de aplicativo na plataforma de identidades da Microsoft. Ele também serve como um mecanismo para atualizar o objeto de aplicativo. Para obter mais informações sobre a entidade Aplicação e respetivo esquema, veja a documentação [Entidade de Aplicação da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Pode configurar atributos de uma aplicação através do portal do Azure ou programaticamente usando o Microsoft Graph. No entanto, existem alguns cenários em que terá de editar o manifesto da aplicação para configurar o atributo de uma aplicação. Os cenários incluem:

* Se registou a aplicação como contas da Microsoft pessoais e de multi-inquilinos do Azure AD, não é possível alterar as contas Microsoft suportadas na IU. Em vez disso, tem de utilizar o editor de manifesto de aplicativo para alterar o tipo de conta suportado.
* Se tiver de definir as permissões e funções que oferece suporte a sua aplicação, tem de modificar o manifesto do aplicativo.

## <a name="configure-the-app-manifest"></a>Configurar o manifesto da aplicação

Para configurar o manifesto do aplicativo:

1. Iniciar sessão a [portal do Azure](https://portal.azure.com).
1. Selecione o **do Azure Active Directory** serviço e, em seguida, selecione **registos das aplicações** ou **registos de aplicações (pré-visualização)**.
1. Selecione a aplicação que pretende configurar.
1. A partir da página **Descrição geral** da aplicação, selecione a secção **Manifesto**. É aberta um editor de manifesto baseada na web, permitindo-lhe editar o manifesto no portal. Opcionalmente, pode selecionar **baixe** para editar o manifesto localmente e, em seguida, utilizar **carregar** para voltar a aplicar à sua aplicação.

## <a name="manifest-reference"></a>Referência do manifesto

> [!NOTE]
> Se não pode ver o **valor de exemplo** coluna após o **Descrição**, maximize a janela do browser e desloque-se/percorra até ver a **valor de exemplo** coluna.

| Chave  | Tipo de valor | Descrição  | Valor de exemplo |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Int32 que pode ser nulo | Especifica a versão de token de acesso esperada pelo recurso. Isso altera a versão e formato do JWT produzidos independentes do ponto final ou cliente utilizado para pedir o token de acesso.<br/><br/>O ponto final utilizado, a versão 1.0 ou versão 2.0, é escolhido pelo cliente e afeta apenas a versão do id_tokens. Recursos tem de configurar explicitamente `accesstokenAcceptedVersion` para indicar o formato do token de acesso de suporte.<br/><br/>Valores possíveis para `accesstokenAcceptedVersion` são 1, 2 ou nulo. Se o valor for nulo, ela é padronizada como 1, o que corresponde ao ponto final do v1.0. | `2` |
| `allowPublicClient` | boolean | Especifica o tipo de aplicação de contingência. Azure AD infere o tipo de aplicação da replyUrlsWithType por predefinição. Existem determinados cenários em que o Azure AD não é possível determinar o tipo de aplicação de cliente (por exemplo, [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) fluxo onde o pedido HTTP acontece sem um redirecionamento de URL). Nesses casos, do Azure AD interpretará o tipo de aplicação com base no valor desta propriedade. Se este valor é definido como verdadeiro o tipo de aplicação de contingência está definido como cliente público, como uma aplicação instalada em execução num dispositivo móvel. O valor predefinido é false, que significa que o tipo de aplicação de contingência é um cliente confidencial, tais como a aplicação web. | `false` |
| `appId` | Cadeia de caracteres de identificador | Especifica o identificador exclusivo da aplicação que está atribuído a uma aplicação pelo Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Tipo de matriz | Especifica a coleção de funções de que uma aplicação poderá declarar. Estas funções podem ser atribuídas a utilizadores, grupos ou principais de serviço. Para obter mais exemplos e informações, consulte [adicionar funções de aplicação na sua aplicação e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `groupMembershipClaims` | string | Configura o `groups` afirmação emitida num utilizador ou o token de acesso de OAuth 2.0 que a aplicação espera. Para definir este atributo, utilize um dos seguintes valores de cadeia válida:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (para grupos de segurança e funções do Azure AD)<br/>- `"All"` (Isto irá obter todos os grupos de segurança, grupos de distribuição e funções de diretório do Azure AD que o utilizador com sessão iniciada é membro. | `"SecurityGroup"` |
| `optionalClaims` | string | As afirmações opcionais devolvidas no token pelo serviço de token de segurança para esta aplicação específica.<br>Neste momento, as aplicações que suportam contas pessoais e do Azure AD (registado através do portal de registo de aplicação) não é possível utilizar afirmações opcionais. No entanto, as aplicações registadas para apenas Azure AD com o ponto final v2.0 podem obter as afirmações opcionais que solicitados no manifesto. Para mais informações, veja [afirmações opcionais](active-directory-optional-claims.md). | `null` |
| `id` | Cadeia de caracteres de identificador | O identificador exclusivo para a aplicação no diretório. Este ID não é o identificador utilizado para identificar a aplicação em qualquer transação de protocolo. Ele é usado para fazer referência ao objeto em consultas de diretório. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Matriz de cadeia de caracteres | URI(s) definidas pelo utilizador que identificam exclusivamente uma aplicação Web no seu inquilino do Azure AD, ou dentro de um domínio personalizado verificado se a aplicação multi-inquilino. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | string | Especifica as ligações para os termos da aplicação de serviço e declaração de privacidade. Os termos de serviço e declaração de privacidade são apresentados aos utilizadores através da experiência de consentimento do utilizador. Para obter mais informações, consulte [como: Adicionar termos de serviço e declaração de privacidade para registar aplicações do Azure AD](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Tipo de matriz | Contém referências a aplicação atribuída credenciais, com base na cadeia de caracteres segredos compartilhados e certificados X.509. Estas credenciais são utilizadas ao pedir tokens de acesso (quando a aplicação está a agir como um cliente em vez disso, que como recurso). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Tipo de matriz | Utilizado para agrupar consentimento, se tiver uma solução que contém duas partes: uma aplicação de cliente e uma aplicação de API da web personalizada. Se introduzir o appID da aplicação de cliente para este valor, o utilizador terá apenas autorizar uma vez a aplicação de cliente. Do Azure AD saberá que consentir o cliente significa implicitamente consentir a API web e irá aprovisionar automaticamente os principais de serviço para o cliente e a web API ao mesmo tempo. O cliente e a aplicação de web API têm de estar registados no mesmo inquilino. | `[GUID]` |
| `logoUrl` | string | Ler apenas o valor que aponta para o URL da CDN para o logótipo que foi carregado no portal. | `https://MyRegisteredAppLogo` |
| `logoutUrl` | string | O URL para terminar a aplicação. | `https://MyRegisteredAppLogout` |
| `name` | string | O nome a apresentar para a aplicação. | `MyRegisteredApp` |
| `oauth2AllowImplicitFlow` | boolean | Especifica se esta aplicação web pode pedir tokens de acesso do fluxo implícito de OAuth2.0. A predefinição é falso. Este sinalizador é utilizado para aplicações baseadas no browser, como as aplicações de página única de Javascript. Para obter mais informações, introduza `OAuth 2.0 implicit grant flow` no índice e consulte os tópicos sobre o fluxo implícito. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | boolean | Especifica se esta aplicação web pode pedir tokens de ID de fluxo implícito de OAuth2.0. A predefinição é falso. Este sinalizador é utilizado para aplicações baseadas no browser, como as aplicações de página única de Javascript. | `false` |
| `oauth2Permissions` | Tipo de matriz | Especifica a coleção de âmbitos de permissão do OAuth 2.0 que a aplicação de API (recurso) do web expõe a aplicações de cliente. Estes âmbitos de permissão podem ser concedidos às aplicações de cliente durante o consentimento. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | boolean | Especifica se, como parte de pedidos de token de OAuth 2.0, do Azure AD irá permitir pedidos POST, em vez de solicitações GET. A predefinição é false, o que especifica que apenas os pedidos GET serão permitidos. | `false` |
| `parentalControlSettings` | string | `countriesBlockedForMinors` Especifica os países em que a aplicação é bloqueada para os menores.<br>`legalAgeGroupRule` Especifica a regra de grupo etário legal que se aplica aos utilizadores da aplicação. Pode ser definido como `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, ou `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Tipo de matriz | Consulte a descrição para o `keyCredentials` propriedade. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Tipo de matriz | Apresenta uma lista de aplicações e as permissões pedidas consentimento implícita. Requer que um administrador tiver dado consentimento para a aplicação. preAuthorizedApplications não necessitam de consentimento para as permissões pedidas ao utilizador. As permissões listadas na preAuthorizedApplications não necessitam de consentimento do utilizador. No entanto, as permissões de pedido adicionais não listadas no preAuthorizedApplications exigem consentimento do utilizador. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `replyUrlsWithType` | Matriz de cadeia de caracteres | Esta propriedade de valor múltiplos contém a lista de valores de redirect_uri registados que do Azure AD aceitará como destinos ao retornar tokens. Cada valor de uri deve conter um valor de tipo de aplicação associada. Os valores de tipo suportados são: `Web`, `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Tipo de matriz | Com o consentimento dinâmico, `requiredResourceAccess` unidades a experiência de consentimento de administrador e a experiência de consentimento do utilizador para os utilizadores que estão a utilizar o consentimento estático. No entanto, isso não orientar a experiência de consentimento do usuário para o caso geral.<br>`resourceAppId` é o identificador exclusivo para o recurso que a aplicação necessita de acesso a. Este valor deve ser igual ao appId declarado na aplicação de recurso de destino.<br>`resourceAccess` é uma matriz que lista as funções de aplicação que a aplicação necessita do recurso especificado e os âmbitos de permissão de OAuth2.0. Contém os `id` e `type` valores dos recursos especificados. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | string | O URL para os metadados SAML para a aplicação. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | string | Especifica o URL para a home page da aplicação. | `https://MyRegisteredApp` |
| `signInAudience` | string | Especifica as contas da Microsoft são suportadas para o aplicativo atual. Os valores suportados são:<ul><li>**AzureADMyOrg** -os utilizadores com um Microsoft trabalhar conta profissional ou escolar no inquilino do Azure AD da minha organização (ou seja, o único inquilino)</li><li>**AzureADMultipleOrgs** -os utilizadores com um Microsoft trabalhar conta profissional ou escolar no inquilino do Azure AD de qualquer organização (ou seja, o multi-inquilino)</li> <li>**AzureADandPersonalMicrosoftAccount** -os utilizadores com uma conta Microsoft pessoal, ou uma conta profissional ou escolar no inquilino do Azure AD de qualquer organização</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Matriz de cadeia de caracteres | Personalizado cadeias de caracteres que podem ser utilizadas para categorizar e identificar o aplicativo. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |


## <a name="manifest-limits"></a>Limites do manifesto
Um manifesto de aplicação tem vários atributos que são conhecidos como coleções approles por exemplo, keycredentials, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess, oauth2Permissions etc. No manifesto da aplicação completa para qualquer aplicativo, o número total de entradas em todas as coleções combinadas tem de foi limitado a 1200. Se já tiver 100 URI de redirecionamento especificado no manifesto do aplicativo, em seguida, é apenas esquerda com o restante do 1100 entradas para utilizar em todas as outras coleções combinado que compõem o manifesto.

> [!NOTE]
> No caso de tentar adicionar mais do que 1200 entradas no manifesto do aplicativo. Poderá receber um erro **"Falha ao atualizar a aplicação xxxxxx. Detalhes do erro: O tamanho do manifesto excedeu o limite. Reduza o número de valores e repita o pedido.** "


## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a relação entre objetos de Principal de serviço e aplicações de uma aplicação, consulte [aplicativos e objetos de principal de serviço no Azure AD](app-objects-and-service-principals.md).
* Consulte a [Glossário do programador do Azure AD](developer-glossary.md) para definições de alguns dos conceitos básicos do Azure Active Directory (AD) para desenvolvedores.

Utilize a seguinte secção de comentários para fornecer comentários que ajuda a refinar e moldar o nosso conteúdo.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
