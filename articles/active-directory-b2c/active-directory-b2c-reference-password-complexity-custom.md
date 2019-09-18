---
title: Configurar a complexidade de senha usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Como configurar requisitos de complexidade de senha usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6454d380b0f34e940951e3de44d1dee0ff6b597f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065536"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a complexidade de senha usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory B2C (Azure AD B2C), você pode configurar os requisitos de complexidade para senhas que são fornecidas por um usuário ao criar uma conta. Por padrão, Azure AD B2C usa senhas **fortes** . Este artigo mostra como configurar a complexidade de senha em [políticas personalizadas](active-directory-b2c-overview-custom.md). Também é possível configurar a complexidade de senha em [fluxos de usuário](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas no Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Adicionar os elementos

1. Copie o arquivo *SignUpOrSignIn. xml* que você baixou com o pacote inicial e nomeie-o como *SingUpOrSignInPasswordComplexity. xml*.
2. Abra o arquivo *SingUpOrSignInPasswordComplexity. xml* e altere a **PolicyId** e o **PublicPolicyUri** para um novo nome de política. Por exemplo, *B2C_1A_signup_signin_password_complexity*.
3. Adicione os seguintes elementos **ClaimType** com identificadores de `newPassword` e `reenterPassword`:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. Os [predicados](predicates.md) têm tipos `IsLengthRange` de `MatchesRegex`método de ou. O `MatchesRegex` tipo é usado para corresponder a uma expressão regular. O `IsLengthRange` tipo usa um comprimento de cadeia de caracteres mínimo e máximo. Adicione um elemento **predicados** ao elemento **BuildingBlocks** se ele não existir com os seguintes elementos de **predicado** :

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Cada elemento **InputValidation** é construído usando os elementos **predicados** definidos. Esse elemento permite que você execute agregações booleanas que são semelhantes a `and` e `or`. Adicione um elemento **InputValidations** ao elemento **BuildingBlocks** se ele não existir com o seguinte elemento **InputValidation** :

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Verifique se o perfil técnico **PolicyProfile** contém os seguintes elementos:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Salve o arquivo de política.

## <a name="test-your-policy"></a>Testar sua política

Ao testar seus aplicativos no Azure ad B2C, pode ser útil ter o token de Azure ad B2C retornado `https://jwt.ms` para poder revisar as declarações nele.

### <a name="upload-the-files"></a>Carregar os arquivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **Identity Experience Framework**.
5. Na página políticas personalizadas, clique em **carregar política**.
6. Selecione **substituir a política, se ela existir**, e, em seguida, procure e selecione o arquivo *SingUpOrSignInPasswordComplexity. xml* .
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que você alterou. Por exemplo, *B2C_1A_signup_signin_password_complexity*.
2. Para **aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token, a **URL de resposta** deve `https://jwt.ms`ser mostrada.
3. Clique em **Executar agora**.
4. Selecione **inscrever-se agora**, insira um endereço de email e insira uma nova senha. As diretrizes são apresentadas em restrições de senha. Termine de inserir as informações do usuário e, em seguida, clique em **criar**. Você deve ver o conteúdo do token que foi retornado.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [Configurar a alteração de senha usando políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


