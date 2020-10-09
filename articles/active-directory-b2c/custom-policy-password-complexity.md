---
title: Configure a complexidade da palavra-passe usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como configurar os requisitos de complexidade da palavra-passe utilizando uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4ab196e894fc53b1243ac363f9863d5c7d4e328f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389008"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configure a complexidade da palavra-passe utilizando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Ative Directory B2C (Azure AD B2C), pode configurar os requisitos de complexidade para as palavras-passe que são fornecidas por um utilizador ao criar uma conta. Por padrão, o Azure AD B2C utiliza senhas **fortes.** Este artigo mostra-lhe como configurar a complexidade da palavra-passe em [políticas personalizadas.](custom-policy-overview.md) Também é possível configurar a complexidade da palavra-passe nos [fluxos de utilizador.](user-flow-password-complexity.md)

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Começar com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar s inscrição com contas locais.


## <a name="add-the-elements"></a>Adicione os elementos

Para configurar a complexidade da palavra-passe, substitua os `newPassword` tipos e `reenterPassword` [reclamar](claimsschema.md) com uma referência a [validações predicados](predicates.md#predicatevalidations). O elemento PredicateValidations agrulia um conjunto de predicados para formar uma validação de entrada de utilizador que pode ser aplicada a um tipo de reclamação. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione o `newPassword` e `reenterPassword` reclamações ao elemento **ClaimsSchema.**

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predicados](predicates.md) define uma validação básica para verificar o valor de um tipo de reclamação e devolve verdadeiro ou falso. A validação é feita utilizando um elemento de método especificado, e um conjunto de parâmetros relevantes para o método. Adicione os seguintes predicados ao elemento **Blocos de Construção,** imediatamente após o fecho do `</ClaimsSchema>` elemento:

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Adicione as seguintes validações predicados ao elemento **Blocos de Construção,** imediatamente após o fecho do `</Predicates>` elemento:

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. Os seguintes perfis técnicos são [perfis técnicos do Ative Directory,](active-directory-technical-profile.md)que lêem e escrevem dados para o Azure Ative Directory. Substitua estes perfis técnicos no ficheiro de extensão. Use `PersistedClaims` para desativar a política de senha forte. Encontre o elemento **ClaimsProviders.**  Adicione os seguintes fornecedores de reclamações da seguinte forma:

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Guarde o ficheiro da apólice.

## <a name="test-your-policy"></a>Teste a sua política

### <a name="upload-the-files"></a>Carregar os ficheiros

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **o Quadro de Experiência de Identidade.**
5. Na página 'Políticas Personalizadas', clique em **''' ''' 'Carregar' Política**.
6. Selecione **Overwrite a política se ela existe**e, em seguida, procure e selecione o ficheiro *TrustFrameworkExtensions.xml.*
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política de inscrição ou inscrição. Por exemplo, *B2C_1A_signup_signin.*
2. Para **Candidatura**, selecione a sua candidatura que já se registou anteriormente. Para ver o símbolo, a **URL de resposta** deve mostrar `https://jwt.ms` .
3. Clique em **Executar agora**.
4. Selecione **Iniciar surgiu agora,** insira um endereço de e-mail e introduza uma nova palavra-passe. A orientação é apresentada sobre as restrições de senha. Termine de introduzir as informações do utilizador e, em seguida, clique em **Criar**. Devia ver o conteúdo do símbolo que foi devolvido.

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar a [mudança de senha utilizando políticas personalizadas no Azure Ative Directory B2C](custom-policy-password-change.md).
- Saiba mais sobre os [elementos Predicados](predicates.md) e [PredicateValidations](predicates.md#predicatevalidations) na referência IEF.
