---
title: Definir um perfil técnico de Azure Active Directory em uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Defina um perfil técnico Azure Active Directory em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4383980953147560b9e51e4ccab3032dd8173dd4
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064629"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico de Azure Active Directory em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para o gerenciamento de usuários do Azure Active Directory. Este artigo descreve as especificidades de um perfil técnico para interagir com um provedor de declarações que dá suporte a esse protocolo padronizado.

## <a name="protocol"></a>Protocol

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`do manipulador de protocolo.

Todos os perfis técnicos do Azure AD incluem o perfil técnico do **AAD-comum** . Os seguintes perfis técnicos não especificam o protocolo porque o protocolo está configurado no perfil técnico **comum do AAD** :

- **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingAlternativeSecurityId-NOERROR** -pesquisa uma conta social no diretório.
- **AAD-UserWriteUsingAlternativeSecurityId** -crie uma nova conta social.
- **AAD-UserReadUsingEmailAddress** -pesquisa uma conta local no diretório.
- **AAD-UserWriteUsingLogonEmail** -crie uma nova conta local.
- **AAD-UserWritePasswordUsingObjectId** -atualiza uma senha de uma conta local.
- **AAD-UserWriteProfileUsingObjectId** -atualiza um perfil de usuário de uma conta local ou social.
- **AAD-UserReadUsingObjectId** -Leia um perfil de usuário de uma conta local ou social.
- **AAD-UserWritePhoneNumberUsingObjectId** -grava o número de telefone MFA de uma conta local ou social

O exemplo a seguir mostra o perfil técnico do **AAD-comum** :

```XML
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

## <a name="input-claims"></a>Declarações de entrada

Os seguintes perfis técnicos incluem **InputClaims** para contas sociais e locais:

- Os perfis técnicos da conta social **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserWriteUsingAlternativeSecurityId** incluem a Declaração **AlternativeSecurityId** . Essa declaração contém o identificador de usuário da conta social.
- A conta local perfis técnicos **AAD-UserReadUsingEmailAddress** e **AAD-UserWriteUsingLogonEmail** inclui a declaração de **email** . Essa declaração contém o nome de entrada da conta local.
- Os perfis técnicos unificados (locais e sociais) **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId**e **AAD-UserWritePhoneNumberUsingObjectId** incluem o Declaração **ObjectID** . O identificador exclusivo de uma conta.

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo perfil técnico do Azure AD. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido em Azure Active Directory. Você também pode incluir declarações que não são retornadas pelo Azure Active Directory, desde que você defina o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

Por exemplo, o perfil técnico do **AAD-UserWriteUsingLogonEmail** cria uma conta local e retorna as seguintes declarações:

- **ObjectID**, que é o identificador da nova conta
- **newuser**, que indica se o usuário é novo
- **authenticationname**, que define a autenticação como`localAccountAuthentication`
- **userPrincipalName**, que é o nome principal do usuário da nova conta
- **signInNames. EmailAddress**, que é o nome de entrada da conta, semelhante à declaração de entrada de **email**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

O elemento **PersistedClaims** contém todos os valores que devem ser persistidos pelo Azure AD com informações de mapeamento possíveis entre um tipo de declaração já definido na seção ClaimsSchema na política e o nome do atributo do Azure AD.

O perfil técnico do **AAD-UserWriteUsingLogonEmail** , que cria uma nova conta local, persiste com as seguintes declarações:

```XML
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

O nome da declaração é o nome do atributo do Azure AD, a menos que o atributo **PartnerClaimType** seja especificado, que contém o nome do atributo do Azure AD.

## <a name="requirements-of-an-operation"></a>Requisitos de uma operação

- Deve haver exatamente um elemento **InputClaim** no recipiente de declarações para todos os perfis técnicos do Azure AD.
- Se a operação for `Write` ou `DeleteClaims`, ela também deverá aparecer em um elemento **PersistedClaims** .
- O valor da Declaração **userPrincipalName** deve estar no formato de `user@tenant.onmicrosoft.com`.
- A Declaração **DisplayName** é necessária e não pode ser uma cadeia de caracteres vazia.

## <a name="azure-ad-technical-provider-operations"></a>Operações do provedor técnico do Azure AD

### <a name="read"></a>Leitura

A operação de **leitura** lê dados sobre uma única conta de usuário. Para ler os dados do usuário, você precisa fornecer uma chave como uma declaração de entrada, como **ObjectID**, **userPrincipalName**, **signInNames** (qualquer tipo, nome de usuário e conta baseada em email) ou **alternativeSecurityId**.

O seguinte perfil técnico lê dados sobre uma conta de usuário usando o objectId do usuário:

```XML
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

A operação de **gravação** cria ou atualiza uma única conta de usuário. Para gravar uma conta de usuário, você precisa fornecer uma chave como uma declaração de entrada, como **ObjectID**, **userPrincipalName**, **signInNames. EmailAddress**ou **alternativeSecurityId**.

O seguinte perfil técnico cria uma nova conta social:

```XML
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

A operação **DeleteClaims** limpa as informações de uma lista de declarações fornecida. Para excluir informações de declarações, você precisa fornecer uma chave como uma declaração de entrada, como **ObjectID**, **userPrincipalName**, **signInNames. EmailAddress** ou **alternativeSecurityId**.

O seguinte perfil técnico exclui declarações:

```XML
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

A operação **DeleteClaimsPrincipal** exclui uma única conta de usuário do diretório. Para excluir uma conta de usuário, você precisa fornecer uma chave como uma declaração de entrada, como **ObjectID**, **userPrincipalName**, **signInNames. EmailAddress** ou **alternativeSecurityId**.

O seguinte perfil técnico exclui uma conta de usuário do diretório usando o nome principal do usuário:

```XML
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

O seguinte perfil técnico exclui uma conta de usuário social usando o **alternativeSecurityId**:

```XML
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

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | A operação a ser executada. Valores possíveis: `Read`, `Write`, `DeleteClaims`ou. `DeleteClaimsPrincipal` |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Não | Gerar um erro se o objeto de usuário não existir no diretório. Valores possíveis: `true` ou `false`. |
| UserMessageIfClaimsPrincipalDoesNotExist | Não | Se um erro for gerado (consulte a descrição do atributo RaiseErrorIfClaimsPrincipalDoesNotExist), especifique a mensagem a ser mostrada ao usuário se o objeto do usuário não existir. O valor pode ser [localizado](localization.md).|
| RaiseErrorIfClaimsPrincipalAlreadyExists | Não | Gerar um erro se o objeto de usuário já existir. Valores possíveis: `true` ou `false`.|
| UserMessageIfClaimsPrincipalAlreadyExists | Não | Se um erro for gerado (consulte a descrição do atributo RaiseErrorIfClaimsPrincipalAlreadyExists), especifique a mensagem a ser mostrada ao usuário se o objeto do usuário já existir. O valor pode ser [localizado](localization.md).|
| ApplicationObjectId | Não | O identificador de objeto de aplicativo para atributos de extensão. Valor ObjectId de um aplicativo. Para obter mais informações, consulte [usar atributos personalizados em uma política de edição de perfil personalizado](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). |
| ClientId | Não | O identificador do cliente para acessar o locatário como terceiros. Para obter mais informações, consulte [usar atributos personalizados em uma política de edição de perfil personalizado](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) |














