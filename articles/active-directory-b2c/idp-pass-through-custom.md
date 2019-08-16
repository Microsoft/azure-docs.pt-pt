---
title: Passe um token de acesso por meio de uma política personalizada para seu aplicativo no Azure Active Directory B2C
description: Saiba como você pode passar um token de acesso para provedores de identidade OAuth 2.0 como uma declaração por meio de uma política personalizada para seu aplicativo no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6795af0829a288c36cad5b848fed50a99dc1bfc
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510124"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Passe um token de acesso por meio de uma política personalizada para seu aplicativo no Azure Active Directory B2C

Uma [política personalizada](active-directory-b2c-get-started-custom.md) no Azure Active Directory B2C (Azure ad B2C) fornece aos usuários de seu aplicativo uma oportunidade de se inscrever ou entrar com um provedor de identidade. Quando isso acontece, Azure AD B2C recebe um [token de acesso](active-directory-b2c-reference-tokens.md) do provedor de identidade. Azure AD B2C usa esse token para recuperar informações sobre o usuário. Você adiciona um tipo de declaração e uma declaração de saída à sua política personalizada para passar o token para os aplicativos que você registra no Azure AD B2C.

Azure AD B2C dá suporte à passagem do token de acesso dos provedores de identidade [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) e [OpenID Connect](active-directory-b2c-reference-oidc.md) . Para todos os outros provedores de identidade, a declaração é retornada em branco.

## <a name="prerequisites"></a>Pré-requisitos

* Sua política personalizada está configurada com um provedor de identidade OAuth 2,0 ou OpenID Connect.

## <a name="add-the-claim-elements"></a>Adicionar os elementos de declaração

1. Abra o arquivo *TrustframeworkExtensions. xml* e adicione o seguinte elemento **ClaimType** com um identificador de `identityProviderAccessToken` para o elemento **ClaimsSchema** :

    ```XML
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

2. Adicione o elemento **OutputClaim** ao elemento **TechnicalProfile** para cada provedor de identidade OAuth 2,0 para o qual você deseja obter o token de acesso. O exemplo a seguir mostra o elemento adicionado ao perfil técnico do Facebook:

    ```XML
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

3. Salve o arquivo *TrustframeworkExtensions. xml* .
4. Abra o arquivo de política de terceira parte confiável, como *SignUpOrSignIn. xml*, e adicione o elemento **OutputClaim** ao **TechnicalProfile**:

    ```XML
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

5. Salve o arquivo de política.

## <a name="test-your-policy"></a>Testar sua política

Ao testar seus aplicativos no Azure ad B2C, pode ser útil ter o token de Azure ad B2C retornado `https://jwt.ms` para poder revisar as declarações nele.

### <a name="upload-the-files"></a>Carregar os arquivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C clicando no filtro **diretório + assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **Identity Experience Framework**.
5. Na página políticas personalizadas, clique em **carregar política**.
6. Selecione **substituir a política, se ela existir**, e, em seguida, procure e selecione o arquivo *TrustframeworkExtensions. xml* .
7. Selecione **Upload**.
8. Repita as etapas de 5 a 7 para o arquivo de terceira parte confiável, como *SignUpOrSignIn. xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que você alterou. Por exemplo, *B2C_1A_signup_signin*.
2. Para **aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token no exemplo abaixo, a **URL de resposta** deve ser `https://jwt.ms`mostrada.
3. Selecione **executar agora**.

    Você verá algo semelhante ao exemplo a seguir:

    ![Token decodificado em jwt.ms com bloco idp_access_token realçado](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre tokens na [referência de token de Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).
