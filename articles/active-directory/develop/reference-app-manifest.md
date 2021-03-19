---
title: Compreender o manifesto da app Azure Ative Directory
titleSuffix: Microsoft identity platform
description: Cobertura detalhada do manifesto da app Azure Ative Directory, que representa a configuração de identidade de uma aplicação num inquilino AZure AD, e é usado para facilitar a autorização da OAuth, experiência de consentimento, e muito mais.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 02/02/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 199d85d2805b92e1421330be8f299c769637b649
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578469"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto da aplicação do Azure Active Directory

O manifesto da aplicação contém uma definição de todos os atributos de um objeto de aplicação na plataforma de identidade da Microsoft. Também serve como um mecanismo para atualizar o objeto de aplicação. Para obter mais informações sobre a entidade aplicação e o seu esquema, consulte a documentação da [entidade de aplicação da API](/graph/api/resources/application)do gráfico.

Pode configurar os atributos de uma aplicação através do portal Azure ou utilizar programáticamente [a API](/graph/api/resources/application) ou [o PowerShell](/powershell/module/azuread#applications). No entanto, existem alguns cenários em que será necessário editar o manifesto da app para configurar o atributo de uma aplicação. Os cenários incluem:

* Se registou a aplicação como multi-inquilino e contas pessoais da Microsoft, não pode alterar as contas da Microsoft suportadas na UI. Em vez disso, deve utilizar o editor manifesto de aplicação para alterar o tipo de conta suportada.
* Para definir permissões e funções que a sua aplicação suporta, tem de modificar o manifesto da aplicação.

## <a name="configure-the-app-manifest"></a>Configure o manifesto da aplicação

Para configurar o manifesto de aplicação:

1. Aceda ao <a href="https://portal.azure.com/" target="_blank">Portal do Azure</a>. Procure e selecione o serviço **Azure Ative Directory.**
1. Selecione **Registos de aplicações**.
1. Selecione a aplicação que pretende configurar.
1. A partir da página **Descrição geral** da aplicação, selecione a secção **Manifesto**. Abre um editor manifesto baseado na web, permitindo-lhe editar o manifesto dentro do portal. Opcionalmente, pode selecionar **Download** para editar o manifesto localmente e, em seguida, usar **upload** para reaplicá-lo à sua aplicação.

## <a name="manifest-reference"></a>Referência manifesta

Esta secção descreve os atributos encontrados no manifesto de aplicação.

### <a name="id-attribute"></a>atributo id

| Chave | Tipo de valor |
| :--- | :--- |
| ID | String |

O identificador único para a aplicação no diretório. Este ID não é o identificador utilizado para identificar a aplicação em qualquer transação protocolar. É usado para fazer referência ao objeto em consultas de diretório.

Exemplo:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion attribute

| Chave | Tipo de valor |
| :--- | :--- |
| accessTokenAcceptedVersion | Nullable Int32 |

Especifica a versão simbólica de acesso esperada pelo recurso. Este parâmetro altera a versão e o formato do JWT produzido independentemente do ponto final ou cliente utilizado para solicitar o token de acesso.

O ponto final utilizado, v1.0 ou v2.0, é escolhido pelo cliente e só afeta a versão de id_tokens. Os recursos precisam de configurar explicitamente `accesstokenAcceptedVersion` para indicar o formato de token de acesso suportado.

Valores possíveis `accesstokenAcceptedVersion` para são 1, 2 ou nulos. Se o valor for nulo, este parâmetro falha em 1, o que corresponde ao ponto final v1.0.

Se `signInAudience` `AzureADandPersonalMicrosoftAccount` for, o valor deve ser `2` .

Exemplo:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>atributo addIns

| Chave | Tipo de valor |
| :--- | :--- |
| addIns | Coleção |

Define o comportamento personalizado que um serviço de consumo pode usar para chamar uma app em contextos específicos. Por exemplo, as aplicações que podem renderizar fluxos de ficheiros podem definir a propriedade para a `addIns` sua funcionalidade "FileHandler". Este parâmetro permitirá que serviços como o Microsoft 365 liguem para a aplicação no contexto de um documento em que o utilizador está a trabalhar.

Exemplo:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>permitir AtributoPublicClient

| Chave | Tipo de valor |
| :--- | :--- |
| permitirPublicClient | Booleano |

Especifica o tipo de aplicação de recuo. A Azure AD infere o tipo de aplicação a partir da respostaUrlsWithType por padrão. Existem certos cenários em que a Azure AD não consegue determinar o tipo de aplicação do cliente. Por exemplo, um desses cenários é o fluxo [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) onde o pedido HTTP acontece sem uma reorientação de URL). Nesses casos, a Azure AD interpretará o tipo de aplicação com base no valor deste imóvel. Se este valor for definido para ser verdadeiro, o tipo de aplicação de retorno é definido como cliente público, como uma aplicação instalada em execução num dispositivo móvel. O valor predefinido é falso, o que significa que o tipo de aplicação de retorno é cliente confidencial, como a aplicação web.

Exemplo:

```json
    "allowPublicClient": false,
```

### <a name="appid-attribute"></a>atributo appId

| Chave | Tipo de valor |
| :--- | :--- |
| appId | String |

Especifica o identificador único para a aplicação que é atribuída a uma aplicação pela Azure AD.

Exemplo:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles atribuem

| Chave | Tipo de valor |
| :--- | :--- |
| appRoles | Coleção |

Especifica a recolha de funções que uma aplicação pode declarar. Estas funções podem ser atribuídas a utilizadores, grupos ou diretores de serviço. Para mais exemplos e informações, consulte [as funções da aplicação adicionar na sua aplicação e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md).

Exemplo:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="errorurl-attribute"></a>atributo errorUrl

| Chave | Tipo de valor |
| :--- | :--- |
| errorUrl | String |

Sem apoio.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims atribuem

| Chave | Tipo de valor |
| :--- | :--- |
|groupMembershipClaims | String |

Configura a `groups` reclamação emitida num utilizador ou no token de acesso OAuth 2.0 que a app espera. Para definir este atributo, utilize um dos seguintes valores de cadeia válidos:

- `"None"`
- `"SecurityGroup"` (para grupos de segurança e funções AD Azure)
- `"ApplicationGroup"` (esta opção inclui apenas grupos que são atribuídos à aplicação)
- `"DirectoryRole"` (obtém as funções de diretório AD Azure de que o utilizador é membro)
- `"All"` (isto irá obter todos os grupos de segurança, grupos de distribuição e papel de diretório AD Azure de que o utilizador inscrito é membro).

Exemplo:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="optionalclaims-attribute"></a>opcional Atribuir atribuições

| Chave | Tipo de valor |
| :--- | :--- |
| opcionalClaims | String |

As reclamações opcionais devolvidas no token pelo serviço de fichas de segurança para esta aplicação específica.

Neste momento, as aplicações que suportam contas pessoais e AZure AD (registadas através do portal de registo de aplicações) não podem utilizar reclamações opcionais. No entanto, as aplicações registadas apenas para Azure AD usando o ponto final v2.0 podem obter as alegações opcionais que solicitaram no manifesto. Para mais informações, consulte [reclamações opcionais.](active-directory-optional-claims.md)

Exemplo:

```json
    "optionalClaims": null,
```


### <a name="identifieruris-attribute"></a>atributo identifierUris

| Chave | Tipo de valor |
| :--- | :--- |
| identifierUris | Matriz de Cordas |

URI(s) definidos pelo utilizador que identificam exclusivamente uma aplicação Web dentro do seu inquilino AZure AD, ou dentro de um domínio personalizado verificado se a app for multi-inquilino.

Exemplo:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>atributo informationalUrls

| Chave | Tipo de valor |
| :--- | :--- |
| informationalUrls | String |

Especifica os links para os termos de serviço e declaração de privacidade da aplicação. Os termos de serviço e declaração de privacidade são divulgados aos utilizadores através da experiência de consentimento do utilizador. Para obter mais informações, consulte [Como: Adicionar Termos de serviço e declaração de privacidade para aplicações AD registadas.](howto-add-terms-of-service-privacy-statement.md)

Exemplo:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>atributo keyCredentials

| Chave | Tipo de valor |
| :--- | :--- |
| keyCredentials | Coleção |

Contém referências a credenciais atribuídas a aplicações, segredos partilhados baseados em cordas e certificados X.509. Estas credenciais são usadas quando solicitam fichas de acesso (quando a aplicação está a agir como cliente e não como um recurso).

Exemplo:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>atributo De Aplicações de Client

| Chave | Tipo de valor |
| :--- | :--- |
| conhecidosClientApplicações | Matriz de Cordas |

Usado para agregar o consentimento se tiver uma solução que contenha duas partes: uma aplicação para clientes e uma aplicação API personalizada na Web. Se introduzir o appID da app do cliente neste valor, o utilizador só terá de consentir uma vez na aplicação do cliente. A Azure AD saberá que consentir com o cliente significa consentir implicitamente na API web. Irá automaticamente providenciar os principais de serviço para o cliente e a API web ao mesmo tempo. Tanto o cliente como a aplicação web API devem estar registados no mesmo inquilino.

Exemplo:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>atributo logoUrl

| Chave | Tipo de valor |
| :--- | :--- |
| logoUrl | String |

Leia apenas o valor que aponta para o URL do CDN para o logotipo que foi carregado no portal.

Exemplo:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>atributo logoutUrl

| Chave | Tipo de valor |
| :--- | :--- |
| logoutUrl | String |

O URL para sair da aplicação.

Exemplo:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>atributo de nome

| Chave | Tipo de valor |
| :--- | :--- |
| name | String |

O nome do visor da aplicação.

Exemplo:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>atributo oauth2AllowImplicitFlow

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2AllowImplicitFlow | Booleano |

Especifica se esta aplicação web pode solicitar fichas implícitas de acesso ao fluxo OAuth2.0. O padrão é falso. Esta bandeira é usada para aplicações baseadas no navegador, como aplicações de página única JavaScript. Para saber mais, `OAuth 2.0 implicit grant flow` insira na tabela de conteúdos e veja os tópicos sobre o fluxo implícito.

Exemplo:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>atributo oauth2AllowIdTokenImplicitFlow

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Booleano |

Especifica se esta aplicação web pode solicitar fichas implícitas de ID de fluxo OAuth2.0. O padrão é falso. Esta bandeira é usada para aplicações baseadas no navegador, como aplicações de página única JavaScript.

Exemplo:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>atributo oauth2Permissions

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2Permissions | Coleção |

Especifica a recolha de âmbitos de permissões OAuth 2.0 que a aplicação web API (recurso) expõe a aplicações de clientes. Estes âmbitos de permissão podem ser concedidos a aplicações do cliente durante o consentimento.

Exemplo:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>atributo oauth2RequiredPostResponse

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2RequiredPostResponse | Booleano |

Especifica se, como parte dos pedidos simbólicos da OAuth 2.0, a Azure AD permitirá pedidos postais, em oposição aos pedidos GET. O padrão é falso, o que especifica que apenas serão permitidos pedidos GET.

Exemplo:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>atributo parentalControlSettings

| Chave | Tipo de valor |
| :--- | :--- |
| parentalControlSettings | String |

- `countriesBlockedForMinors` especifica os países/regiões em que a aplicação está bloqueada para menores.
- `legalAgeGroupRule` especifica a regra do grupo de idade legal que se aplica aos utilizadores da app. Pode ser definido para `Allow` , , , , ou `RequireConsentForPrivacyServices` `RequireConsentForMinors` `RequireConsentForKids` `BlockMinors` .

Exemplo:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordSascenciais atribuem

| Chave | Tipo de valor |
| :--- | :--- |
| passwordSCredentias | Coleção |

Consulte a descrição da `keyCredentials` propriedade.

Exemplo:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>pré-autoria de aplicações de aplicações

| Chave | Tipo de valor |
| :--- | :--- |
| pré-autoria de aplicações | Coleção |

Lista pedidos e permissões solicitadas para consentimento implícito. Requer que um administrador tenha dado consentimento ao pedido. Pré-Autorizem As aplicações de aplicações não exigem que o utilizador consinta nas permissões solicitadas. As permissões listadas em Aplicações pré-autorizadas não requerem o consentimento do utilizador. No entanto, quaisquer permissões solicitadas adicionais não listadas em Aplicações pré-autorizadas requerem o consentimento do utilizador.

Exemplo:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publisherdomain-attribute"></a>atributo publisherDomain

| Chave | Tipo de valor |
| :--- | :--- |
| publisherDomain | String |

O domínio do editor verificado para a aplicação. Só para ler.

Exemplo:

```json
    "publisherDomain": "{tenant}.onmicrosoft.com",
```

### <a name="replyurlswithtype-attribute"></a>atributo answerUrlsWithType

| Chave | Tipo de valor |
| :--- | :--- |
| respostaUrlsWithType | Coleção |

Esta propriedade multi-valor detém a lista de valores de redirect_uri registados que a Azure AD aceitará como destinos ao devolver fichas. Cada valor URI deve conter um valor de tipo de aplicação associado. Os valores do tipo suportado são:

- `Web`
- `InstalledClient`
- `Spa`

Para saber mais, consulte [as restrições e limitações de respostaUrl](./reply-url.md).

Exemplo:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>atributo requeredResourceAccess

| Chave | Tipo de valor |
| :--- | :--- |
| requeredResourceAccess | Coleção |

Com o consentimento dinâmico, `requiredResourceAccess` impulsiona a experiência de consentimento de administração e a experiência de consentimento do utilizador para os utilizadores que estão a usar o consentimento estático. No entanto, este parâmetro não impulsiona a experiência de consentimento do utilizador para o caso geral.

- `resourceAppId` é o identificador único para o recurso a que a app requer acesso. Este valor deve ser igual ao appId declarado na aplicação de recursos-alvo.
- `resourceAccess` é um conjunto que lista os âmbitos de permissão OAuth2.0 e as funções de aplicação que a aplicação requer a partir do recurso especificado. Contém os `id` valores e `type` valores dos recursos especificados.

Exemplo:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>atributo samlMetadataUrl

| Chave | Tipo de valor |
| :--- | :--- |
| samlMetadataUrl | String |

O URL para os metadados SAML para a aplicação.

Exemplo:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>atributo signInUrl

| Chave | Tipo de valor |
| :--- | :--- |
| signInUrl | String |

Especifica o URL para a página inicial da aplicação.

Exemplo:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience atributo

| Chave | Tipo de valor |
| :--- | :--- |
| signInAudience | String |

Especifica quais as contas da Microsoft suportadas para a aplicação atual. Os valores suportados são:
- `AzureADMyOrg` - Utilizadores com uma conta de trabalho ou escola da Microsoft no inquilino AZure AD da minha organização (por exemplo, inquilino único)
- `AzureADMultipleOrgs` - Utilizadores com uma conta de trabalho ou escola da Microsoft no inquilino AZure AD de qualquer organização (por exemplo, multi-inquilino)
- `AzureADandPersonalMicrosoftAccount` - Utilizadores com uma conta pessoal da Microsoft, ou uma conta de trabalho ou escola no inquilino AD Azure de qualquer organização
- `PersonalMicrosoftAccount` - Contas pessoais que são usadas para iniciar scontabilidade em serviços como Xbox e Skype.

Exemplo:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>tags atribuem

| Chave | Tipo de valor |
| :--- | :--- |
| etiquetas | Matriz de Cordas  |

Cordas personalizadas que podem ser usadas para categorizar e identificar a aplicação.

Exemplo:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Problemas comuns

### <a name="manifest-limits"></a>Limites manifestos

Um manifesto de aplicação tem múltiplos atributos que são referidos como coleções; por exemplo, appRoles, keyCredentials, conhecidosClientApplications, identifierUris, redirectUris, resourceAccess e oauth2Permissions. Dentro do manifesto de aplicação completo para qualquer aplicação, o número total de entradas em todas as coleções combinadas foi limitado a 1200. Se especificar previamente 100 URIs de redirecionamento no manifesto de aplicação, então só fica com 1100 entradas restantes para usar em todas as outras coleções combinadas que compõem o manifesto.

> [!NOTE]
> Caso tente adicionar mais de 1200 entradas no manifesto de aplicação, poderá ver um erro **"Falhou na atualização da aplicação xxxxxx. Detalhes de erro: O tamanho do manifesto excedeu o seu limite. Por favor, reduza o número de valores e relemisça o seu pedido."**

### <a name="unsupported-attributes"></a>Atributos não suportados

O manifesto de aplicação representa o esquema do modelo de aplicação subjacente no Azure AD. À medida que o esquema subjacente evolui, o editor manifesto será atualizado para refletir o novo esquema de vez em quando. Como resultado, poderá notar novos atributos que aparecem no manifesto de aplicação. Em raras ocasiões, pode notar uma alteração sintática ou semântica nos atributos existentes ou pode encontrar um atributo que existiu anteriormente. Por exemplo, verá novos atributos nas inscrições da App , que são [conhecidos](https://go.microsoft.com/fwlink/?linkid=2083908)com um nome diferente na experiência de registos da App (Legacy).

| Inscrições de aplicativos (Legado)| Registos de aplicações           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Para obter descrições para estes atributos, consulte a secção [de referência manifesta.](#manifest-reference)

Quando tentar carregar um manifesto previamente descarregado, poderá ver um dos seguintes erros. Este erro é provável porque o editor manifesto agora suporta uma versão mais recente do esquema, que não coincide com a que está a tentar carregar.

* "Falhou na atualização da aplicação xxxxxx. Detalhe de erro: Identificador de objetos inválidos "indefinido". []."
* "Falhou na atualização da aplicação xxxxxx. Detalhe de erro: Um ou mais valores de propriedade especificados são inválidos. []."
* "Falhou na atualização da aplicação xxxxxx. Detalhe de erro: Não é permitido definir OsTenants disponíveis nesta versão api para atualização. []."
* "Falhou na atualização da aplicação xxxxxx. Detalhe de erro: As atualizações à propriedade 'answerUrls' não são permitidas para esta aplicação. Utilize a propriedade 'answerUrlsWithType'. []."
* "Falhou na atualização da aplicação xxxxxx. Detalhe de erro: Foi encontrado um valor sem um nome tipo e não existe qualquer tipo esperado. Quando o modelo é especificado, cada valor na carga útil deve ter um tipo que pode ser especificado na carga útil, explicitamente pelo chamador ou implicitamente inferido do valor principal. []"

Quando vir um destes erros, recomendamos as seguintes ações:

1. Edite os atributos individualmente no editor manifesto em vez de carregar um manifesto previamente descarregado. Use a tabela [de referência manifesta](#manifest-reference) para entender a sintaxe e a semântica de atributos antigos e novos para que possa editar com sucesso os atributos em que está interessado.
1. Se o seu fluxo de trabalho exigir que guarde os manifestos no seu repositório de origem para utilização posterior, sugerimos a rebasão dos manifestos guardados no seu repositório com o que vê na experiência de registos da **App.**

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a relação entre a aplicação de uma aplicação e o objeto principal de serviço, consulte [aplicações e objetos principais de serviço em Azure AD](app-objects-and-service-principals.md).
* Consulte o [glossário de desenvolvedores de plataformas de identidade](developer-glossary.md) da Microsoft para definições de alguns conceitos de desenvolvedores de plataformas de identidade da Microsoft.

Utilize a secção de comentários a seguir para fornecer feedback que ajude a refinar e moldar o nosso conteúdo.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: /graph/migrate-azure-ad-graph-planning-checklist
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/