---
title: Passar um token de acesso através de uma política personalizada para a sua aplicação no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como pode passar um token de acesso para fornecedores de identidade de OAuth2.0 como uma afirmação através de uma política personalizada para a sua aplicação no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 784387b119bff6445015419adfd3bc0e52eee43f
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402648"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Passar um token de acesso através de uma política personalizada para a sua aplicação no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

R [política personalizada](active-directory-b2c-get-started-custom.md) no Azure Active Directory (Azure AD) B2C fornece a utilizadores da sua aplicação uma oportunidade de se inscrever ou iniciar sessão com um fornecedor de identidade. Quando isto acontecer, o Azure AD B2C recebe uma [token de acesso](active-directory-b2c-reference-tokens.md) do fornecedor de identidade. O Azure AD B2C utiliza esse token para obter informações sobre o utilizador. Adicionar que um tipo de afirmação e a saída de afirmações para a sua política personalizada para passar o token por meio para os aplicativos que Registre-se no Azure AD B2C. 

Azure AD B2C suporta passando o token de acesso de [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) e [OpenID Connect](active-directory-b2c-reference-oidc.md) fornecedores de identidade. Para todos os outros fornecedores de identidade, a afirmação é devolvida em branco.

## <a name="prerequisites"></a>Pré-requisitos

- A diretiva personalizada é configurada com um fornecedor de identidade do OAuth 2.0 ou OpenID Connect.

## <a name="add-the-claim-elements"></a>Adicionar os elementos de afirmação 

1. Abra sua *TrustframeworkExtensions.xml* de ficheiros e adicione as seguintes **ClaimType** elemento com um identificador de `identityProviderAccessToken` para o **ClaimsSchema** elemento:

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

2. Adicionar a **OutputClaim** elemento para a **TechnicalProfile** elemento para cada fornecedor de identidade do OAuth 2.0 que gostaria que o token de acesso para. O exemplo seguinte mostra o elemento adicionado para o perfil técnico do Facebook:

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

3. Guardar a *TrustframeworkExtensions.xml* ficheiro.
4. Abra o ficheiro de política de terceiros entidade confiadora, tal como *SignUpOrSignIn.xml*e adicione o **OutputClaim** elemento para a **TechnicalProfile**:

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

5. Guarde o ficheiro de política.

## <a name="test-your-policy"></a>Testar a política

Ao testar as suas aplicações no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado para `https://jwt.ms` para conseguir analisar as afirmações no mesmo.

### <a name="upload-the-files"></a>Carregue os ficheiros

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **arquitetura de experiências de identidade**.
5. Na página de políticas personalizadas, clique em **carregar política**.
6. Selecione **substituir a política, se existir**e, em seguida, procure e selecione o *TrustframeworkExtensions.xml* ficheiro.
7. Clique em **Carregar**.
8. Repita os passos 5 a 7 para o ficheiro da entidade confiadora de terceiros, como *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que alterou. Por exemplo, *B2C_1A_signup_signin*.
2. Para **aplicativo**, selecione a aplicação que registou anteriormente. Para ver o token no exemplo abaixo, o **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**.

    Deverá ver algo semelhante ao seguinte exemplo:

    ![Token descodificada](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre tokens no [referência de token do Azure Active Directory](active-directory-b2c-reference-tokens.md).





