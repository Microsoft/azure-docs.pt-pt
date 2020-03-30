---
title: Definir um perfil técnico de transformação de Reclamações
titleSuffix: Azure AD B2C
description: Defina um perfil técnico de transformação de Reclamações numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84c1cf798e88e4067da8a495c1591143d2ee1bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189791"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de transformação de sinistros numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico de transformação de sinistros permite-lhe chamar transformações de sinistros de saída para manipular valores de sinistros, validar reclamações ou definir valores padrão para um conjunto de reclamações de saída.

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `Proprietary`tem de ser definido para . O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`manipulador esprotocolo utilizado pelo Azure AD B2C: .

O exemplo seguinte mostra um perfil técnico de transformação de sinistros:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** é obrigatório. Deve fornecer pelo menos uma reclamação de saída devolvida pelo perfil técnico. O exemplo que se segue mostra como definir valores predefinidos nas alegações de saída:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Transformações de sinistros de produção

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar reclamações ou gerar novos. O perfil técnico seguinte chama a **remoção DeFormaçãoIdByIdentityProvider** alega a transformação. Esta transformação de sinistros remove uma identificação social da coleção de **AlternativasSegurançaIds**. As alegações de saída deste perfil técnico são `facebook.com` **identidadeProvider2**, que está definida para , e **AlternativaSecurityIds**- que contém a lista de identidades sociais associadas a este utilizador após a remoção facebook.com identidade.

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

O perfil técnico de transformação de sinistros permite-lhe executar uma transformação de sinistros a partir do passo de orquestração de qualquer viagem do utilizador. No exemplo seguinte, o passo da orquestração chama um dos perfis técnicos dedesvinculação, como **unLink-Facebook-OAUTH**. Este perfil técnico chama ao perfil técnico de transformação de sinistros **RemoveAlternativeSecurityIdByIdentityProvider,** que gera uma nova alegação **AlternativeSecurityIds2** que contém a lista de identidades sociais dos utilizadores, removendo a identidade do Facebook das coleções.

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

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores `true`possíveis: ou `false`  (padrão). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |

## <a name="use-a-validation-technical-profile"></a>Utilize um perfil técnico de validação

Um perfil técnico de transformação de sinistros pode ser usado para validar informação. No exemplo seguinte, o [perfil técnico autoafirmado](self-asserted-technical-profile.md) chamado **LocalAccountSignUpWithLogonEmail** pede ao utilizador que introduza o e-mail duas vezes, e depois liga para o perfil técnico de [validação](validation-technical-profile.md) chamado **Validate-Email** para validar os e-mails. O perfil técnico **validado-e-mail** chama a transformação de sinistros **AssertEmailAreEqual** para comparar os dois **e-mails** e **emails de reclamaçõesRepeat**, e lançar uma exceção se não forem iguais de acordo com a comparação especificada.

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

O perfil técnico de transformação de sinistros chama a transformação de **reivindicações Da AssertEmailAreEqual,** que afirma que os e-mails fornecidos pelo utilizador são os mesmos.

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

Um perfil técnico autoafirmado pode ligar para o perfil técnico de validação e mostrar a mensagem de erro conforme especificado nos metadados **UserMessageIfClaimsTransformationStringsAreNotEqual.**

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
