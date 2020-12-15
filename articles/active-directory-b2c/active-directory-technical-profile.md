---
title: Defina um perfil técnico AD AD Azure numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do Azure Ative Directory numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3f92bfe98a45117264c957481a75493de652abc9
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508106"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico do Azure Ative Directory numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) presta apoio à gestão do utilizador do Azure Ative Directory. Este artigo descreve as especificidades de um perfil técnico para interagir com um fornecedor de sinistros que suporta este protocolo padronizado.

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O **atributo do manipulador** deve conter o nome totalmente qualificado do conjunto do manipulador de `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` protocolos .

Seguindo [o pacote de iniciação de políticas personalizadas,](custom-policy-get-started.md#custom-policy-starter-pack) os perfis técnicos Azure AD incluem o perfil técnico **AAD-Common.** Os perfis técnicos da AD AZure não especificam o protocolo porque o protocolo está configurado no perfil técnico **AAD-Common:**
 
- **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingAlternativeSecurityId-NoError** - Procure uma conta social no diretório.
- **AAD-UserWriteUsingAlternativeSecurityId** - Crie uma nova conta social.
- **AAD-UserReadUsingEmailAddress** - Procure uma conta local no diretório.
- **AAD-UserWriteUsingLogonEmail** - Criar uma nova conta local.
- **AAD-UserWritePasswordUsingObjectId** - Atualize uma palavra-passe de uma conta local.
- **AAD-UserWriteProfileUsingObjectId** - Atualize um perfil de utilizador de uma conta local ou social.
- **AAD-UserReadUsingObjectId** - Leia um perfil de utilizador de uma conta local ou social.
- **AAD-UserWritePhoneNumberUsingObjectId** - Escreva o número de telefone do MFA de uma conta local ou social

O exemplo a seguir mostra o perfil técnico **AAD-Common:**

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

O elemento InputClaims contém uma reclamação, que é usada para procurar uma conta no diretório, ou criar uma nova. Deve haver exatamente um elemento InputClaim na coleção de reclamações de entrada para todos os perfis técnicos Azure AD. Poderá ser necessário mapear o nome da reclamação definida na sua política para o nome definido no Diretório Ativo Azure.

Para ler, atualizar ou eliminar uma conta de utilizador existente, a alegação de entrada é uma chave que identifica exclusivamente a conta no diretório AD Azure. Por exemplo, **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName**, ou **alternativeSecurityId**. 

Para criar uma nova conta de utilizador, a reivindicação de entrada é uma chave que identifica exclusivamente uma conta local ou federada. Por exemplo, conta local: **signInNames.emailAddress,** ou **signInNames.userName**. Para uma conta federada: a **alternativaSecurityId**.

O elemento [InputClaimsTransformations](technicalprofiles.md#input-claims-transformations) pode conter uma coleção de elementos de transformação de pedidos de entrada que são usados para modificar a alegação de entrada ou gerar um novo.

## <a name="outputclaims"></a>Resultados

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo perfil técnico Azure AD. Poderá ser necessário mapear o nome da reclamação definida na sua política para o nome definido no Diretório Ativo Azure. Também pode incluir reclamações que não são devolvidas pelo Diretório Ativo Azure, desde que desemconfiem do `DefaultValue` atributo.

O elemento [OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations) pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

Por exemplo, o perfil técnico **AAD-UserWriteUsingLogonEmail** cria uma conta local e devolve as seguintes reclamações:

- **objectId**, que é identificador da nova conta
- **newUser**, que indica se o utilizador é novo
- **autenticaçãoSource,** que define a autenticação para `localAccountAuthentication`
- **userPrincipalName**, que é o nome principal do utilizador da nova conta
- **signInNames.emailAddress**, que é o nome de entrada de conta, semelhante ao pedido de entrada **de e-mail**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistidoClaims

O elemento **PersistedClaims** contém todos os valores que devem ser persistidos pela Azure AD com possíveis informações de mapeamento entre um tipo de reclamação já definido na secção [ClaimsSchema](claimsschema.md) na política e o nome de atributo AD Azure.

O perfil técnico **AAD-UserWriteUsingLogonEmail,** que cria uma nova conta local, persiste na sequência de alegações:

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

O nome da reclamação é o nome do atributo AZure AD, a menos que o atributo **PartnerClaimType** seja especificado, que contém o nome de atributo AD Azure.

## <a name="requirements-of-an-operation"></a>Requisitos de uma operação

- Deve haver exatamente um elemento **InputClaim** no saco de reclamações para todos os perfis técnicos Azure AD.
- O [artigo de atributos](user-profile-attributes.md) de perfil de utilizador descreve os atributos suportados do perfil de utilizador Azure AD B2C que pode utilizar nas reclamações de entrada, alegações de saída e reclamações persistentes. 
- Se a operação for `Write` `DeleteClaims` ou, então também deve aparecer num elemento **PersistedClaims.**
- O valor da reclamação do **nome do utilizadorPrincipal Deve** estar no formato de `user@tenant.onmicrosoft.com` .
- A **alegação do nome de visualização** é necessária e não pode ser uma corda vazia.

## <a name="azure-ad-technical-provider-operations"></a>Operações de prestador técnico da Azure AD

### <a name="read"></a>Ler

A operação **Ler** lê dados sobre uma única conta de utilizador. O seguinte perfil técnico lê dados sobre uma conta de utilizador utilizando o objectId do utilizador:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Escrita

A operação **Write** cria ou atualiza uma única conta de utilizador. O seguinte perfil técnico cria uma nova conta social:

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

A operação **DeleteClaims** limpa as informações de uma lista de reclamações fornecida. O seguinte perfil técnico elimina as alegações:

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

A operação **DeleteClaimsPrincipal** elimina uma única conta de utilizador do diretório. O seguinte perfil técnico elimina uma conta de utilizador do diretório utilizando o nome principal do utilizador:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

O seguinte perfil técnico elimina uma conta de utilizador social utilizando **o AlternativeSecurityId**:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | A operação a ser realizada. Valores possíveis: `Read` `Write` , , ou `DeleteClaims` `DeleteClaimsPrincipal` . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Não | Mente um erro se o objeto do utilizador não existir no diretório. Valores possíveis: `true` ou `false` . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | Não | Levante um erro se o objeto do utilizador já existir. Valores possíveis: `true` ou `false` .|
| AplicaçãoObjectid | Não | O identificador de objeto de aplicação para atributos de extensão. Valor: ObjectId de uma aplicação. Para obter mais informações, consulte [Utilizar atributos personalizados.](user-flow-custom-attributes.md?pivots=b2c-custom-policy) |
| ClientId | Não | O identificador de cliente para aceder ao inquilino como um terceiro. Para obter mais informações, consulte [Utilizar atributos personalizados numa política de edição de perfil personalizado](user-flow-custom-attributes.md?pivots=b2c-custom-policy) |
| IncluirClaimResolvingInClaimsHandling  | Não | Para pedidos de entradas e saídas, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` , ou `false` (predefinição). Se pretender utilizar uma reclamação no perfil técnico, desa um pouco `true` para . |

### <a name="ui-elements"></a>Elementos da IU
 
As seguintes definições podem ser utilizadas para configurar a mensagem de erro exibida após a falha. Os metadados devem ser configurados no perfil técnico [autoafirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization.md).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | Não | Se for levantado um erro (ver RaiseErrorIfClaimsPrincipalAlreadyExists atribua descrição), especifique a mensagem para mostrar ao utilizador se o objeto do utilizador já existir. |
| UserMessageIfClaimsPrincipalDoesNotExist | Não | Se for levantado um erro (ver descrição do atributo RaiseErrorIfClaimsPrincipalDoesNotExist), especifique a mensagem para mostrar ao utilizador se o objeto do utilizador não existir. |


## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte artigo, por exemplo, utilizando o perfil técnico Azure AD:

- [Adicione reclamações e personalize a entrada do utilizador usando políticas personalizadas no Azure Ative Directory B2C](configure-user-input.md)














