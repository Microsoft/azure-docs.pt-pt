---
title: Definir um perfil técnico de declaração própria numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico de declaração própria numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3c728660f1a77c02f1e4b5fdeb467a7dbba4e36a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512665"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico de declaração própria em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas as interações no B2C do Azure Active Directory (Azure AD) em que o utilizador é esperado para fornecer entradas são autodeclarativas perfis técnicos. Por exemplo, uma página de inscrição, a página de início de sessão ou a palavra-passe página de reposição.

## <a name="protocol"></a>Protocol

O **Name** atributo da **protocolo** elemento tem de ser definido como `Proprietary`. O **manipulador** atributo tem de conter o nome completamente qualificado da assemblagem de manipulador de protocolo que é utilizado pelo Azure AD B2C, para autodeclarativas: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo seguinte mostra um perfil de técnico de declaração própria para e-mail Inscreva-se:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>Afirmações de entrada

Num perfil técnico de declaração própria, pode usar o **InputClaims** e **InputClaimsTransformations** elementos para pré-povoar o valor das declarações que aparecem na página de declaração própria (saída afirmações). Por exemplo, na política de perfil de edição, o percurso do utilizador lê primeiro o perfil de utilizador do serviço de diretório do Azure AD B2C, em seguida, o perfil técnico de declaração própria define as afirmações de entrada com os dados de utilizador armazenados no perfil do usuário. Essas declarações são recolhidas a partir de perfil do usuário e, em seguida, são apresentadas ao utilizador que, em seguida, pode editar os dados existentes.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Afirmações de saída

O **OutputClaims** elemento contém uma lista das declarações a serem apresentados para recolher dados do usuário. Para pré-povoar as afirmações de saída com alguns valores, utilize as afirmações de entrada que foram descritas anteriormente. O elemento também pode conter um valor predefinido. A ordem das declarações na **OutputClaims** controla a ordem em que o Azure AD B2C processa as afirmações no ecrã. O **DefaultValue** atributo entra em vigor apenas se a afirmação nunca tiver sido definida antes. No entanto, se foi definida antes de um passo de orquestração anterior, mesmo que o usuário deixar o valor vazio, o valor predefinido não terá efeito. Para forçar a utilização de um valor predefinido, defina o **AlwaysUseDefaultValue** atributo para `true`. Para forçar o utilizador para fornecer um valor de uma afirmação de saída específico, defina o **necessários** atributo da **OutputClaims** elemento `true`.

O **ClaimType** elemento no **OutputClaims** coleção tem de definir o **UserInputType** tipo suportado pelo Azure AD B2C, tais como deentradadoelementoparaqualquerutilizador`TextBox`ou `DropdownSingleSelect`. Ou o **OutputClaim** elemento tem de definir um **DefaultValue**.  

O **OutputClaimsTransformations** elemento pode conter uma coleção de **OutputClaimsTransformation** elementos que são utilizados para modificar as afirmações de saída ou gerar novas etiquetas.

A seguinte declaração de saída está sempre definida como `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Caso de utilização

Existem quatro cenários de afirmações de saída:

- **Recolha a saída de afirmações do utilizador** - quando precisar coletar informações do usuário, como a data de nascimento, deve adicionar a afirmação para o **OutputClaims** coleção. Tem de especificar as afirmações que são apresentadas ao utilizador a **UserInputType**, tal como `TextBox` ou `DropdownSingleSelect`. Se o perfil técnico de declaração própria contém um perfil de técnicas de validação que produz a mesma afirmação, o Azure AD B2C não apresenta a afirmação para o utilizador. Se existir qualquer afirmação de saída para apresentar ao usuário, o Azure AD B2C ignora o perfil técnico.
- **Definir um valor predefinido de uma afirmação de saída** – sem a recolha de dados do usuário ou retornar os dados do perfil técnico de validação. O `LocalAccountSignUpWithLogonEmail` autodeclarativas conjuntos de perfil técnico a **executado SelfAsserted-introdução** alegam `true`.
- **Um perfil de técnicas de validação devolve as afirmações de saída** -seu perfil técnico pode chamar um perfil de técnicas de validação que retorna algumas afirmações. Pode querer exibidas as afirmações e retorná-los para as próximas etapas de orquestração no percurso do utilizador. Por exemplo, quando iniciar sessão com uma conta local, o perfil técnico de declaração própria chamada `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação com o nome `login-NonInteractive`. Este perfil técnico valida as credenciais de utilizador e também devolve o perfil de utilizador. Por exemplo, 'userPrincipalName', 'displayName', "givenName" e "Apelido".
- **As declarações por meio de transformação de declarações de saída de saída**

No exemplo a seguir, o `LocalAccountSignUpWithLogonEmail` autodeclarativas perfil técnico demonstra a utilização de afirmações de saída e conjuntos **executado SelfAsserted-introdução** para `true`. O `objectId`, `authenticationSource`, `newUser` afirmações são o resultado do `AAD-UserWriteUsingLogonEmail` técnica de validação de perfil e não são apresentados ao utilizador.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Manter as afirmações

Se o **PersistedClaims** elemento estiver ausente, o perfil técnico de declaração própria não mantém os dados para o Azure AD B2C. Em vez disso, é efetuada uma chamada para um perfil de técnicas de validação que é responsável por manter os dados. Por exemplo, a política de inscrição utiliza o `LocalAccountSignUpWithLogonEmail` autodeclarativas perfil técnico para recolher o novo perfil de utilizador. O `LocalAccountSignUpWithLogonEmail` perfil técnico chama o perfil técnico de validação para criar a conta no Azure AD B2C.

## <a name="validation-technical-profiles"></a>Perfis de técnicos de validação

Um perfil de técnicas de validação é utilizado para validar a algumas ou todas as afirmações de saída do perfil técnico referência. Afirmações de entrada do perfil técnico validação têm de aparecer nas afirmações de saída do perfil técnico de declaração própria. O perfil técnico de validação valida a entrada do usuário e pode devolver um erro ao utilizador. 

O perfil técnico de validação pode ser qualquer perfil técnico na política, como [do Azure Active Directory](active-directory-technical-profile.md) ou uma [REST API](restful-technical-profile.md) perfis técnicos. No exemplo anterior, o `LocalAccountSignUpWithLogonEmail` perfil técnico valida que o signinName não existe no diretório. Caso contrário, o perfil técnico de validação cria uma conta local e retorna o objectId, authenticationSource, newUser. O `SelfAsserted-LocalAccountSignin-Email` perfil técnico chamadas a `login-NonInteractive` perfil técnico de validação para validar as credenciais de utilizador.

Também pode chamar um perfil técnico da REST API com a sua lógica de negócios, substituir afirmações de entrada ou enriquecer os dados de utilizador com a integração mais com a aplicação de linha de negócio empresarial. Para obter mais informações, consulte [perfil técnico de validação](validation-technical-profile.md)

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| setting.showContinueButton | Não | Mostra o botão continuar. Valores possíveis: `true` (predefinição), ou `false` |
| setting.showCancelButton | Não | Mostra o botão Cancelar. Valores possíveis: `true` (predefinição), ou `false` |
| setting.operatingMode | Não | Para uma página de início de sessão, esta propriedade controla o comportamento do campo de nome de utilizador, como validação de entrada e de mensagens de erro. Valores esperados: `Username` ou `Email`. |
| ContentDefinitionReferenceId | Sim | O identificador do [definição de conteúdo](contentdefinitions.md) associados a este perfil técnico. |
| EnforceEmailVerification | Não | Para inscrição ou edição de perfil, que impõe a verificação de e-mail. Valores possíveis: `true` (predefinição), ou `false`. | 
| setting.showSignupLink | Não | Mostra o botão de inscrição. Valores possíveis: `true` (predefinição), ou `false` |
| setting.retryLimit | Não | Controla o número de vezes que um utilizador pode tentar para fornecer os dados que são comparados com um perfil de técnicas de validação. Por exemplo, um utilizador tentar inscrever-se com uma conta que já existe e que mantém a tentar até que o limite atingido.
| SignUpTarget | Não | O identificador de exchange de destino de inscrição. Quando o usuário clica no botão de inscrição, o Azure AD B2C executa o identificador do exchange especificado. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O **CryptographicKeys** elemento não é utilizado.













