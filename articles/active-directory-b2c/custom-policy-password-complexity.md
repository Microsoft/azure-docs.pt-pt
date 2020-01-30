---
title: Configure a complexidade da palavra-passe utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como configurar os requisitos de complexidade da palavra-passe utilizando uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d0caa029bd33da499db23f218b2392344c4585ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849074"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configure a complexidade da palavra-passe utilizando políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Ative Directory B2C (Azure AD B2C), pode configurar os requisitos de complexidade das palavras-passe fornecidas por um utilizador ao criar uma conta. Por predefinição, o Azure AD B2C utiliza senhas **fortes.** Este artigo mostra-lhe como configurar a complexidade da palavra-passe em [políticas personalizadas.](custom-policy-overview.md) Também é possível configurar a complexidade da palavra-passe nos [fluxos do utilizador.](user-flow-password-complexity.md)

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Get started com políticas personalizadas no Diretório Ativo B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>Adicione os elementos

1. Copie o ficheiro *SignUpOrSignIn.xml* que descarregou com o pacote de arranque e nomeie-o *SingUpOrSignInPasswordComplexity.xml*.
2. Abra o ficheiro *SingUpOrSignInPasswordComplexity.xml* e altere o **PolicyId** e o **PublicPolicyUri** para um novo nome de política. Por exemplo, *B2C_1A_signup_signin_password_complexity.*
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

4. [Os predicados](predicates.md) têm tipos de métodos de `IsLengthRange` ou `MatchesRegex`. O tipo `MatchesRegex` é usado para combinar com uma expressão regular. O tipo `IsLengthRange` tem um comprimento mínimo e máximo de corda. Adicione um elemento **Predicado** ao elemento **BuildingBlocks** se não existir com os seguintes elementos **Predicados:**

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

5. Cada elemento **de validação** de entrada é construído utilizando os elementos **Predicados** definidos. Este elemento permite-lhe realizar agregações booleanas semelhantes a `and` e `or`. Adicione um elemento **De Validação** de Entrada ao elemento **BuildingBlocks** se não existir com o seguinte elemento **de validação** de entrada:

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

6. Certifique-se de que o perfil técnico **PolicyProfile** contém os seguintes elementos:

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

7. Guarde o ficheiro político.

## <a name="test-your-policy"></a>Teste a sua política

Ao testar as suas aplicações em Azure AD B2C, pode ser útil que o token Azure AD B2C seja devolvido à `https://jwt.ms` para poder rever as reclamações nele.

### <a name="upload-the-files"></a>Faça upload dos ficheiros

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **Identity Experience Framework**.
5. Na página Políticas Personalizadas, clique na **Política de Upload**.
6. **Selecione Sobrepor a apólice se ela existir,** e depois procurar e selecionar o ficheiro *SingUpOrSignInPasswordComplexity.xml.*
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abre a política que mudaste. Por exemplo, *B2C_1A_signup_signin_password_complexity.*
2. Para **Aplicação,** selecione a sua aplicação que registou anteriormente. Para ver o símbolo, o **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**.
4. **Selecione Iniciar sessão agora,** insira um endereço de e-mail e introduza uma nova senha. É apresentada orientação sobre restrições de senha. Termine de introduzir as informações do utilizador e, em seguida, clique em **Criar**. Devia ver o conteúdo do símbolo que foi devolvido.

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar a alteração da [palavra-passe utilizando políticas personalizadas no Diretório Ativo Azure B2C](custom-policy-password-change.md).


