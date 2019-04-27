---
title: Configurar o início de sessão para um fornecedor de identidade do multi-inquilino do Azure AD com as políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Adicione um fornecedor de identidade do multi-inquilino do Azure AD com as políticas personalizadas - Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 41b170ea66b1cb4c830ad0327ac2e1e3d2922b04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316821"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão para o multi-inquilino do Azure Active Directory com as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para utilizadores com o ponto de extremidade do multi-inquilino do Azure Active Directory (Azure AD) utilizando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure AD B2C. Isto permite aos utilizadores a partir de múltiplos inquilinos do Azure AD para iniciar sessão no Azure AD B2C sem configurar um fornecedor de técnico para cada inquilino. No entanto, os convidados membros em qualquer um desses inquilinos **não irá** conseguir iniciar sessão. Para isso, precisa [configurar individualmente cada inquilino](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com` é utilizado o organizacional para inquilino do Azure AD e `fabrikamb2c.onmicrosoft.com` é utilizado como o inquilino do Azure AD B2C nas instruções seguintes.

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos na [introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registar uma aplicação

Para ativar o início de sessão para que os utilizadores específicos de um organização do Azure AD, terá de registar uma aplicação organizacionais no inquilino do Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o Azure organizacional inquilino do AD (contoso.com) ao clicar o **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **registos das aplicações**.
4. Selecione **Novo registo de aplicação**.
5. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
6. Para o **tipo de aplicação**, selecione `Web app / API`.
7. Para o **URL de início de sessão**, introduza o seguinte URL em todas as letras minúsculas, onde `your-tenant` é substituído pelo nome do inquilino do Azure AD B2C (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```
    
8. Clique em **Criar**. Copiar o **ID da aplicação** a ser utilizado mais tarde.
9. Selecione a aplicação e, em seguida, selecione **definições**.
10. Selecione **chaves**, introduza a descrição da chave, selecione uma duração e, em seguida, clique em **guardar**. Copie o valor da chave que é apresentada a ser utilizado mais tarde.
11. Sob **configurações**, selecione **propriedades**, defina **vários inquilinos** para `Yes`e, em seguida, clique em **guardar**

## <a name="create-a-policy-key"></a>Criar uma chave de política

Precisa armazenar a chave da aplicação que criou no seu inquilino do Azure AD B2C.

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
2. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
3. Na página de descrição geral, selecione **arquitetura de experiências de identidade - pré-visualização**.
4. Selecione **chaves de política** e, em seguida, selecione **Add**.
5. Para **opções**, escolha `Manual`.
6. Introduza um **nome** para a chave de política. Por exemplo, `ContosoAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente o nome da sua chave.
7. Na **segredo**, introduza a chave de aplicação que registou anteriormente.
8. Para **utilização de chave**, selecione `Signature`.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um fornecedor de afirmações

Se pretender que os utilizadores iniciem sessão através da utilização do Azure AD, terá de definir do Azure AD como um fornecedor de afirmações com Azure AD B2C possam comunicar através de um ponto de extremidade. O ponto final fornece um conjunto de afirmações que são utilizadas pelo Azure AD B2C para verificar que um utilizador específico foi autenticado. 

Pode definir do Azure AD como um fornecedor de afirmações ao adicionar o Azure AD para o **ClaimsProvider** elemento no arquivo de extensão da sua política.

1. Abra o *TrustFrameworkExtensions.xml*.
2. Encontrar o **ClaimsProviders** elemento. Se não existir, adicione-o sob o elemento raiz.
3. Adicionar um novo **ClaimsProvider** da seguinte forma:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Sob o **ClaimsProvider** elemento, atualize o valor para **domínio** para um valor exclusivo que pode ser utilizado para distinguir de outros fornecedores de identidade.
5. Sob o **TechnicalProfile** elemento, atualize o valor para **DisplayName**. Este valor é apresentado no botão de início de sessão no seu ecrã de início de sessão.
6. Definir **client_id** para o ID de aplicação do registo de aplicação de mulity inquilino do Azure AD.

### <a name="restrict-access"></a>Restringir o acesso

> [!NOTE]
> Usando `https://sts.windows.net` como o valor de **ValidTokenIssuerPrefixes** permite que todos os utilizadores do Azure AD iniciar sessão na sua aplicação.

Terá de atualizar a lista de emissores de token válidas e restringir o acesso a uma lista específica de utilizadores de inquilino do Azure AD, que pode iniciar sessão. Para obter os valores, precisa examinar os metadados para cada um dos específico inquilinos do Azure AD que deseja que os utilizadores iniciem sessão a partir. O formato dos dados é semelhante ao seguinte: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, onde `your-tenant` é o seu nome de inquilino do Azure AD (contoso.com ou qualquer outro inquilino do Azure AD).

1. Abra o browser e vá para o **METADADOS** URL e procurar o **emissor** de objeto e copie o seu valor. Deve ter um aspeto semelhante ao seguinte: `https://sts.windows.net/tenant-id/`.
2. Copie e cole o valor para o **ValidTokenIssuerPrefixes** chave. Pode adicionar várias, separando-as com uma vírgula. Um exemplo disso será inserido um comentário no exemplo acima de XML.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o ficheiro de extensão de verificação

Agora, configurou sua política para que o Azure AD B2C sabe como se comunicar com o diretório do Azure AD. Tente carregar o ficheiro de extensão da sua política só para confirmar que ele não tem quaisquer problemas até agora.

1. Sobre o **políticas personalizadas** página no seu inquilino do Azure AD B2C, selecione **carregar política**.
2. Ativar **substituir a política, se existir**e, em seguida, procure e selecione o *TrustFrameworkExtensions.xml* ficheiro.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de afirmações

Neste momento, o fornecedor de identidade tiver sido configurado, mas não está disponível em qualquer uma dos ecrãs de início de sessão-inscrição/início de sessão. Para tornar disponível, pode criar um duplicado de um percurso do utilizador modelo existente e, em seguida, modificá-lo para que ele também tem o fornecedor de identidade do Azure AD.

1. Abra o *TrustFrameworkBase.xml* ficheiro do pacote de iniciante.
2. Localize e copie o conteúdo inteiro dos **UserJourney** elemento que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize a **UserJourneys** elemento. Se o elemento não existir, adicione um.
4. Colar o conteúdo inteiro do **UserJourney** elemento que copiou como subordinado da **UserJourneys** elemento.
5. Mudar o nome o ID do percurso do utilizador. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Exibir o botão

O **ClaimsProviderSelection** elemento é semelhante a um botão do fornecedor de identidade numa tela de início de sessão-inscrição/início de sessão. Se adicionar um **ClaimsProviderSelection** elemento para o Azure AD, um novo botão exibido quando um utilizador que chegam na página.

1. Encontrar o **OrchestrationStep** elemento que inclui `Order="1"` no percurso do utilizador que criou.
2. Sob **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor da **TargetClaimsExchangeId** para um valor adequado, por exemplo `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligar o botão para uma ação

Agora que tem um botão no local, terá de ligá-lo a uma ação. A ação, neste caso, é para o Azure AD B2C comunicar com o Azure AD para receber um token. Ligue o botão a uma ação, o perfil técnico de ligação para o seu fornecedor de afirmações do AD do Azure.

1. Encontrar o **OrchestrationStep** que inclua `Order="2"` no percurso do utilizador.
2. Adicione as seguintes **ClaimsExchange** elemento certificar-se de que usar o mesmo valor para **Id** que utilizou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```
    
    Atualize o valor de **TechnicalProfileReferenceId** para o **Id** do perfil técnico que criou anteriormente. Por exemplo, `Common-AAD`.

3. Guardar a *TrustFrameworkExtensions.xml* de ficheiro e carregá-lo novamente para a verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação do Azure AD B2C

Comunicação com o Azure AD B2C ocorre por meio de um aplicativo que criar no seu inquilino. Esta secção lista os passos opcionais que pode seguir para criar uma aplicação de teste, se ainda não o tiver feito.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para o aplicativo, por exemplo *testapp1*.
6. Para **aplicação Web / Web API**, selecione `Yes`e, em seguida, introduza `https://jwt.ms` para o **URL de resposta**.
7. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro da entidade confiadora de terceiros

Atualize o ficheiro da entidade confiadora de terceiros (RP) que inicia o percurso do utilizador que criou.

1. Faça uma cópia deles *SignUpOrSignIn.xml* no diretório de trabalho e renomeá-lo. Por exemplo, mude o nome para *SignUpSignContoso.xml*.
2. Abra o ficheiro novo e atualize o valor do **PolicyId** atributo **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInContoso`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Atualize o valor do **ReferenceId** atributo **DefaultUserJourney** de acordo com o ID do percurso do utilizador novo que criou (SignUpSignContoso).
5. Guardar as alterações, carregue o ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação do Azure AD B2C que criou está selecionada na **selecione aplicativo** campo e, em seguida, testá-lo ao clicar em **executar agora**.
