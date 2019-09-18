---
title: Definir um perfil técnico autodeclarado em uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Defina um perfil técnico autodeclarado em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4fec742766cebeb5b1d82655e09af77a888c375c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063680"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico autodeclarado em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas as interações em Azure Active Directory B2C (Azure AD B2C) em que o usuário deve fornecer entrada são perfis técnicos autodeclarados. Por exemplo, uma página de inscrição, uma página de entrada ou uma página de redefinição de senha.

## <a name="protocol"></a>Protocol

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure ad B2C, para autoasserted:`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico autodeclarado para inscrição de email:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Declarações de entrada

Em um perfil técnico autodeclarado, você pode usar os elementos **InputClaims** e **InputClaimsTransformations** para pré-popular o valor das declarações que aparecem na página autodeclarada (declarações de saída). Por exemplo, na política Editar perfil, o percurso do usuário lê primeiro o perfil do usuário do serviço de diretório Azure AD B2C, então o perfil técnico autodeclarado define as declarações de entrada com os dados do usuário armazenados no perfil do usuário. Essas declarações são coletadas do perfil do usuário e, em seguida, apresentadas ao usuário que pode editar os dados existentes.

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


## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações a serem apresentadas para coletar dados do usuário. Para preencher previamente as declarações de saída com alguns valores, use as declarações de entrada que foram descritas anteriormente. O elemento também pode conter um valor padrão. A ordem das declarações no **OutputClaims** controla a ordem em que Azure ad B2C renderiza as declarações na tela. O atributo **DefaultValue** entrará em vigor somente se a declaração nunca tiver sido definida antes. Mas, se ele tiver sido definido antes em uma etapa de orquestração anterior, mesmo que o usuário deixe o valor vazio, o valor padrão não terá efeito. Para forçar o uso de um valor padrão, defina o atributo **AlwaysUseDefaultValue** como `true`. Para forçar o usuário a fornecer um valor para uma declaração de saída específica, defina o atributo **Required** do elemento **OutputClaims** como `true`.

O elemento **ClaimType** na coleção **OutputClaims** precisa definir o elemento **userinputtype** como qualquer tipo de entrada de usuário com suporte `TextBox` pelo Azure ad B2C, como ou. `DropdownSingleSelect` Ou o elemento **OutputClaim** deve definir um **DefaultValue**.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

A seguinte declaração de saída é sempre definida `live.com`como:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Caso de utilização

Há quatro cenários para as declarações de saída:

- **Coletando as declarações de saída do usuário** -quando precisar coletar informações do usuário, como data de nascimento, você deve adicionar a declaração à coleção **OutputClaims** . As declarações que são apresentadas ao usuário devem especificar o **userinputtype**, como `TextBox` ou `DropdownSingleSelect`. Se o perfil técnico autodeclarado contiver um perfil técnico de validação que produza a mesma declaração, Azure AD B2C não apresentará a declaração ao usuário. Se não houver nenhuma declaração de saída para apresentar ao usuário, Azure AD B2C ignorará o perfil técnico.
- **Definindo um valor padrão em uma declaração de saída** -sem coletar dados do usuário ou retornar os dados do perfil técnico de validação. O `LocalAccountSignUpWithLogonEmail` perfil técnico autodeclarado define a Declaração **Executed-SelfAsserted-Input** para `true`.
- **Um perfil técnico de validação retorna as declarações de saída** – seu perfil técnico pode chamar um perfil técnico de validação que retorna algumas declarações. Talvez você queira emergir as declarações e retorná-las às próximas etapas de orquestração no percurso do usuário. Por exemplo, ao entrar com uma conta local, o perfil técnico autodeclarado chamado `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação chamado. `login-NonInteractive` Este perfil técnico valida as credenciais do usuário e também retorna o perfil do usuário. Como ' userPrincipalName ', ' displayName ', ' excerto ' e ' sobrenome '.
- **Gerar as declarações por meio da transformação de declarações de saída**

No exemplo a seguir, o `LocalAccountSignUpWithLogonEmail` perfil técnico autodeclarado demonstra o uso de declarações de saída e conjuntos **Executed-SelfAsserted-Input** para `true`. As `objectId`declarações `authenticationSource`, ,`newUser` são saídas do perfil `AAD-UserWriteUsingLogonEmail` técnico de validação e não são mostradas ao usuário.

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

## <a name="persist-claims"></a>Persistir declarações

Se o elemento **PersistedClaims** estiver ausente, o perfil técnico autodeclarado não persistirá os dados para Azure ad B2C. Em vez disso, é feita uma chamada para um perfil técnico de validação responsável por manter os dados. Por exemplo, a política de inscrição usa o `LocalAccountSignUpWithLogonEmail` perfil técnico autodeclarado para coletar o novo perfil de usuário. O `LocalAccountSignUpWithLogonEmail` perfil técnico chama o perfil técnico de validação para criar a conta no Azure ad B2C.

## <a name="validation-technical-profiles"></a>Perfis técnicos de validação

Um perfil técnico de validação é usado para validar algumas ou todas as declarações de saída do perfil técnico de referência. As declarações de entrada do perfil técnico de validação devem aparecer nas declarações de saída do perfil técnico autodeclarado. O perfil técnico de validação valida a entrada do usuário e pode retornar um erro para o usuário.

O perfil técnico de validação pode ser qualquer perfil técnico na política, como [Azure Active Directory](active-directory-technical-profile.md) ou perfis técnicos da [API REST](restful-technical-profile.md) . No exemplo anterior, o `LocalAccountSignUpWithLogonEmail` perfil técnico valida que o signinName não existe no diretório. Caso contrário, o perfil técnico de validação cria uma conta local e retorna o objectId, authenticationname, newUser. O `SelfAsserted-LocalAccountSignin-Email` perfil técnico chama o `login-NonInteractive` perfil técnico de validação para validar as credenciais do usuário.

Você também pode chamar um perfil técnico da API REST com sua lógica de negócios, substituir as declarações de entrada ou enriquecer os dados do usuário com a integração adicional com o aplicativo de linha de negócios corporativo. Para obter mais informações, consulte [validação do perfil técnico](validation-technical-profile.md)

## <a name="metadata"></a>Metadados

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| setting.showContinueButton | Não | Exibe o botão continuar. Valores possíveis: `true` (padrão) ou`false` |
| setting.showCancelButton | Não | Exibe o botão Cancelar. Valores possíveis: `true` (padrão) ou`false` |
| setting.operatingMode | Não | Para uma página de entrada, essa propriedade controla o comportamento do campo username, como a validação de entrada e as mensagens de erro. Valores esperados `Username` : `Email`ou. |
| ContentDefinitionReferenceId | Sim | O identificador da [definição de conteúdo](contentdefinitions.md) associada a este perfil técnico. |
| EnforceEmailVerification | Não | Para a inscrição ou a edição de perfil, o impõe a verificação de email. Valores possíveis: `true` (padrão) ou. `false` |
| setting.showSignupLink | Não | Exibe o botão de inscrição. Valores possíveis: `true` (padrão) ou`false` |
| setting.retryLimit | Não | Controla o número de vezes que um usuário pode tentar fornecer os dados que são verificados em relação a um perfil técnico de validação. Por exemplo, um usuário tenta se inscrever com uma conta que já existe e continua tentando até que o limite seja atingido.
| SignUpTarget | Não | O identificador de troca de destino de inscrição. Quando o usuário clica no botão de inscrição, Azure AD B2C executa o identificador de troca especificado. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** não é usado.













