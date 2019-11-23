---
title: Noções básicas sobre o manifesto do aplicativo Azure Active Directory | Microsoft Docs
description: Cobertura detalhada do manifesto do aplicativo Azure Active Directory, que representa a configuração de identidade de um aplicativo em um locatário do Azure AD e é usado para facilitar a autorização de OAuth, a experiência de consentimento e muito mais.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: e44c3e607f3d5a5ea8269b9885a4fec54000bb5f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389586"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto do aplicativo Azure Active Directory

O manifesto do aplicativo contém uma definição de todos os atributos de um objeto de aplicativo na plataforma de identidade da Microsoft. Ele também serve como um mecanismo para atualizar o objeto de aplicativo. Para obter mais informações sobre a entidade de aplicativo e seu esquema, consulte a [documentação da entidade de aplicativo API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Você pode configurar os atributos de um aplicativo por meio da portal do Azure ou programaticamente usando a [API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) ou o [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). No entanto, há alguns cenários em que você precisará editar o manifesto do aplicativo para configurar o atributo de um aplicativo. Os cenários incluem:

* Se você registrou o aplicativo como Azure AD multilocatário e contas pessoais da Microsoft, não será possível alterar as contas da Microsoft com suporte na interface do usuário. Em vez disso, você deve usar o editor de manifesto do aplicativo para alterar o tipo de conta com suporte.
* Se você precisar definir permissões e funções às quais seu aplicativo dá suporte, você deve modificar o manifesto do aplicativo.

## <a name="configure-the-app-manifest"></a>Configurar o manifesto do aplicativo

Para configurar o manifesto do aplicativo:

1. Entre na [portal do Azure](https://portal.azure.com).
1. Selecione o serviço **Azure Active Directory** e, em seguida, selecione **registros de aplicativo**.
1. Selecione o aplicativo que você deseja configurar.
1. A partir da página **Descrição geral** da aplicação, selecione a secção **Manifesto**. Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto no Portal. Opcionalmente, você pode selecionar **baixar** para editar o manifesto localmente e, em seguida, usar **carregar** para reaplicá-lo ao seu aplicativo.

## <a name="manifest-reference"></a>Referência de manifesto

> [!NOTE]
> Se você não conseguir ver a coluna de **valor de exemplo** após a **Descrição**, maximize a janela do navegador e role/passe o dedo até ver a coluna **valor de exemplo** .

| Chave  | Tipo de valor | Descrição  | Valor de exemplo |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Int32 anulável | Especifica a versão do token de acesso esperada pelo recurso. Isso altera a versão e o formato do JWT produzido independentemente do ponto de extremidade ou cliente usado para solicitar o token de acesso.<br/><br/>O ponto de extremidade usado, v 1.0 ou v 2.0, é escolhido pelo cliente e só afeta a versão do id_tokens. Os recursos precisam configurar explicitamente `accesstokenAcceptedVersion` para indicar o formato de token de acesso com suporte.<br/><br/>Os valores possíveis para `accesstokenAcceptedVersion` são 1, 2 ou NULL. Se o valor for NULL, esse padrão será 1, que corresponde ao ponto de extremidade v 1.0. <br/><br/>Se `signInAudience` for `AzureADandPersonalMicrosoftAccount`, o valor deverá ser `2`  | `2` |
| `addIns` | Coleção | Define o comportamento personalizado que um serviço de consumo pode usar para chamar um aplicativo em contextos específicos. Por exemplo, os aplicativos que podem renderizar fluxos de arquivos podem definir a propriedade addIns para sua funcionalidade "FileHandler". Isso permitirá que serviços como o Office 365 chamem o aplicativo no contexto de um documento no qual o usuário está trabalhando. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Booleano | Especifica o tipo de aplicativo de fallback. O AD do Azure infere o tipo de aplicativo do replyUrlsWithType por padrão. Há determinados cenários em que o Azure AD não pode determinar o tipo de aplicativo cliente (por exemplo, o fluxo [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) em que a solicitação HTTP ocorre sem um redirecionamento de URL). Nesses casos, o AD do Azure interpretará o tipo de aplicativo com base no valor dessa propriedade. Se esse valor for definido como true, o tipo de aplicativo de fallback será definido como cliente público, como um aplicativo instalado em execução em um dispositivo móvel. O valor padrão é false, o que significa que o tipo de aplicativo de fallback é cliente confidencial, como aplicativo Web. | `false` |
| `availableToOtherTenants` | Booleano | true se o aplicativo for compartilhado com outros locatários; caso contrário, false. <br><br> _Observação: isso está disponível apenas na experiência Registros de aplicativo (herdada). Substituído por `signInAudience` na experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `appId` | Cadeia | Especifica o identificador exclusivo para o aplicativo que é atribuído a um aplicativo pelo Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Coleção | Especifica a coleção de funções que um aplicativo pode declarar. Essas funções podem ser atribuídas a usuários, grupos ou entidades de serviço. Para obter mais exemplos e informações, consulte [adicionar funções de aplicativo em seu aplicativo e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | Cadeia | O nome de exibição do aplicativo. <br><br> _Observação: isso está disponível apenas na experiência Registros de aplicativo (herdada). Substituído por `name` na experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"MyRegisteredApp"` |
| `errorUrl` | Cadeia | Sem suporte. | |
| `groupMembershipClaims` | Cadeia | Configura a declaração de `groups` emitida em um usuário ou token de acesso OAuth 2,0 que o aplicativo espera. Para definir esse atributo, use um dos seguintes valores de cadeia de caracteres válidos:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (para grupos de segurança e funções do Azure AD)<br/>- `"All"` (isso obterá todos os grupos de segurança, grupos de distribuição e funções de diretório do Azure AD dos quais o usuário conectado é membro. | `"SecurityGroup"` |
| `homepage` | Cadeia | A URL para a home page do aplicativo. <br><br> _Observação: isso está disponível apenas na experiência Registros de aplicativo (herdada). Substituído por `signInUrl` na experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"https://MyRegisteredApp"` |
| `objectId` | Cadeia | O identificador exclusivo para o aplicativo no diretório. <br><br> _Observação: isso está disponível apenas na experiência Registros de aplicativo (herdada). Substituído por `id` na experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | Cadeia | As declarações opcionais retornadas no token pelo serviço de token de segurança para este aplicativo específico.<br>Neste momento, os aplicativos que dão suporte a contas pessoais e ao Azure AD (registrados por meio do portal de registro de aplicativo) não podem usar declarações opcionais. No entanto, os aplicativos registrados apenas para o Azure AD usando o ponto de extremidade v 2.0 podem obter as declarações opcionais solicitadas no manifesto. Para obter mais informações, consulte [declarações opcionais](active-directory-optional-claims.md). | `null` |
| `id` | Cadeia | O identificador exclusivo para o aplicativo no diretório. Essa ID não é o identificador usado para identificar o aplicativo em qualquer transação de protocolo. Ele é usado para a referência do objeto em consultas de diretório. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | URIS (s) definidos pelo usuário que identificam exclusivamente um aplicativo Web dentro de seu locatário do Azure AD ou dentro de um domínio personalizado verificado se o aplicativo for multilocatário. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | Cadeia | Especifica os links para os termos de serviço e a política de privacidade do aplicativo. Os termos de serviço e a política de privacidade são exibidos para os usuários por meio da experiência de consentimento do usuário. Para obter mais informações, consulte [como adicionar termos de serviço e a política de privacidade para aplicativos registrados do Azure ad](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Coleção | Mantém referências a credenciais atribuídas ao aplicativo, segredos compartilhados com base em cadeia de caracteres e certificados X. 509. Essas credenciais são usadas ao solicitar tokens de acesso (quando o aplicativo está agindo como um cliente, e não como recurso). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | Usado para o consentimento de agrupamento se você tiver uma solução que contenha duas partes: um aplicativo cliente e um aplicativo de API Web personalizado. Se você inserir a appID do aplicativo cliente nesse valor, o usuário só terá que consentir uma vez para o aplicativo cliente. O AD do Azure saberá que o consentimento para o cliente significa implicitamente consentir a API da Web e provisionará automaticamente as entidades de serviço para o cliente e a API da Web ao mesmo tempo. O cliente e o aplicativo de API Web devem ser registrados no mesmo locatário. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | Cadeia | Valor somente leitura que aponta para a URL da CDN para o logotipo que foi carregado no Portal. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | Cadeia | A URL para fazer logoff do aplicativo. | `"https://MyRegisteredAppLogout"` |
| `name` | Cadeia | O nome de exibição do aplicativo. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Booleano | Especifica se este aplicativo Web pode solicitar tokens de acesso de fluxo implícitos do OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como aplicativos de página única do JavaScript. Para saber mais, insira `OAuth 2.0 implicit grant flow` no sumário e veja os tópicos sobre o fluxo implícito. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Booleano | Especifica se este aplicativo Web pode solicitar tokens de ID de fluxo implícitos do OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como aplicativos de página única do JavaScript. | `false` |
| `oauth2Permissions` | Coleção | Especifica a coleção de escopos de permissão OAuth 2,0 que o aplicativo de API da Web (recurso) expõe para aplicativos cliente. Esses escopos de permissão podem ser concedidos a aplicativos cliente durante o consentimento. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Booleano | Especifica se, como parte das solicitações de token do OAuth 2,0, o AD do Azure permitirá solicitações POST, em oposição às solicitações GET. O padrão é false, que especifica que somente as solicitações GET serão permitidas. | `false` |
| `parentalControlSettings` | Cadeia | `countriesBlockedForMinors` especifica os países nos quais o aplicativo está bloqueado para menores.<br>`legalAgeGroupRule` especifica a regra de grupo de idade legal que se aplica aos usuários do aplicativo. Pode ser definido como `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`ou `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Coleção | Consulte a descrição para a propriedade `keyCredentials`. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Coleção | Lista os aplicativos e as permissões solicitadas para consentimento implícito. Requer que um administrador tenha fornecido consentimento para o aplicativo. preAuthorizedApplications não exigem que o usuário concorde com as permissões solicitadas. As permissões listadas em preAuthorizedApplications não exigem o consentimento do usuário. No entanto, todas as permissões adicionais solicitadas não listadas em preAuthorizedApplications exigem o consentimento do usuário. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Booleano | Especifica se este aplicativo é um cliente público (como um aplicativo instalado em execução em um dispositivo móvel). <br><br> _Observação: isso está disponível apenas na experiência Registros de aplicativo (herdada). Substituído por `allowPublicClient` na experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `publisherDomain` | Cadeia | O domínio do Publicador verificado para o aplicativo. Somente leitura. | https://www.contoso.com |
| `replyUrls` | matriz de cadeia de caracteres | Essa propriedade de vários valores contém a lista de valores de redirect_uri registrados que o AD do Azure aceitará como destinos ao retornar tokens. <br><br> _Observação: isso está disponível apenas na experiência Registros de aplicativo (herdada). Substituído por `replyUrlsWithType` na experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `replyUrlsWithType` | Coleção | Essa propriedade de vários valores contém a lista de valores de redirect_uri registrados que o AD do Azure aceitará como destinos ao retornar tokens. Cada valor de URI deve conter um valor de tipo de aplicativo associado. Os valores de tipo com suporte são: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> Saiba mais sobre [restrições e limitações do replyUrl](https://docs.microsoft.com/azure/active-directory/develop/reply-url). | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Coleção | Com o consentimento dinâmico, `requiredResourceAccess` orienta a experiência de consentimento do administrador e a experiência de consentimento do usuário para usuários que estão usando consentimento estático. No entanto, isso não orienta a experiência de consentimento do usuário para o caso geral.<br>`resourceAppId` é o identificador exclusivo para o recurso ao qual o aplicativo requer acesso. Esse valor deve ser igual ao appId declarado no aplicativo de recurso de destino.<br>`resourceAccess` é uma matriz que lista os escopos de permissão OAuth 2.0 e as funções de aplicativo que o aplicativo requer do recurso especificado. Contém os valores `id` e `type` dos recursos especificados. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | Cadeia | A URL para os metadados SAML para o aplicativo. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | Cadeia | Especifica a URL para a home page do aplicativo. | `https://MyRegisteredApp` |
| `signInAudience` | Cadeia | Especifica quais contas da Microsoft têm suporte para o aplicativo atual. Os valores suportados são:<ul><li>**AzureADMyOrg** -usuários com uma conta corporativa ou de estudante da Microsoft no locatário do Azure ad da minha organização (ou seja, um único locatário)</li><li>**AzureADMultipleOrgs** -usuários com uma conta corporativa ou de estudante da Microsoft em qualquer locatário do Azure ad da organização (ou seja, multilocatário)</li> <li>**AzureADandPersonalMicrosoftAccount** -usuários com um conta Microsoft pessoal ou uma conta corporativa ou de estudante no locatário do Azure AD de qualquer organização</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | Cadeias de caracteres personalizadas que podem ser usadas para categorizar e identificar o aplicativo. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Problemas comuns

### <a name="manifest-limits"></a>Limites de manifesto

Um manifesto de aplicativo tem vários atributos que são chamados de coleções; por exemplo, approles, keycredentials, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess e oauth2Permissions. No manifesto completo do aplicativo para qualquer aplicativo, o número total de entradas em todas as coleções combinadas tem sido limitado em 1200. Se você já tiver um URI de redirecionamento de 100 especificado no manifesto do aplicativo, você terá apenas a 1100 entradas restantes para usar em todas as outras coleções combinadas que compõem o manifesto.

> [!NOTE]
> Caso você tente adicionar mais de 1200 entradas no manifesto do aplicativo, você poderá ver um erro **"falha ao atualizar o aplicativo xxxxxx. Detalhes do erro: o tamanho do manifesto excedeu seu limite. Reduza o número de valores e repita a solicitação. "**

### <a name="unsupported-attributes"></a>Atributos sem suporte

O manifesto do aplicativo representa o esquema do modelo de aplicativo subjacente no Azure AD. Conforme o esquema subjacente evolui, o editor de manifesto será atualizado para refletir o novo esquema de tempos em tempos. Como resultado, você pode notar novos atributos aparecendo no manifesto do aplicativo. Em raras ocasiões, você pode notar uma alteração sintática ou semântica nos atributos existentes ou pode encontrar um atributo que existia anteriormente não é mais suportado. Por exemplo, você verá novos atributos no [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) que são conhecidos com um nome diferente na experiência de registros de aplicativo (herdada).


| Registros de aplicativo (Herdado)| Registos de aplicações           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Para obter descrições para esses atributos, consulte a seção de [referência do manifesto](#manifest-reference) .

Ao tentar carregar um manifesto baixado anteriormente, você poderá ver um dos erros a seguir. Isso é provável porque o editor de manifesto agora dá suporte a uma versão mais recente do esquema, que não corresponde ao que você está tentando carregar.

- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: identificador de objeto ' indefinido ' inválido. [].** "
- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: um ou mais valores de propriedade especificados são inválidos. [].** "
- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: não é permitido definir availableToOtherTenants nesta versão da API para atualização. [].** "
- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: as atualizações para a propriedade ' replyUrls ' não são permitidas para este aplicativo. Use a propriedade ' replyUrlsWithType ' em seu lugar. [].** "
- "**Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: um valor sem um nome de tipo foi encontrado e nenhum tipo esperado está disponível. Quando o modelo é especificado, cada valor na carga deve ter um tipo que pode ser especificado na carga, explicitamente pelo chamador ou inferido implicitamente do valor pai. []** "

Quando você vir um desses erros, recomendamos o seguinte:

1. Edite os atributos individualmente no editor de manifesto em vez de carregar um manifesto baixado anteriormente. Use a tabela de [referência de manifesto](#manifest-reference) para entender a sintaxe e a semântica dos atributos novos e antigos para que você possa editar com êxito os atributos nos quais está interessado. 
1. Se o fluxo de trabalho exigir que você salve os manifestos no repositório de origem para uso posterior, sugerimos a REBASE dos manifestos salvos em seu repositório com aquele que você vê na experiência de **registros de aplicativo** .

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a relação entre o aplicativo de um aplicativo e objetos de entidade de serviço, consulte [objetos de aplicativo e entidade de serviço no Azure ad](app-objects-and-service-principals.md).
* Consulte o [Glossário de desenvolvedor da plataforma de identidade da Microsoft](developer-glossary.md) para obter definições de alguns dos principais conceitos de desenvolvedor da plataforma de identidade da Microsoft.

Use a seção de comentários a seguir para fornecer comentários que ajudam a refinar e formatar nosso conteúdo.

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
