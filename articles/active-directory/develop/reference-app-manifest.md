---
title: Compreender o manifesto da aplicação Azure Ative Directory Microsoft Docs
description: Cobertura detalhada do manifesto da aplicação Azure Ative Directory, que representa a configuração de identidade de uma aplicação num inquilino da AD Azure, e é usada para facilitar a autorização da OAuth, experiência de consentimento e muito mais.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: a12715ba9aac77461d4968bd9b8f3de30af243c4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262753"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto de aplicativo sony Azure Ative Diretório

O manifesto de aplicação contém uma definição de todos os atributos de um objeto de aplicação na plataforma de identidade da Microsoft. Também serve como um mecanismo para atualizar o objeto de aplicação. Para obter mais informações sobre a entidade aplicação e o seu esquema, consulte a documentação da [entidade da Aplicação API graph.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)

Pode configurar os atributos de uma aplicação através do portal Azure ou utilizar programáticamente a [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) ou [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). No entanto, existem alguns cenários em que terá de editar o manifesto da aplicação para configurar o atributo de uma aplicação. Os cenários incluem:

* Se registou a aplicação como multi-inquilino si e contas pessoais da Microsoft, não pode alterar as contas suportadas da Microsoft no UI. Em vez disso, deve utilizar o editor manifesto de aplicação para alterar o tipo de conta suportada.
* Se precisar de definir permissões e funções que a sua aplicação suporta, tem de modificar o manifesto de aplicação.

## <a name="configure-the-app-manifest"></a>Configure o manifesto da aplicação

Para configurar o manifesto de candidatura:

1. Aceda ao [Portal do Azure](https://portal.azure.com). Procure e selecione o serviço **de Diretório Ativo Azure.**
1. Selecione **Registos das aplicações**.
1. Selecione a aplicação que pretende configurar.
1. A partir da página **Descrição geral** da aplicação, selecione a secção **Manifesto**. Abre-se um editor manifesto baseado na web, permitindo-lhe editar o manifesto dentro do portal. Opcionalmente, pode selecionar **O Download** para editar o manifesto localmente e, em seguida, utilizar o **Upload** para o reaplicar à sua aplicação.

## <a name="manifest-reference"></a>Referência manifesta


### <a name="key-value-type-accesstokenacceptedversion-nullable-int32"></a>Chave, Valor: `accessTokenAcceptedVersion`, Int32 Nuveível 
Especifica a versão simbólica de acesso esperada pelo recurso. Este parâmetro altera a versão e o formato do JWT produzido independentemente do ponto final ou cliente utilizado para solicitar o sinal de acesso.

O ponto final utilizado, v1.0 ou v2.0, é escolhido pelo cliente e só afeta a versão de id_tokens. Os recursos precisam de configurar explicitamente `accesstokenAcceptedVersion` para indicar o formato de acesso suportado.

Os valores possíveis para `accesstokenAcceptedVersion` são 1, 2 ou nulos. Se o valor for nulo, este parâmetro desfaz-se em 1, o que corresponde ao ponto final v1.0. 

Se `signInAudience` for `AzureADandPersonalMicrosoftAccount`, o valor deve ser `2`  

Valor de exemplo: `2` 

### <a name="key-value-type-addins-collection"></a>Chave, Valor: `addIns`, Coleção 
Define o comportamento personalizado que um serviço consumista pode usar para chamar uma app em contextos específicos. Por exemplo, as aplicações que podem renderizar streams de ficheiros podem definir a propriedade addIns para a sua funcionalidade "FileHandler". Este parâmetro permitirá que serviços como o Office 365 liguem para a aplicação no contexto de um documento em que o utilizador está a trabalhar. 

Valor do exemplo: 
<code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>

### <a name="key-value-type-allowpublicclient-boolean"></a>Chave, Tipo de valor: `allowPublicClient`, Boolean 
Especifica o tipo de aplicação de recuo. A AD Azure infere o tipo de aplicação a partir da respostaUrlsWithType por padrão. Existem certos cenários em que a Azure AD não consegue determinar o tipo de aplicação do cliente. Por exemplo, um desses cenários é o fluxo [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) onde o pedido de HTTP acontece sem uma reorientação url). Nesses casos, a Azure AD interpretará o tipo de aplicação com base no valor deste imóvel. Se este valor for definido para ser verdade, o tipo de aplicação de recuo é definido como cliente público, como uma aplicação instalada que funciona num dispositivo móvel. O valor predefinido é falso, o que significa que o tipo de aplicação de recuo é cliente confidencial, como aplicação web. 

Valor de exemplo: `false` 

### <a name="key-value-type-availabletoothertenants-boolean"></a>Chave, Tipo de valor: `availableToOtherTenants`, Boolean 
verdade se o pedido for partilhado com outros inquilinos; caso contrário, falso. <br><br> Nota: Isto está disponível apenas na experiência de registos da **App (Legacy).** Substituído por `signInAudience` na experiência de registos da [App.](https://go.microsoft.com/fwlink/?linkid=2083908) 

### <a name="key-value-type-appid-string"></a>Chave, Tipo de valor: `appId`, Corda 
Especifica o identificador único para a aplicação que é atribuída a uma aplicação pela Azure AD. 

Valor de exemplo: `"601790de-b632-4f57-9523-ee7cb6ceba95"` 

### <a name="key-value-type-approles-collection"></a>Chave, Valor: `appRoles`, Coleção 
Especifica a recolha de funções que uma aplicação pode declarar. Estas funções podem ser atribuídas a utilizadores, grupos ou diretores de serviço. Para mais exemplos e informações, consulte Adicionar funções de [aplicação na sua aplicação e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md) 

Valor do exemplo: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  

### <a name="key-value-type-displayname-string"></a>Chave, Tipo de valor: `displayName`, Corda 
O nome de exibição da aplicação. <br><br> Nota: Isto está disponível apenas na experiência de registos da **App (Legacy).** Substituído por `name` na experiência de registos da [App.](https://go.microsoft.com/fwlink/?linkid=2083908) 

Valor de exemplo: `"MyRegisteredApp"` 

### <a name="key-value-type-errorurl-string"></a>Chave, Tipo de valor: `errorUrl`, Corda 
Não suportado. 

### <a name="key-value-type-groupmembershipclaims-string"></a>Chave, Tipo de valor: `groupMembershipClaims`, Corda 
Configura a alegação `groups` emitida num utilizador ou no token de acesso OAuth 2.0 que a aplicação espera. Para definir este atributo, utilize um dos seguintes valores de cadeia válidos:
- `"None"`
- `"SecurityGroup"` (para grupos de segurança e funções azure ad)
- `"All"` (isto terá todos os grupos de segurança, grupos de distribuição e funções de diretório Azure AD que o utilizador inscrito é membro. 

Valor de exemplo: `"SecurityGroup"` 

### <a name="key-value-type-homepage-string"></a>Chave, Tipo de valor: `homepage`, Corda 
O URL para a página inicial da aplicação. <br><br> Nota: Isto está disponível apenas na experiência de registos da **App (Legacy).** Substituído por `signInUrl` na experiência de registos da [App.](https://go.microsoft.com/fwlink/?linkid=2083908) 

Valor de exemplo: `"https://MyRegisteredApp"` 

### <a name="key-value-type-objectid-string"></a>Chave, Tipo de valor: `objectId`, Corda 
O identificador único para a app no diretório. <br><br> Isto está disponível apenas na experiência de registos da **App (Legacy).** Substituído por `id` na experiência de registos da [App.](https://go.microsoft.com/fwlink/?linkid=2083908) 

Valor de exemplo: `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` 

### <a name="key-value-type-optionalclaims-string"></a>Chave, Tipo de valor: `optionalClaims`, Corda 
As reclamações opcionais devolvidas no símbolo pelo serviço de fichas de segurança para esta aplicação específica.<br>Neste momento, as aplicações que suportam contas pessoais e AD Azure (registada através do portal de registo de aplicações) não podem utilizar reclamações opcionais. No entanto, as aplicações registadas apenas para AD Azure utilizando o ponto final v2.0 podem obter as reclamações opcionais que solicitaram no manifesto. Para mais informações, consulte [reclamações opcionais.](active-directory-optional-claims.md) 

Valor do exemplo:  
`null` 

### <a name="key-value-type-id-string"></a>Chave, Tipo de valor: `id`, Corda 
O identificador único para a app no diretório. Este ID não é o identificador utilizado para identificar a aplicação em qualquer transação de protocolo. É usado para referenciar o objeto em consultas de diretório. 

Valor de exemplo: `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` 

### <a name="key-value-type-identifieruris-string-array"></a>Chave, tipo de valor: `identifierUris`, String Array 
URI(s) definido pelo utilizador que identifica uma aplicação Web exclusivamente dentro do seu inquilino Azure AD, ou dentro de um domínio personalizado verificado se a aplicação for multi-inquilino. 

Valor do exemplo: 
<code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> 

### <a name="key-value-type-informationalurls-string"></a>Chave, Tipo de valor: `informationalUrls`, Corda 
Especifica os links para os termos de serviço e declaração de privacidade da aplicação. Os termos de serviço e declaração de privacidade são divulgados aos utilizadores através da experiência de consentimento do utilizador. Para mais informações, consulte [Como: Adicionar Termos de serviço e declaração de privacidade para aplicações AD Azure registadas](howto-add-terms-of-service-privacy-statement.md). 

Valor do exemplo: 
<code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> 

### <a name="key-value-type-keycredentials-collection"></a>Chave, Valor: `keyCredentials`, Coleção 
Detém referências a credenciais atribuídas a apps, segredos partilhados baseados em cordas e certificados X.509. ). 

Valor do exemplo: 
<code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-knownclientapplications-string-array"></a>Chave, tipo de valor: `knownClientApplications`, String Array 
Usado para agregar o consentimento se tiver uma solução que contenha duas partes: uma aplicação de cliente e uma aplicação API web personalizada. Se introduzir o appID da aplicação do cliente neste valor, o utilizador só terá de consentir uma vez na aplicação do cliente. A Azure AD saberá que consentir com o cliente significa consentir implicitamente com a Web API. Fornecerá automaticamente os principais de serviços tanto para o cliente como para a Web API ao mesmo tempo. Tanto o cliente como a aplicação Web API devem ser registados no mesmo inquilino. 

Valor de exemplo: `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` 

### <a name="key-value-type-logourl-string"></a>Chave, Tipo de valor: `logoUrl`, Corda 
Leia apenas o valor que aponta para o URL da CDN para o logotipo que foi carregado no portal. 

Valor de exemplo: `"https://MyRegisteredAppLogo"` 

### <a name="key-value-type-logouturl-string"></a>Chave, Tipo de valor: `logoutUrl`, Corda 
O URL para sair da aplicação. 

Valor do exemplo:  
`"https://MyRegisteredAppLogout"` 

### <a name="key-value-type-name-string"></a>Chave, Tipo de valor: `name`, Corda 
O nome de exibição da aplicação. 

Valor de exemplo: `"MyRegisteredApp"` 

### <a name="key-value-type-oauth2allowimplicitflow-boolean"></a>Chave, Tipo de valor: `oauth2AllowImplicitFlow`, Boolean 
Especifica se esta aplicação web pode solicitar fichas implícitas de acesso ao fluxo OAuth2.0. O padrão é falso. Esta bandeira é usada para aplicações baseadas no navegador, como aplicações de página única JavaScript. Para saber mais, insira `OAuth 2.0 implicit grant flow` na tabela de conteúdos e veja os tópicos sobre o fluxo implícito. 

Valor de exemplo: `false` 

### <a name="key-value-type-oauth2allowidtokenimplicitflow-boolean"></a>Chave, Tipo de valor: `oauth2AllowIdTokenImplicitFlow`, Boolean 
Especifica se esta aplicação web pode solicitar fichas de ID de fluxo implícita oAuth2.0. O padrão é falso. Esta bandeira é usada para aplicações baseadas no navegador, como aplicações de página única JavaScript. 

Valor de exemplo: `false` 

### <a name="key-value-type-oauth2permissions-collection"></a>Chave, Valor: `oauth2Permissions`, Coleção 
Especifica a recolha de âmbitos de permissão OAuth 2.0 que a aplicação web API (recurso) expõe às aplicações dos clientes. Estes âmbitos de permissão podem ser concedidos a aplicações de clientes durante o consentimento. 

Valor do exemplo: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>

### <a name="key-value-type-oauth2requiredpostresponse-boolean"></a>Chave, Tipo de valor: `oauth2RequiredPostResponse`, Boolean 
Especifica se, como parte dos pedidos de token OAuth 2.0, a Azure AD permitirá pedidos de CORREIO, em oposição aos pedidos do GET. O padrão é falso, o que especifica que apenas serão permitidos pedidos GET. 

Valor de exemplo: `false` 

### <a name="key-value-type-parentalcontrolsettings-string"></a>Chave, Tipo de valor: `parentalControlSettings`, Corda 

`countriesBlockedForMinors` especifica os países em que a app está bloqueada para menores.<br>`legalAgeGroupRule` especifica a regra do grupo etário legal que se aplica aos utilizadores da app. Pode ser definido para `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`ou `BlockMinors`.  

Valor do exemplo: 
<code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> 

### <a name="key-value-type-passwordcredentials-collection"></a>Chave, Valor: `passwordCredentials`, Coleção 
Consulte a descrição da propriedade `keyCredentials`. 

Valor do exemplo: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> 

### <a name="key-value-type-preauthorizedapplications-collection"></a>Chave, Valor: `preAuthorizedApplications`, Coleção 
Lista pedidos e permissões solicitadas para consentimento implícito. Requer que um administrador tenha dado consentimento ao pedido. pré-AutorizadoSAplicações não requerem que o utilizador consinta nas permissões solicitadas. Permissões listadas em aplicações pré-autorizadas não requerem o consentimento do utilizador. No entanto, quaisquer permissões solicitadas adicionais não listadas em Aplicações pré-autorizadas requerem o consentimento do utilizador. 

Valor do exemplo: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-publicclient-boolean"></a>Chave, Tipo de valor: `publicClient`, Boolean 
Especifica se esta aplicação é um cliente público (como uma aplicação instalada em execução num dispositivo móvel). 

Este imóvel está disponível apenas na experiência de registos da **App (Legacy).** Substituído por `allowPublicClient` na experiência de registos da [App.](https://go.microsoft.com/fwlink/?linkid=2083908) 

### <a name="key-value-type-publisherdomain-string"></a>Chave, Tipo de valor: `publisherDomain`, Corda 
O domínio da editora verificada para a aplicação. Só de leitura. 

Valor de exemplo: `https://www.contoso.com`

### <a name="key-value-type-replyurls-string-array"></a>Chave, Valor: `replyUrls`, String array 
Este imóvel de vários valores de valor de valor de valor redirect_uri registado que a Azure AD aceitará como destinos ao devolver fichas. <br><br> Este imóvel está disponível apenas na experiência de registos da **App (Legacy).** Substituído por `replyUrlsWithType` na experiência de registos da [App.](https://go.microsoft.com/fwlink/?linkid=2083908) 

### <a name="key-value-type-replyurlswithtype-collection"></a>Chave, Valor: `replyUrlsWithType`, Coleção 
Este imóvel de vários valores de valor de valor de valor redirect_uri registado que a Azure AD aceitará como destinos ao devolver fichas. Cada valor URI deve conter um valor associado do tipo de aplicação. Os valores de tipo suportados são: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> Saiba mais sobre [respostaRestrições e limitações deurl](https://docs.microsoft.com/azure/active-directory/develop/reply-url). 

Valor do exemplo: 
<code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-requiredresourceaccess-collection"></a>Chave, Valor: `requiredResourceAccess`, Coleção 
Com consentimento dinâmico, `requiredResourceAccess` impulsiona a experiência de consentimento do administrador e a experiência de consentimento do utilizador para os utilizadores que estão a usar o consentimento estático. No entanto, este parâmetro não conduz a experiência de consentimento do utilizador para o caso geral.<br>`resourceAppId` é o identificador único para o recurso a que a app necessita de acesso. Este valor deve ser igual ao appId declarado na aplicação de recursos-alvo.<br>`resourceAccess` é uma matriz que lista os âmbitos de permissão OAuth2.0 e funções de aplicação que a aplicação requer a partir do recurso especificado. Contém os valores `id` e `type` dos recursos especificados. 

Valor do exemplo: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> 

### <a name="key-value-type-samlmetadataurl-string"></a>Chave, Tipo de valor: `samlMetadataUrl`, Corda 
O URL dos metadados SAML para a aplicação. 

Valor de exemplo: `https://MyRegisteredAppSAMLMetadata` 

### <a name="key-value-type-signinurl-string"></a>Chave, Tipo de valor: `signInUrl`, Corda 

Especifica o URL na página inicial da aplicação. 

Valor de exemplo: `https://MyRegisteredApp` 

### <a name="key-value-type-signinaudience-string"></a>Chave, Tipo de valor: `signInAudience`, Corda 
Especifica quais as contas da Microsoft suportadas para a aplicação atual. Os valores suportados são:
- **AzureADMyOrg** - Utilizadores com uma conta de trabalho ou escola da Microsoft no inquilino Da IA da minha organização (por exemplo, inquilino único)
- **AzureADMultipleOrgs** - Utilizadores com uma conta de trabalho ou escola da Microsoft em qualquer inquilino Azure AD de qualquer organização (por exemplo, multi-inquilino)
- **AzureADandPersonalMicrosoftAccount** - Utilizadores com uma conta pessoal da Microsoft, ou uma conta de trabalho ou escola em qualquer inquilino da AD Azure de qualquer organização 

Valor do exemplo:  
`AzureADandPersonalMicrosoftAccount` 

### <a name="key-value-type-tags-string-array"></a>Chave, tipo de valor: `tags`, String Array 
Cordas personalizadas que podem ser usadas para categorizar e identificar a aplicação. 

Valor do exemplo: 
<code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code>

## <a name="common-issues"></a>Problemas comuns

### <a name="manifest-limits"></a>Limites manifestos

Um manifesto de aplicação tem múltiplos atributos que são referidos como coleções; por exemplo, aplicações, credenciais de chave, aplicações conhecidas do ClientApplication, identificadorUris, rediretUris, necessárioSAccess e oauth2Permissions. Dentro do manifesto de candidatura completo para qualquer aplicação, o número total de entradas em todas as coleções combinadas foi limitado a 1200. Se especificar previamente 100 URIs redirecionais no manifesto de aplicação, só restam 1100 entradas restantes para utilizar em todas as outras coleções combinadas que compõem o manifesto.

> [!NOTE]
> Caso tente adicionar mais de 1200 entradas no manifesto de aplicação, poderá ver um erro **"Falhei em atualizar a aplicação xxxxxx. Detalhes do erro: O tamanho do manifesto excedeu o seu limite. Por favor, reduza o número de valores e retente o seu pedido."**

### <a name="unsupported-attributes"></a>Atributos não suportados

O manifesto de aplicação representa o esquema do modelo de aplicação subjacente em Azure AD. À medida que o esquema subjacente evolui, o editor manifesto será atualizado para refletir o novo esquema de vez em quando. Como resultado, poderá notar novos atributos que aparecem no manifesto de aplicação. Em raras ocasiões, poderá notar uma alteração sintática ou semântica nos atributos existentes ou pode encontrar um atributo que já existia já não são suportados. Por exemplo, você verá novos atributos nas [inscrições](https://go.microsoft.com/fwlink/?linkid=2083908)da App , que são conhecidos com um nome diferente na experiência de registos da App (Legacy).

| Registos de aplicativos (Legado)| Registos de aplicações           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Para obter descrições para estes atributos, consulte a secção [de referência manifesto.](#manifest-reference)

Quando tentar fazer o upload de um manifesto previamente descarregado, poderá ver um dos seguintes erros. Este erro é provável porque o editor manifesto agora suporta uma versão mais recente do esquema, que não combina com a que está a tentar carregar.

* "Falhei em atualizar a aplicação xxxxxx. Detalhe de erro: Identificador de objeto inválido 'indefinido'. []."
* "Falhei em atualizar a aplicação xxxxxx. Detalhe de erro: Um ou mais valores de propriedade especificados são inválidos. []."
* "Falhei em atualizar a aplicação xxxxxx. Detalhe de erro: Não é permitido definir disponíveisToOtherTenants nesta versão api para atualização. []."
* "Falhei em atualizar a aplicação xxxxxx. Detalhe de erro: As atualizações para a propriedade 'answerUrls' não são permitidas para esta aplicação. Utilize a propriedade 'answerUrlsWithType'. []."
* "Falhei em atualizar a aplicação xxxxxx. Detalhe de erro: Foi encontrado um valor sem um nome de tipo e não existe um tipo esperado. Quando o modelo é especificado, cada valor na carga deve ter um tipo que possa ser especificado na carga útil, explicitamente pelo chamador ou implicitamente inferido do valor-mãe. []"

Quando vir um destes erros, recomendamos as seguintes ações:

1. Editar os atributos individualmente no editor manifesto em vez de carregar um manifesto previamente descarregado. Use a tabela de [referência manifesto](#manifest-reference) para entender a sintaxe e a semântica de atributos antigos e novos para que possa editar com sucesso os atributos que lhe interessam. 
1. Se o seu fluxo de trabalho exigir que guarde os manifestos no seu repositório de origem para uso posterior, sugerimos que rebasteça os manifestos guardados no seu repositório com o que vê na experiência de registos da **App.**

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a relação entre a aplicação de uma aplicação e o principal objeto de serviço, consulte [os objetos principais da Aplicação e do serviço em Azure AD](app-objects-and-service-principals.md).
* Consulte o [glossário](developer-glossary.md) de desenvolvedor da plataforma de identidade microsoft para definições de alguns conceitos de desenvolvedor de plataformas de identidade da Microsoft.

Utilize a secção de comentários seguintes para fornecer feedback que ajude a refinar e moldar o nosso conteúdo.

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
