---
title: Passe um token de acesso através de uma política personalizada para a sua app
titleSuffix: Azure AD B2C
description: Saiba como pode passar um token de acesso para os fornecedores de identidade OAuth 2.0 como reivindicação através de uma política personalizada para a sua aplicação no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c434ad6a724ba513caf7923916997600097b43f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387869"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Passe um token de acesso através de uma política personalizada para a sua aplicação no Azure Ative Directory B2C

Uma [política personalizada](custom-policy-get-started.md) no Azure Ative Directory B2C (Azure AD B2C) proporciona aos utilizadores da sua aplicação a oportunidade de se inscreverem ou se inscreverem com um fornecedor de identidade. Quando isso acontece, o Azure AD B2C recebe um sinal de [acesso](tokens-overview.md) do fornecedor de identidade. O Azure AD B2C utiliza esse símbolo para obter informações sobre o utilizador. Adicione um tipo de reclamação e reivindicação de saída à sua política personalizada para passar o token através das aplicações que regista no Azure AD B2C.

O Azure AD B2C suporta a passagem do token de acesso dos fornecedores de identidade [OAuth 2.0](authorization-code-flow.md) e [OpenID Connect.](openid-connect.md) Para todos os outros fornecedores de identidade, a reclamação é devolvida em branco.

## <a name="prerequisites"></a>Pré-requisitos

* A sua política personalizada está configurada com um provedor de identidade OAuth 2.0 ou OpenID Connect.

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
4. Abra o seu ficheiro de política partidária, como *SignUpOrSignIn.xml, *e adicione o elemento **OutputClaim** ao **Programa Técnico:**

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
5. Na página 'Políticas Personalizadas', clique em **''' ''' 'Carregar' Política**.
6. Selecione **Overwrite a política se ela existe**e, em seguida, procure e selecione o ficheiro *TrustframeworkExtensions.xml.*
7. Selecione **Carregar**.
8. Repita os passos 5 a 7 para o ficheiro do partido, como *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abre a política que mudaste. Por exemplo, *B2C_1A_signup_signin.*
2. Para **Candidatura**, selecione a sua candidatura que já se registou anteriormente. Para ver o símbolo no exemplo abaixo, o **URL de resposta** deve mostrar `https://jwt.ms` .
3. Selecione **Executar agora**.

    Deve ver algo semelhante ao seguinte exemplo:

    ![Ficha descodificada em jwt.ms com bloco idp_access_token em destaque](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre fichas na [referência simbólica Azure Ative Directory B2C](tokens-overview.md).
