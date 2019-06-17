---
title: Definir um perfil de técnico de transformação de afirmações numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil de técnico de transformação de afirmações numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fd2f77200741c54d1ed10f0a0eec1da76462b0a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511621"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil de técnicas de transformação de afirmações em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil de técnicas de transformação de afirmações permite que chame transformações para manipular os valores de afirmações, validar afirmações ou definir os valores predefinidos para um conjunto de afirmações de saída de afirmações de saída.

## <a name="protocol"></a>Protocol

O **Name** atributo da **protocolo** elemento tem de ser definido como `Proprietary`. O **manipulador** atributo tem de conter o nome completamente qualificado da assemblagem de manipulador de protocolo que é utilizado pelo Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

O exemplo seguinte mostra um perfil de técnicas de transformação de afirmações:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Afirmações de saída

O **OutputClaims** elemento é obrigatório. Deve fornecer pelo menos uma afirmação devolvida pelo perfil técnico de saída. O exemplo seguinte mostra como definir os valores predefinidos nas afirmações de saída:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Transformações de afirmações de saída

O **OutputClaimsTransformations** elemento pode conter uma coleção de **OutputClaimsTransformation** elementos que são utilizados para modificar afirmações ou gerar novas etiquetas. As seguintes chamadas de perfil técnico da **RemoveAlternativeSecurityIdByIdentityProvider** transformação de afirmações. Este afirmações remove transformação um social identificar da coleção de **AlternativeSecurityIds**. São as afirmações de saída deste perfil técnico **identityProvider2**, que é definida como `facebook.com`, e **AlternativeSecurityIds**, que contém a lista de identidades sociais associado a este utilizador após a remoção de identidade facebook.com.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

O perfil técnico de transformação de afirmações permite-lhe executar uma transformação de declarações do passo de orquestração de qualquer percurso de utilizador. No exemplo a seguir, o passo de orquestração chama um dos perfis técnicos de desassociar, tal como **desassociar-Facebook-OAUTH**. Este perfil técnico chama as declarações perfil técnico de transformação **RemoveAlternativeSecurityIdByIdentityProvider**, que gera uma nova **AlternativeSecurityIds2** afirmação que contém a lista de redes sociais as identidades dos utilizadores, ao remover a identidade do Facebook das coleções.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="use-a-validation-technical-profile"></a>Utilizar um perfil de técnicas de validação

Um perfil de técnicas de transformação de afirmações pode ser utilizado para validar as informações. No exemplo a seguir, o [auto-avaliar relativamente perfil técnico](self-asserted-technical-profile.md) com o nome **LocalAccountSignUpWithLogonEmail** pede ao utilizador para introduzir o e-mail de duas vezes, em seguida, chama o [técnica de validação perfil](validation-technical-profile.md) com o nome **validar E-Mail** para validar os e-mails. O **E-Mail validar** perfil técnico chama a transformação de declarações **AssertEmailAreEqual** para comparar duas afirmações **e-mail** e **emailRepeat** e lançar uma exceção se não forem iguais, de acordo com a comparação especificada.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

As chamadas de perfil técnico de transformação de afirmações a **AssertEmailAreEqual** afirmações de transformação, que declara que as mensagens de correio eletrónico fornecidas pelo usuário são os mesmas.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

A autodeclarativas perfil técnico pode chamar o perfil técnico de validação e mostrar a mensagem de erro, conforme especificado na **UserMessageIfClaimsTransformationStringsAreNotEqual** metadados.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
