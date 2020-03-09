---
title: Defina um perfil técnico da AD Azure numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico azure Ative Directory numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 82daf447270fc0413284e3e7a908a8b5237a4f9c
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78932985"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de Diretório Ativo Azure numa política personalizada azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) presta suporte à gestão de utilizadores do Diretório Ativo Azure. Este artigo descreve as especificidades de um perfil técnico para interagir com um fornecedor de sinistros que suporta este protocolo padronizado.

## <a name="protocol"></a>Protocolo

O **nome** atributo do elemento **protocolo** tem de ser definido para `Proprietary`. O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo`Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Os perfis técnicos da AD AD de [política personalizada](custom-policy-get-started.md#custom-policy-starter-pack) incluem o perfil técnico **AAD-Common.** Os perfis técnicos da AD Azure não especificam o protocolo porque o protocolo está configurado no perfil técnico **AAD-Common:**
 
- **AAD-UserReadUsingAlternativeSecurityId** and **AAD-UserReadUsingAlternativeSecurityId-NoError** - Procure uma conta social no diretório.
- **AAD-UserWriteUsingAlternativeSecurityId** - Criar uma nova conta social.
- **AAD-UserReadUsingEmailAddress** - Procure uma conta local no diretório.
- **AAD-UserWriteUsingLogonEmail** - Criar uma nova conta local.
- **AAD-UserWritePasswordUsingObjectId** - Atualize uma palavra-passe de uma conta local.
- **AAD-UserWriteProfileUsingObjectId** - Atualize um perfil de utilizador de uma conta local ou social.
- **AAD-UserReadUsingObjectId** - Leia um perfil de utilizador de uma conta local ou social.
- **AAD-userWriteNumberUsingObjectId** - Escreva o número de telefone do MFA de uma conta local ou social

O exemplo que se segue mostra o perfil técnico **AAD-Common:**

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

## <a name="input-claims"></a>Reclamações de entrada

O elemento InputClaims contém uma reclamação, que é usada para procurar uma conta no diretório, ou criar uma nova. Deve haver exatamente um elemento InputClaim na recolha de reclamações de entrada para todos os perfis técnicos da AD Azure. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido no Diretório Ativo Azure.

Para ler, atualizar ou eliminar uma conta de utilizador existente, a alegação de entrada é uma chave que identifica exclusivamente a conta no diretório da AD Azure. Por exemplo, **objectId**, **userPrincipalName**, **signNames.emailAddress,** **signName.userName**, ou **alternativeSecurityId**. 

Para criar uma nova conta de utilizador, a alegação de entrada é uma chave que identifica exclusivamente uma conta local ou federada. Por exemplo, conta local: **signNames.emailAddress**, ou **signInNames.userName**. Para uma conta federada: a **alternativaSecurityId**.

O elemento InputClaimsTransformations pode conter uma coleção de elementos de transformação de créditos de entrada que são usados para modificar a reclamação de entrada ou gerar um novo.

## <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo perfil técnico da AD Azure. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido no Diretório Ativo Azure. Também pode incluir reclamações que não são devolvidas pelo Diretório Ativo Azure, desde que detetete o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

Por exemplo, o perfil técnico **AAD-UserWriteUseLogonEmail** cria uma conta local e devolve as seguintes reclamações:

- **objectId**, que é identificador da nova conta
- **newUser**, que indica se o utilizador é novo
- **autenticaçãoSource**, que define a autenticação para `localAccountAuthentication`
- **userPrincipalName**, que é o nome principal do utilizador da nova conta
- **signNames.emailAddress**, que é o nome de entrada na conta, semelhante à reclamação de entrada de **e-mail**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>Reclamações Persistidas

O elemento **PersistedClaims** contém todos os valores que devem ser persponiados pela Azure AD com possíveis informações de mapeamento entre um tipo de reclamação já definido na secção ClaimsSchema na política e no nome de atributo azure AD.

O perfil técnico **AAD-UserWriteUseLogonEmail,** que cria uma nova conta local, persiste na sequência de reclamações:

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

O nome da reclamação é o nome do atributo AD Azure, a menos que seja especificado o atributo **PartnerClaimType,** que contém o nome do atributo Azure AD.

## <a name="requirements-of-an-operation"></a>Requisitos de uma operação

- Deve haver exatamente um elemento **De InputClaim** no saco de reclamações para todos os perfis técnicos da AD Azure.
- Se a operação for `Write` ou `DeleteClaims`, então também deve aparecer num elemento **PersistedClaims.**
- O valor da reclamação do **utilizadorPrincipalName** deve estar no formato de `user@tenant.onmicrosoft.com`.
- A alegação de nome de **exibição** é necessária e não pode ser uma corda vazia.

## <a name="azure-ad-technical-provider-operations"></a>Operações de prestador técnico da Azure AD

### <a name="read"></a>Leitura

A operação **Read** lê dados sobre uma única conta de utilizador. Para ler os dados dos utilizadores, é necessário fornecer uma chave como uma alegação de entrada, tais como **objectId,** **userPrincipalName,** **signInNames** (qualquer tipo, nome do utilizador e conta baseada em e-mail) ou **segurança ida em alternativa**.

O perfil técnico seguinte lê dados sobre uma conta de utilizador utilizando o objectid do utilizador:

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

A operação **Write** cria ou atualiza uma única conta de utilizador. Para escrever uma conta de utilizador, é necessário fornecer uma chave como uma alegação de entrada, tais como **objectId,** **userPrincipalName**, **signNames.emailAddress**, ou **alternativaSecurityId**.

O perfil técnico seguinte cria uma nova conta social:

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

A operação **DeleteClaims** iliba as informações de uma lista de reclamações fornecidas. Para eliminar informações de reclamações, é necessário fornecer uma chave como uma alegação de entrada, tais como **objectId,** **userPrincipalName**, **signNames.emailAddress** ou **alternativeSecurityId**.

O perfil técnico seguinte elimina as reclamações:

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

A operação **DeleteClaimsPrincipal** elimina uma única conta de utilizador do diretório. Para eliminar uma conta de utilizador, é necessário fornecer uma chave como uma alegação de entrada, tais como **objectId**, **userPrincipalName**, **signNames.emailAddress** ou **alternativeSecurityId**.

O perfil técnico seguinte elimina uma conta de utilizador do diretório utilizando o nome principal do utilizador:

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

O perfil técnico seguinte elimina uma conta de utilizador social utilizando **alternativaSSecurityId:**

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
| Operação | Sim | A operação a ser realizada. Valores possíveis: `Read`, `Write`, `DeleteClaims`ou `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Não | Levante um erro se o objeto do utilizador não existir no diretório. Valores possíveis: `true` ou `false`. |
| UserMessageIfClaimsPrincipalDoesNotExist | Não | Se for levantado um erro (consulte a descrição do atributo RaiseErrorIfClaimsPrincipalDoesNotExist), especifique a mensagem para mostrar ao utilizador se o objeto do utilizador não existir. O valor pode ser [localizado.](localization.md)|
| RaiseErrorIfClaimsPrincipalAlreadyExists | Não | Levante um erro se o objeto do utilizador já existir. Valores possíveis: `true` ou `false`.|
| UserMessageIfClaimsPrincipalAlreadyExists | Não | Se for levantado um erro (ver RaiseErrorIfClaimsPrincipalAlreadyExists aatribuição), especifique a mensagem para mostrar ao utilizador se o objeto do utilizador já existir. O valor pode ser [localizado.](localization.md)|
| ApplicationObjectId | Não | O identificador de objeto de aplicação para atributos de extensão. Valor: ObjectId de uma aplicação. Para mais informações, consulte [Use atributos personalizados numa política](custom-policy-custom-attributes.md)de edição de perfil personalizado. |
| ClientId | Não | O cliente identifica o inquilino como terceiro. Para mais informações, consulte [Use atributos personalizados numa política de edição de perfil personalizado](custom-policy-custom-attributes.md) |
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`, ou `false` (predefinido). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |














