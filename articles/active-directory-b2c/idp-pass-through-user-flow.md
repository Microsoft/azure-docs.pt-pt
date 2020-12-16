---
title: Passe um token de acesso do fornecedor de identidade para a sua app
titleSuffix: Azure AD B2C
description: Saiba como passar um token de acesso para os fornecedores de identidade OAuth 2.0 como uma reivindicação num fluxo de utilizador em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e379b3fc77716eeea28b3dbeb9c3a022f0f16106
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516304"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Passe um token de acesso de um fornecedor de identidade à sua aplicação no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Um [fluxo de utilizador](user-flow-overview.md) no Azure Ative Directory B2C (Azure AD B2C) proporciona aos utilizadores da sua aplicação a oportunidade de se inscreverem ou se inscreverem com um fornecedor de identidade. Quando a viagem começa, o Azure AD B2C recebe um sinal de [acesso](tokens-overview.md) do fornecedor de identidade. O Azure AD B2C utiliza esse símbolo para obter informações sobre o utilizador. Você ativa uma reclamação no fluxo do seu utilizador para passar o token através das aplicações que regista no Azure AD B2C.

::: zone pivot="b2c-user-flow"

O Azure AD B2C suporta a aprovação do token de acesso de fornecedores de identidade [OAuth 2.0,](add-identity-provider.md) que incluem [Facebook](identity-provider-facebook.md) e [Google](identity-provider-google.md). Para todos os outros fornecedores de identidade, a reclamação é devolvida em branco.

::: zone-end

::: zone pivot="b2c-custom-policy"

O Azure AD B2C suporta a passagem do token de acesso dos fornecedores de identidade [OAuth 2.0](authorization-code-flow.md) e [OpenID Connect.](openid-connect.md) Para todos os outros fornecedores de identidade, a reclamação é devolvida em branco.

::: zone-end

O seguinte diagrama mostra como um token de fornecedor de identidade retorna à sua aplicação: 

![O fornecedor de identidade passa através do fluxo](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Ativar a reclamação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **os fluxos do Utilizador (políticas)** e, em seguida, selecione o fluxo do utilizador. Por exemplo, **B2C_1_signupsignin1.**
5. Selecione **Afirmações de aplicação**.
6. Ativar a reclamação **do Fornecedor de Identidade Access Token.**

    ![Ativar a reclamação do Access ToKen do Fornecedor de Identidade](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. Clique **em Guardar** para salvar o fluxo do utilizador.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

Ao testar as suas aplicações em Azure AD B2C, pode ser útil ter o token AD B2C Azure devolvido `https://jwt.ms` para rever as reclamações no mesmo.

1. Na página geral do fluxo do utilizador, selecione Executar o **fluxo do utilizador**.
2. Para **Candidatura**, selecione a sua candidatura que já se registou anteriormente. Para ver o símbolo no exemplo abaixo, o **URL de resposta** deve mostrar `https://jwt.ms` .
3. Clique **no fluxo do utilizador executar** e, em seguida, iniciar scontabilidade com as credenciais da sua conta. Deve ver o sinal de acesso do fornecedor de identidade na **reclamação idp_access_token.**

    Deve ver algo semelhante ao seguinte exemplo:

    ![Ficha descodificada em jwt.ms com bloco idp_access_token em destaque](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Adicione os elementos de reivindicação

1. Abra o seu ficheiro *TrustframeworkExtensions.xml* e adicione o seguinte elemento **ClaimType** com um identificador do `identityProviderAccessToken` elemento **ClaimsSchema:**

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Adicione o elemento **OutputClaim** ao elemento **TécnicoProfile** para cada fornecedor de identidade OAuth 2.0 para o que gostaria de ter o token de acesso. O exemplo a seguir mostra o elemento adicionado ao perfil técnico do Facebook:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Guarde o ficheiro *TrustframeworkExtensions.xml.*
4. Abra o seu ficheiro de política partidária, como *SignUpOrSignIn.xml,* e adicione o elemento **OutputClaim** ao **Programa Técnico:**

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Guarde o ficheiro da apólice.

## <a name="test-your-policy"></a>Teste a sua política

Ao testar as suas aplicações em Azure AD B2C, pode ser útil ter o token AD B2C Azure devolvido `https://jwt.ms` para poder rever as reclamações no mesmo.

### <a name="upload-the-files"></a>Carregar os ficheiros

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C clicando no filtro **de subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **o Quadro de Experiência de Identidade.**
5. Na página 'Políticas Personalizadas', clique em **'Carregar' Política**.
6. Selecione **Overwrite a política se ela existe** e, em seguida, procure e selecione o ficheiro *TrustframeworkExtensions.xml.*
7. Selecione **Carregar**.
8. Repita os passos 5 a 7 para o ficheiro do partido, como *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abre a política que mudaste. Por exemplo, *B2C_1A_signup_signin.*
2. Para **Candidatura**, selecione a sua candidatura que já se registou anteriormente. Para ver o símbolo no exemplo abaixo, o **URL de resposta** deve mostrar `https://jwt.ms` .
3. Selecione **Executar agora**.

    Deve ver algo semelhante ao seguinte exemplo:

    ![Ficha descodificada em jwt.ms com bloco idp_access_token em destaque](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [visão geral dos tokens Azure AD B2C](tokens-overview.md).
