---
title: Configurar o sedutar para multi-inquilino Azure AD por políticas personalizadas
titleSuffix: Azure AD B2C
description: Adicione um fornecedor de identidade Azure AD multi-inquilino usando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 645a0d21fc25cb45914eed02e023a0076c457ffb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87116298"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o sº de inscrição para o Diretório Ativo Azure multi-inquilino utilizando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como permitir o início de sedutar para utilizadores que usam o ponto final de multi-inquilino para Azure Ative Directory (Azure AD) utilizando [políticas personalizadas](custom-policy-overview.md) em Azure AD B2C. Isto permite que os utilizadores de vários inquilinos da AZure AD assinem usando o Azure AD B2C, sem que tenha de configurar um fornecedor de identidade para cada inquilino. No entanto, os membros convidados de qualquer um destes inquilinos **não** poderão assinar. Para isso, é necessário [configurar individualmente cada inquilino.](identity-provider-azure-ad-single-tenant-custom.md)

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Começar com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Registar uma aplicação

Para ativar o sôm-in para utilizadores de uma organização específica da Azure AD, você precisa registrar uma aplicação dentro do inquilino organizacional Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino organizacional Azure AD (por exemplo, contoso.com). Selecione o **filtro de subscrição Diretório +** no menu superior e, em seguida, escolha o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Por exemplo, `Azure AD B2C App`.
1. Selecione **Contas em qualquer diretório organizacional** para esta aplicação.
1. Para o **Redirect URI,** aceite o valor da **Web,** e introduza o seguinte URL em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu inquilino Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecione **Registar**. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.
1. Selecione **Certificados & segredos**e, em seguida, selecione **Novo segredo de cliente**.
1. Introduza uma **Descrição** para o segredo, selecione uma expiração e, em seguida, **selecione Adicionar**. Grave o **valor** do segredo para utilização num passo posterior.

## <a name="configuring-optional-claims"></a>Configurar pedidos opcionais

Se quiser obter as `family_name` `given_name` reclamações da Azure AD, pode configurar reclamações opcionais para a sua aplicação no portal Azure UI ou manifesto de aplicação. Para mais informações, consulte [Como fornecer reclamações opcionais à sua aplicação AD AZure.](../active-directory/develop/active-directory-optional-claims.md)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione **registos de Aplicações.**
1. Selecione a aplicação para a inscrição que pretende configurar pedidos opcionais na lista.
1. A partir da secção **Gerir,** selecione **a configuração Token**.
1. **Selecione Adicionar reclamação opcional**.
1. Para o **tipo Token**, selecione **ID**.
1. Selecione as reclamações opcionais a adicionar `family_name` e `given_name` .
1. Clique em **Adicionar**.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave de aplicação que você criou no seu inquilino Azure AD B2C.

1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o **filtro de subscrição Diretório +** no menu superior e, em seguida, escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **as teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Manual` .
1. Insira um **Nome** para a chave de política. Por exemplo, `AADAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando é criado, pelo que a sua referência no XML na secção seguinte é *B2C_1A_AADAppSecret*.
1. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
1. Para **a utilização da chave**, selecione `Signature` .
1. Selecione **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se quiser que os utilizadores entrem em súb9 utilizando o Azure AD, tem de definir a AZure AD como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir a Azure AD como fornecedor de sinistros adicionando Azure AD ao elemento **ClaimsProvider** no ficheiro de extensão da sua política.

1. Abra o ficheiro *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Sob o elemento **ClaimsProvider,** atualize o valor **do Domínio** para um valor único que possa ser usado para distingui-lo de outros fornecedores de identidade.
1. Sob o elemento **TécnicoProfile,** atualize o valor **do DisplayName,** por exemplo, `Contoso Employee` . Este valor é apresentado no botão de início de sposição na sua página de início de sposição.
1. Defina **client_id** para o ID de aplicação da aplicação Azure AD multi-inquilino que registou anteriormente.
1. Em **CryptographicKeys,** atualize o valor do **StorageReferenceId** para o nome da chave de política criada anteriormente. Por exemplo, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Restringir o acesso

> [!NOTE]
> A utilização `https://login.microsoftonline.com/` como valor para **ValidTokenIssuerPrefixes** permite que todos os utilizadores AZure AD entrem na sua aplicação.

Você precisa atualizar a lista de emitentes de token válidos e restringir o acesso a uma lista específica de utilizadores inquilinos AZure AD que podem iniciar sação.

Para obter os valores, veja os metadados de descoberta OpenID Connect para cada um dos inquilinos AD Azure de que gostaria que os utilizadores iniciassem súpido. O formato do URL de metadados é semelhante a `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` , onde está o seu nome de inquilino `your-tenant` AZURE AD. Por exemplo:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Execute estes passos para cada inquilino AD Azure que deve ser usado para iniciar sedura:

1. Abra o seu navegador e vá ao URL de metadados OpenID Connect para o inquilino. Encontre o objeto **emitente** e registem o seu valor. Deve parecer-se `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` com.
1. Copie e cole o valor na tecla **ValidTokenIssuerPrefixes.** Separe vários emitentes com uma vírgula. Um exemplo com dois emitentes aparece na `ClaimsProvider` amostra XML anterior.

### <a name="upload-the-extension-file-for-verification"></a>Faça o upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com os teus diretórios AD Azure. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
2. Ativar **a política em caso de existência**e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Selecione **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição/inscrição. Para disponibilizá-lo, cria uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifica-a de modo a que também tenha o fornecedor de identidade AZure AD.

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
3. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição/inscrição. Se adicionar um elemento **ClaimsProviderSelection** para Azure AD, um novo botão aparece quando um utilizador pousa na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que criou em *TrustFrameworkExtensions.xml*.
1. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `AzureADExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, é que a Azure AD B2C comunique com a Azure AD para receber um token. Ligue o botão a uma ação ligando o perfil técnico do seu fornecedor de reclamações Azure AD.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para **ID** que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Atualize o valor do **TechnicalProfileReferenceD** para o **ID** do perfil técnico que criou anteriormente. Por exemplo, `Common-AAD`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação AZure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do grupo de cdações (RP) que inicia a jornada do utilizador que criou:

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignContoso.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInContoso`.
1. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da jornada do utilizador que criou anteriormente. Por exemplo, *SignUpSignInContoso*.
1. Guarde as suas alterações e carrete o ficheiro.
1. A partir das **políticas personalizadas carregadas,** selecione a política recém-criada da lista.
1. No drop-down da **aplicação Select,** selecione a aplicação Azure AD B2C que criou anteriormente. Por exemplo, *testapp1*.
1. Copie o **ponto final do Run e** abra-o numa janela de navegador privado, por exemplo, Modo Incógnito no Google Chrome ou uma janela InPrivate no Microsoft Edge. A abertura numa janela de navegador privada permite-lhe testar toda a viagem do utilizador não utilizando quaisquer credenciais AZure AD atualmente em cache.
1. Selecione o sinal AZure AD no botão, por exemplo, *Empregado contoso,* e, em seguida, insira as credenciais para um utilizador em um dos seus inquilinos organizacionais AZure AD. É-lhe pedido que autorize o pedido e, em seguida, introduza informações para o seu perfil.

Se o sinal em processo for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

Para testar a capacidade de inscrição de vários inquilinos, execute os dois últimos passos usando as credenciais para um utilizador que existe outro inquilino AD AZure.

## <a name="next-steps"></a>Passos seguintes

Ao trabalhar com políticas personalizadas, pode por vezes precisar de informações adicionais quando se trata de resolver problemas de uma política durante o seu desenvolvimento.

Para ajudar a diagnosticar problemas, pode colocar temporariamente a política em "modo de desenvolvimento" e recolher registos com a Azure Application Insights. Descubra como em [Azure Ative Directory B2C: Recolha de registos](troubleshoot-with-application-insights.md).
