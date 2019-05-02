---
title: Configurar a complexidade de palavra-passe com as políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Como configurar os requisitos de complexidade de palavra-passe utilizando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7fb90720b8ee3834a0451cc70b026099dadaf369
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682482"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a complexidade de palavra-passe com as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory (Azure AD) B2C, pode configurar os requisitos de complexidade de palavras-passe que são fornecidos por um utilizador quando criar uma conta. Por predefinição, o Azure AD B2C utiliza **forte** palavras-passe. Este artigo mostra-lhe como configurar a complexidade de palavra-passe na [políticas personalizadas](active-directory-b2c-overview-custom.md). Também é possível configurar a complexidade de palavra-passe na [fluxos de utilizador](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos na [introdução às políticas personalizadas no Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Adicionar os elementos

1. Copiar o *SignUpOrSignIn.xml* que transferiu com o pacote de iniciante e um nome de ficheiro *SingUpOrSignInPasswordComplexity.xml*.
2. Abra o *SingUpOrSignInPasswordComplexity.xml* de ficheiros e alterar o **PolicyId** e o **PublicPolicyUri** para um novo nome de política. Por exemplo, *B2C_1A_signup_signin_password_complexity*.
3. Adicione as seguintes **ClaimType** elementos com os identificadores de `newPassword` e `reenterPassword`:

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

4. [Predicados](predicates.md) têm tipos de método de `IsLengthRange` ou `MatchesRegex`. O `MatchesRegex` tipo é usado para corresponder uma expressão regular. O `IsLengthRange` tipo assume um comprimento de cadeia de caracteres mínimo e máximo. Adicionar uma **predicados** elemento para a **BuildingBlocks** elemento se não existir com o seguinte procedimento **predicado** elementos:

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

5. Cada **InputValidation** elemento é construído utilizando a definidos **predicado** elementos. Esse elemento permite-lhe efetuar agregações booleanas, que são semelhantes às `and` e `or`. Adicionar uma **InputValidations** elemento para a **BuildingBlocks** elemento se não existir com o seguinte procedimento **InputValidation** elemento:

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

6. Certifique-se de que o **PolicyProfile** perfil técnico contém os seguintes elementos:

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

7. Guarde o ficheiro de política.

## <a name="test-your-policy"></a>Testar a política

Ao testar as suas aplicações no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado para `https://jwt.ms` para conseguir analisar as afirmações no mesmo.

### <a name="upload-the-files"></a>Carregue os ficheiros

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **arquitetura de experiências de identidade**.
5. Na página de políticas personalizadas, clique em **carregar política**.
6. Selecione **substituir a política, se existir**e, em seguida, procure e selecione o *SingUpOrSignInPasswordComplexity.xml* ficheiro.
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que alterou. Por exemplo, *B2C_1A_signup_signin_password_complexity*.
2. Para **aplicativo**, selecione a aplicação que registou anteriormente. Para ver o token, o **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**.
4. Selecione **Inscreva-se agora**, introduza um endereço de e-mail e introduza uma palavra-passe nova. Documentação de orientação é apresentada em restrições de palavra-passe. Termina de introduzir as informações de utilizador e, em seguida, clique em **criar**. Deverá ver o conteúdo do token que foi devolvido.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [configurar a alteração de palavra-passe com as políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


