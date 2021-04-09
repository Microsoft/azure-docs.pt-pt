---
title: Configurar requisitos de complexidade de palavras-passe
titleSuffix: Azure AD B2C
description: Como configurar requisitos de complexidade para as palavras-passe fornecidas pelos consumidores no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 81c6e58e34f30d5736c40c77a308321dee28ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224270"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configure requisitos de complexidade para senhas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O Azure Ative Directory B2C (Azure AD B2C) suporta a alteração dos requisitos de complexidade das palavras-passe fornecidas por um utilizador final ao criar uma conta. Por padrão, o Azure AD B2C utiliza senhas **fortes.** O Azure AD B2C também suporta opções de configuração para controlar a complexidade das palavras-passe que os clientes podem usar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Aplicação da regra da palavra-passe

Durante a inscrição ou a redefinição da palavra-passe, um utilizador final deve fornecer uma palavra-passe que cumpra as regras de complexidade. As regras de complexidade da palavra-passe são aplicadas por fluxo de utilizador. É possível que um fluxo de utilizador exija um pino de quatro dígitos durante a inscrição, enquanto outro fluxo de utilizador requer uma corda de oito caracteres durante a inscrição. Por exemplo, pode utilizar um fluxo de utilizador com uma complexidade de senha diferente para adultos do que para crianças.

A complexidade da palavra-passe nunca é aplicada durante a entrada. Os utilizadores nunca são solicitados durante o s-in a alterar a sua palavra-passe porque não cumpre o requisito de complexidade atual.

A complexidade da palavra-passe pode ser configurada nos seguintes tipos de fluxos de utilizador:

- Fluxo de utilizador de inscrição ou de entrada
- Redefinição de senha fluxo de utilizador

Se estiver a utilizar políticas personalizadas, pode[(configurar a complexidade da palavra-passe numa política personalizada).](password-complexity.md)

## <a name="configure-password-complexity"></a>Configure a complexidade da palavra-passe

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
3. No portal Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador**.
2. Selecione um fluxo de utilizador e clique em **Propriedades**.
3. Sob **a complexidade da Palavra-Passe,** altere a complexidade da palavra-passe para este fluxo de utilizador para **Simples,** **Forte** ou **Personalizado**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | Description |
| --- | --- |
| Simples | Uma palavra-passe que é de pelo menos 8 a 64 caracteres. |
| Forte | Uma palavra-passe que é de pelo menos 8 a 64 caracteres. Requer 3 de 4 maiúsculas, maiúsculas, números ou símbolos. |
| Personalizado | Esta opção proporciona o maior controlo sobre as regras de complexidade da palavra-passe.  Permite configurar um comprimento personalizado.  Também permite aceitar senhas só de número (pinos). |

## <a name="custom-options"></a>Opções personalizadas

### <a name="character-set"></a>Conjunto de caracteres

Permite-lhe aceitar apenas dígitos (pinos) ou o conjunto de caracteres completo.

- **Os números só** permitem dígitos (0-9) durante a insintrodução de uma palavra-passe.
- **Tudo** permite qualquer letra, número ou símbolo.

### <a name="length"></a>Comprimento

Permite-lhe controlar os requisitos de comprimento da palavra-passe.

- **O comprimento mínimo** deve ser de, pelo menos, 4.
- **O comprimento máximo** deve ser maior ou igual ao comprimento mínimo e, no máximo, pode ser de 256 caracteres.

### <a name="character-classes"></a>Aulas de caráter

Permite-lhe controlar os diferentes tipos de caracteres utilizados na palavra-passe.

- **2 de 4: Caracteres minúsculos, caracteres maiúsculas, Número (0-9), Símbolo** garante que a palavra-passe contém pelo menos dois tipos de caracteres. Por exemplo, um número e um caráter minúsculo.
- **3 de 4: Caracteres minúsculos, caracteres maiúsculas, Número (0-9), Símbolo** garante que a palavra-passe contém pelo menos três tipos de caracteres. Por exemplo, um número, um caráter minúsculo e um personagem maiúscula.
- **4 de 4: Caracteres minúsculos, caracteres maiúsculas, Número (0-9), Símbolo** garante que a palavra-passe contém tudo para tipos de caracteres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar em frustração do utilizador final. Alguns estudos demonstraram que este requisito não melhora a entropia da palavra-passe. Ver [Diretrizes de Senha do NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Validação predicado de senha

Para configurar a complexidade da palavra-passe, substitua os `newPassword` tipos e `reenterPassword` [reclamar](claimsschema.md) com uma referência a [validações predicados](predicates.md#predicatevalidations). O elemento PredicateValidations agrulia um conjunto de predicados para formar uma validação de entrada de utilizador que pode ser aplicada a um tipo de reclamação. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione o `newPassword` e `reenterPassword` reclamações ao elemento **ClaimsSchema.**

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. [Predicados](predicates.md) define uma validação básica para verificar o valor de um tipo de reclamação e devolve verdadeiro ou falso. A validação é feita utilizando um elemento de método especificado, e um conjunto de parâmetros relevantes para o método. Adicione os seguintes predicados ao elemento **Blocos de Construção,** imediatamente após o fecho do `</ClaimsSchema>` elemento:

    ```xml
    <!-- 
    <BuildingBlocks>-->
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
    <!-- 
    </BuildingBlocks>-->
    ```

1. Adicione as seguintes validações predicados ao elemento **Blocos de Construção,** imediatamente após o fecho do `</Predicates>` elemento:

    ```xml
    <!-- 
    <BuildingBlocks>-->
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
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>Desativar senha forte 

Os seguintes perfis técnicos são [perfis técnicos do Ative Directory,](active-directory-technical-profile.md)que lêem e escrevem dados para o Azure Ative Directory. Substitua estes perfis técnicos no ficheiro de extensão. Use `PersistedClaims` para desativar a política de senha forte. Encontre o elemento **ClaimsProviders.**  Adicione os seguintes fornecedores de reclamações da seguinte forma:

```xml
<!-- 
<ClaimsProviders>-->
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
<!-- 
</ClaimsProviders>-->
```

Se utilizar a política [de inscrição baseada no nome de utilizador,](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin) atualize os `AAD-UserWriteUsingLogonEmail` perfis , e `AAD-UserWritePasswordUsingObjectId` `LocalAccountWritePasswordUsingObjectId` técnicos com a política *DisableStrongPassword.*

Guarde o ficheiro da apólice.

## <a name="test-your-policy"></a>Teste a sua política

### <a name="upload-the-files"></a>Carregar os ficheiros

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **o Quadro de Experiência de Identidade.**
5. Na página 'Políticas Personalizadas', clique em **'Carregar' Política**.
6. Selecione **Overwrite a política se ela existe** e, em seguida, procure e selecione o ficheiro *TrustFrameworkExtensions.xml.*
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política de inscrição ou inscrição. Por exemplo, *B2C_1A_signup_signin.*
2. Para **Candidatura**, selecione a sua candidatura que já se registou anteriormente. Para ver o símbolo, a **URL de resposta** deve mostrar `https://jwt.ms` .
3. Clique em **Executar agora**.
4. Selecione **Iniciar surgiu agora,** insira um endereço de e-mail e introduza uma nova palavra-passe. A orientação é apresentada sobre as restrições de senha. Termine de introduzir as informações do utilizador e, em seguida, clique em **Criar**. Devia ver o conteúdo do símbolo que foi devolvido.

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar a [mudança de senha no Azure Ative Directory B2C](add-password-change-policy.md).
- Saiba mais sobre os [elementos Predicados](predicates.md) e [PredicateValidations](predicates.md#predicatevalidations) na referência IEF.


::: zone-end
