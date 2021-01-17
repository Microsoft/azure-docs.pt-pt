---
title: Configurar inscrição e iniciar sôm-in com uma conta ID.me
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas ID.me nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c7d43a55878a07e424ce1b6f55782502c244239c
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98537931"
---
# <a name="set-up-sign-up-and-sign-in-with-a-idme-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta ID.me utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]


## <a name="create-an-idme-application"></a>Criar uma aplicação ID.me

Para permitir o sessão de sessão para utilizadores com uma conta ID.me no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação em [ID.me Recursos de Desenvolvimento para API & SDK.](https://developers.id.me/) Para mais informações, consulte [o Guia de Integração da OAuth.](https://developers.id.me/documentation/oauth/overview/kyc) Se ainda não tiver uma conta de programador ID.me, pode inscrever-se em [https://developers.id.me/registration/new](https://developers.id.me/registration/new) .

1. Inscreva-se no [ID.me Developer Resources for API & SDK](https://developers.id.me/) com as suas credenciais de conta ID.me.
1. Selecione **Ver as minhas aplicações** e selecione **Continue**.
1. Selecione **Criar novo**
    1. Introduza um **nome** e **nome de exibição**.
    1. In **Redirect URI** insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`Substitua-o pelo nome do seu inquilino. 
1. Clique em **Continue** (Continuar).
1. Copiar os valores do **ID do Cliente** e **do Cliente Secreto.** Precisa de ambos para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que você já gravou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione **Identity Experience Framework**.
5. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha `Manual` .
7. Insira um **Nome** para a chave de política. Por exemplo, `IdMeSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
9. Para **a utilização da chave**, selecione `Signature` .
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se quiser que os utilizadores assinem através de uma conta ID.me, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir uma conta ID.me como prestador de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política.

1. Abra a *TrustFrameworkExtensions.xml.*
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>id.me</Domain>
      <DisplayName>ID.me</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="IdMe-OAUTH2">
          <DisplayName>IdMe</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">api.id.me</Item>
            <Item Key="authorization_endpoint">https://api.id.me/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.id.me/oauth/token</Item>
            <Item Key="ClaimsEndpoint">https://api.id.me/api/public/v2/attributes.json</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="scope">openid alumni</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your ID.me application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdMeSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="uuid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="fname" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lname" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="me.id.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateDisplayNameFromFirstNameAndLastName" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Desa** esta client_id ao ID do pedido a partir do registo do pedido.
5. Guarde o ficheiro.

### <a name="add-the-claims-transformations"></a>Adicione as transformações de sinistros

Em seguida, você precisa de uma transformação de reclamações para criar a reivindicação do nome de exibição. Adicione a seguinte transformação de reclamações ao `<ClaimsTransformations>` elemento interior `<BuildingBlocks>` . 

```xml
 <ClaimsTransformations>
  <ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
    </OutputClaims>
   </ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="upload-the-extension-file-for-verification"></a>Faça o upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com a tua conta ID.me. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
2. Ativar **a política em caso de existência** e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição/inscrição. Para disponibilizá-lo, cria uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifica-a de modo a que também tenha o fornecedor de identidade ID.me.

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
3. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInIdMe`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição/inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta ID.me, um novo botão aparece quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que criou.
2. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `IdMeExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="IdMeExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, é que a Azure AD B2C comunique com uma conta ID.me para receber um token.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para iD que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="IdMeExchange" TechnicalProfileReferenceId="IdMe-OAUTH2" />
    ```

    Atualizar o valor do **TécnicoProfileReferenceD** para o ID do perfil técnico que criou anteriormente. Por exemplo, `IdMe-OAUTH2`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignInIdMe.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignIdMe`.
1. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_IdMe`
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova jornada do utilizador que criou (SignUpSignIdMe).
1. Guarde as suas alterações, faça o upload do ficheiro.
1. De acordo com **as políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Para **Select Application**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione **Executar agora** e selecione ID.me para iniciar sins com ID.me e testar a política personalizada.

::: zone-end