---
title: Definir um perfil técnico do Azure Active Directory numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico do Azure Active Directory numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 433307791201e3799b3bc9e54aec765d9fbeb4af
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718700"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do Azure Active Directory numa política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory (Azure AD) B2C fornece suporte para a gestão de utilizadores do Azure Active Directory. Este artigo descreve as especificações de um perfil técnico da interagir com um fornecedor de afirmações que suporte esse protocolo padronizado.

## <a name="protocol"></a>Protocolo

O **Name** atributo da **protocolo** elemento tem de ser definido como `Proprietary`. O **manipulador** atributo tem de conter o nome completamente qualificado do assembly do manipulador de protocolo `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Todos os perfis de técnicos do Azure AD incluem o **comuns de AAD** perfil técnico. Os seguintes perfis técnicos não especificar o protocolo, porque o protocolo está configurado no **comuns de AAD** perfil técnico:

- **AAD-UserReadUsingAlternativeSecurityId** e **UserReadUsingAlternativeSecurityId-AAD-NoError** - procure uma conta de redes sociais no diretório de cópia de segurança.
- **AAD-UserWriteUsingAlternativeSecurityId** -criar uma nova conta de redes sociais.
- **AAD-UserReadUsingEmailAddress** - procure uma conta local no diretório de cópia de segurança. 
- **AAD-UserWriteUsingLogonEmail** -criar uma nova conta local.
- **AAD-UserWritePasswordUsingObjectId** -atualizar uma palavra-passe de uma conta local.
- **AAD-UserWriteProfileUsingObjectId** -atualizar um perfil de utilizador de uma conta local ou redes sociais.
- **AAD-UserReadUsingObjectId** -ler um perfil de utilizador de uma conta local ou redes sociais.
- **AAD-UserWritePhoneNumberUsingObjectId** -escreva o número de telefone MFA de uma conta local ou redes sociais

A exemplo a seguir mostra a **comuns de AAD** perfil técnico:

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

## <a name="input-claims"></a>Afirmações de entrada

Incluem os seguintes perfis técnicos **InputClaims** para contas de redes sociais e locais:

- Os perfis de técnico de conta de redes sociais **AAD UserReadUsingAlternativeSecurityId** e **AAD UserWriteUsingAlternativeSecurityId** inclui o **AlternativeSecurityId** de afirmação. Esta afirmação contém o identificador de utilizador de conta de redes sociais.
- Os perfis de técnico de conta local **AAD UserReadUsingEmailAddress** e **AAD UserWriteUsingLogonEmail** inclui o **e-mail** de afirmação. Esta afirmação contém o nome de início de sessão da conta local.
- Os perfis técnicos unificados (locais e redes sociais) **AAD UserReadUsingObjectId**, **AAD UserWritePasswordUsingObjectId**, **AAD UserWriteProfileUsingObjectId**, e **AAD UserWritePhoneNumberUsingObjectId** inclui a **objectId** de afirmação. O identificador exclusivo de uma conta.

O **InputClaimsTransformations** elemento pode conter uma coleção de **InputClaimsTransformation** elementos que são utilizados para modificar as afirmações de entrada ou gerar novas etiquetas.

## <a name="output-claims"></a>Afirmações de saída

O **OutputClaims** elemento contém uma lista das declarações devolvido pelo perfil técnico do Azure AD. Terá de mapear o nome da afirmação definido na sua política para o nome definido no Azure Active Directory. Também pode incluir declarações que não são devolvidas pelo Azure Active Directory, desde que definir o `DefaultValue` atributo.

O **OutputClaimsTransformations** elemento pode conter uma coleção de **OutputClaimsTransformation** elementos que são utilizados para modificar as afirmações de saída ou gerar novas etiquetas.

Por exemplo, o **AAD UserWriteUsingLogonEmail** perfil técnico cria uma conta local e retorna as seguintes declarações:

- **objectId**, que é o identificador da nova conta
- **newUser**, que indica se o utilizador é novo
- **authenticationSource**, que define a autenticação para `localAccountAuthentication`
- **userPrincipalName**, que é o nome do principal de utilizador da nova conta
- **signInNames.emailAddress**, que é o início de sessão no nome da conta, semelhante a **e-mail** afirmações de entrada

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

O **PersistedClaims** elemento contém todos os valores que devem ser mantidos pelo Azure AD com informações de mapeamento possíveis entre um tipo de afirmação já definido na secção ClaimsSchema na política e o atributo do Azure AD nome. 

O **AAD UserWriteUsingLogonEmail** perfil técnico, que cria a nova conta local, persistir seguintes afirmações:

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

O nome da afirmação é o nome do atributo do Azure AD, a menos que o **PartnerClaimType** atributo é especificado, que contém o nome de atributo do Azure AD.

## <a name="requirements-of-an-operation"></a>Requisitos de uma operação

- Tem de existir exatamente um **InputClaim** elemento na matriz afirmações para todos os perfis de técnicos do Azure AD. 
- Se a operação for `Write` ou `DeleteClaims`, em seguida, também tem de aparecer num **PersistedClaims** elemento.
- O valor do **userPrincipalName** afirmação tem de estar no formato de `user@tenant.onmicrosoft.com`.
- O **displayName** afirmação é necessária e não pode ser uma cadeia vazia.

## <a name="azure-ad-technical-provider-operations"></a>Operações de fornecedor técnico do Azure AD

### <a name="read"></a>Leitura

O **leitura** operação lê os dados relativos uma conta de utilizador único. Para ler dados de utilizador, tem de fornecer uma chave como uma afirmação de entrada, como **objectId**, **userPrincipalName**, **signInNames** (qualquer tipo, nome de utilizador e conta de e-mail) ou **alternativeSecurityId**.  

O perfil técnico seguinte lê os dados relativos uma conta de utilizador com objectId do utilizador:

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

O **escrever** operação cria ou atualiza uma conta de utilizador único. Para escrever uma conta de utilizador, tem de fornecer uma chave como uma afirmação de entrada, como **objectId**, **userPrincipalName**, **signInNames.emailAddress**, ou  **alternativeSecurityId**.  

O perfil técnico seguinte cria uma nova conta de redes sociais:

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

O **DeleteClaims** operação limpa as informações a partir de uma lista especificada de declarações. Para excluir informações de afirmações, tem de fornecer uma chave como uma afirmação de entrada, como **objectId**, **userPrincipalName**, **signInNames.emailAddress** ou **alternativeSecurityId**.  

O perfil técnico seguinte elimina afirmações:

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

O **DeleteClaimsPrincipal** operação elimina uma única conta de utilizador do diretório. Para eliminar uma conta de utilizador, tem de fornecer uma chave como uma afirmação de entrada, como **objectId**, **userPrincipalName**, **signInNames.emailAddress** ou  **alternativeSecurityId**.  

O perfil técnico seguinte elimina uma conta de utilizador do diretório com o nome principal de utilizador:

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

O perfil técnico seguinte elimina uma conta de utilizador de redes sociais com **alternativeSecurityId**:

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

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | A operação a ser executada. Valores possíveis: `Read`, `Write`, `DeleteClaims`, ou `DeleteClaimsPrincipal`. | 
| RaiseErrorIfClaimsPrincipalDoesNotExist | Não | Emitir um erro se o objeto de utilizador não existir no diretório. Valores possíveis: `true` ou `false`. | 
| UserMessageIfClaimsPrincipalDoesNotExist | Não | Se um erro se estiver a ser gerado (consulte a descrição do atributo RaiseErrorIfClaimsPrincipalDoesNotExist), especifique a mensagem a apresentar ao utilizador se o objeto de utilizador não existe. O valor pode ser [localizada](localization.md).| 
| RaiseErrorIfClaimsPrincipalAlreadyExists | Não | Emitir um erro se o objeto de utilizador já existe. Valores possíveis: `true` ou `false`.| 
| UserMessageIfClaimsPrincipalAlreadyExists | Não | Se um erro se estiver a ser gerado (consulte a descrição do atributo RaiseErrorIfClaimsPrincipalAlreadyExists), especifique a mensagem a apresentar ao utilizador se o objeto de utilizador já existe. O valor pode ser [localizada](localization.md).| 
| ApplicationObjectId | Não | O identificador de objeto de aplicativo para atributos de extensão. Valor: ObjectId de um aplicativo. Para obter mais informações, consulte [atributos personalizados de utilização num perfil personalizado do Editar política](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). | 
| ClientId | Não | O identificador de cliente para aceder ao inquilino como uma aplicação de terceiros. Para obter mais informações, consulte [atributos personalizados de utilização num perfil personalizado do Editar política](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) | 














