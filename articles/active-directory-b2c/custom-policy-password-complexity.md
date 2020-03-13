---
title: Configure a complexidade da palavra-passe utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como configurar os requisitos de complexidade da palavra-passe utilizando uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: af6a7611381cbf7a251e65969d156f4c40d71843
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126766"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configure a complexidade da palavra-passe utilizando políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Ative Directory B2C (Azure AD B2C), pode configurar os requisitos de complexidade das palavras-passe fornecidas por um utilizador ao criar uma conta. Por predefinição, o Azure AD B2C utiliza senhas **fortes.** Este artigo mostra-lhe como configurar a complexidade da palavra-passe em [políticas personalizadas.](custom-policy-overview.md) Também é possível configurar a complexidade da palavra-passe nos [fluxos do utilizador.](user-flow-password-complexity.md)

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada de trabalho para inscrever-se e entrar com contas locais.


## <a name="add-the-elements"></a>Adicione os elementos

Para configurar a complexidade da palavra-passe, sobrepor-se aos tipos de `newPassword` e `reenterPassword` [de reclamação](claimsschema.md) com referência a [validações predicadas](predicates.md#predicatevalidations). O elemento PredicadoValidaum agrupa um conjunto de predicados para formar uma validação de entrada do utilizador que pode ser aplicada a um tipo de reclamação. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>.

1. Procure o elemento [BuildingBlocks](buildingblocks.md) . Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione as `newPassword` e `reenterPassword` reclamações ao elemento **ClaimsSchema.**

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predicados](predicates.md) define uma validação básica para verificar o valor de um tipo de reclamação e devolve verdadeiro ou falso. A validação é feita utilizando um elemento de método especificado, e um conjunto de parâmetros relevantes para o método. Adicione os seguintes predicados ao elemento **BuildingBlocks,** imediatamente após o fecho do elemento `</ClaimsSchema>`:

    ```XML
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

1. Adicione as seguintes validações predicadas ao elemento **BuildingBlocks,** imediatamente após o fecho do elemento `</Predicates>`:

    ```XML
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

1. Os seguintes perfis técnicos são [perfis técnicos ative diretórios,](active-directory-technical-profile.md)que lêem e escrevem dados para o Azure Ative Directory. Anular estes perfis técnicos no ficheiro de extensão. Utilize `PersistedClaims` para desativar a política de senhas fortes. Encontre o elemento **ClaimsProviders.**  Adicione os seguintes prestadores de reclamações da seguinte forma:

    ```XML
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

1. Guarde o ficheiro político.

## <a name="test-your-policy"></a>Teste a sua política

### <a name="upload-the-files"></a>Faça upload dos ficheiros

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione Quadro de **Experiência de Identidade**.
5. Na página Políticas Personalizadas, clique na **Política de Upload**.
6. **Selecione Sobrepor a apólice se ela existir**, e depois procurar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política de inscrição ou inscrição. Por exemplo, *B2C_1A_signup_signin.*
2. Para **Aplicação,** selecione a sua aplicação que registou anteriormente. Para ver o símbolo, o **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**.
4. **Selecione Iniciar sessão agora,** insira um endereço de e-mail e introduza uma nova senha. É apresentada orientação sobre restrições de senha. Termine de introduzir as informações do utilizador e, em seguida, clique em **Criar**. Devia ver o conteúdo do símbolo que foi devolvido.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como configurar a alteração da [palavra-passe utilizando políticas personalizadas no Diretório Ativo Azure B2C](custom-policy-password-change.md).
- - Saiba mais sobre os [elementos Predicados](predicates.md) e [Predicados Validações](predicates.md#predicatevalidations) na referência IEF.
