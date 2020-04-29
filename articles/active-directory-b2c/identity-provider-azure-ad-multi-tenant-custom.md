---
title: Configurar o início de inscrição para a AD Azure multi-inquilino por políticas personalizadas
titleSuffix: Azure AD B2C
description: Adicione um fornecedor de identidade Azure AD multi-inquilino usando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 320723744e1366fdc73cd0593fb0ebece03367f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678114"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o sign-in para o diretório ativo azure multi-inquilino utilizando políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para utilizadores que usam o ponto final multi-inquilino para o Azure Ative Directory (Azure AD) utilizando [políticas personalizadas](custom-policy-overview.md) no Azure AD B2C. Isto permite que os utilizadores de vários inquilinos da AD Azure assinem a utilização do Azure AD B2C, sem que tenha de configurar um fornecedor de identidade para cada inquilino. No entanto, os membros convidados de qualquer um destes inquilinos **não** poderão assinar. Para isso, é necessário [configurar individualmente cada inquilino.](identity-provider-azure-ad-single-tenant-custom.md)

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Get started com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Registar uma aplicação

Para permitir o início de sessão para utilizadores de uma organização específica da AD Azure, é necessário registar uma aplicação dentro do inquilino da AD Azure organizacional.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino ad organizacional Azure (por exemplo, contoso.com). Selecione o filtro de **subscrição Do Diretório +** no menu superior e, em seguida, escolha o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua candidatura. Por exemplo, `Azure AD B2C App`.
1. Selecione **Contas em qualquer diretório organizacional** para esta aplicação.
1. Para o **Redirect URI,** aceite o valor da **Web**e introduza `your-B2C-tenant-name` o seguinte URL em todas as letras minúsculas, onde é substituído pelo nome do seu inquilino Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecione **Registar**. Grave o ID de **Aplicação (cliente)** para utilização num passo posterior.
1. Selecione **Certificados & segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Introduza uma **Descrição** para o segredo, selecione uma expiração e, em seguida, **selecione Adicionar**. Grave o **valor** do segredo para uso num passo posterior.

## <a name="configuring-optional-claims"></a>Configurar reclamações opcionais

Se quiser obter `family_name` as `given_name` reclamações e reclamações da Azure AD, pode configurar reclamações opcionais para a sua aplicação no portal Azure UI ou manifesto de aplicação. Para mais informações, consulte [Como fornecer reclamações opcionais à sua aplicação Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione registos de **Aplicações**.
1. Selecione a aplicação que pretende configurar reclamações opcionais na lista.
1. A partir da secção **Gerir,** selecione **configuração Token**.
1. **Selecione Adicionar reclamação opcional**.
1. Para o **tipo Token,** selecione **ID**.
1. Selecione as reclamações `given_name`opcionais a adicionar e `family_name` .
1. Clique em **Adicionar**.

## <a name="create-a-policy-key"></a>Criar uma chave política

Você precisa armazenar a chave de aplicação que criou no seu inquilino Azure AD B2C.

1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, escolha o diretório que contém o seu inquilino Azure AD AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. No âmbito **das Políticas,** selecione Quadro de **Experiência de Identidade**.
1. Selecione **teclas Policy** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha. `Manual`
1. Introduza um **nome** para a chave política. Por exemplo, `AADAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando é criado, pelo que a sua referência no XML na secção seguinte é *B2C_1A_AADAppSecret*.
1. Em **Segredo,** insira o seu segredo de cliente que gravou anteriormente.
1. Para **a utilização da chave,** selecione `Signature`.
1. Selecione **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se pretender que os utilizadores assinem utilizando o Azure AD, tem de definir o Azure AD como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são utilizadas pelo Azure AD B2C para verificar se um utilizador específico se autenticou.

Pode definir a AD Azure como um fornecedor de sinistros adicionando AD Azure ao elemento **ClaimsProvider** no ficheiro de extensão da sua apólice.

1. Abra o ficheiro *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo Fornecedor de **Reclamações** da seguinte forma:

    ```XML
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

1. Sob o elemento **ClaimsProvider,** atualize o valor do **Domínio** para um valor único que pode ser usado para distingui-lo de outros fornecedores de identidade.
1. Sob o elemento **Perfil Técnico,** atualize o `Contoso Employee`valor para **DisplayName**, por exemplo, . Este valor é apresentado no botão de início de sessão na sua página de início de sessão.
1. **Deteto client_id** para a identificação de candidatura do pedido de multi-inquilino sinuoso da AD Azure que registou anteriormente.
1. Em **CryptographicKeys, atualize**o valor do **StorageReferenceId** para o nome da chave de política que criou anteriormente. Por exemplo, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Restringir o acesso

> [!NOTE]
> A `https://login.microsoftonline.com/` utilização como valor para **ValidatokenIssuerPrefixes** permite que todos os utilizadores de AD Azure inscrevam na sua aplicação.

Você precisa atualizar a lista de emitentes de token válidos e restringir o acesso a uma lista específica de utilizadores de inquilinos DaD Azure que podem iniciar sessão.

Para obter os valores, veja os metadados de descoberta do OpenID Connect para cada um dos inquilinos da AD Azure que gostaria de ter os utilizadores a iniciar em sessão. O formato do URL dos `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`metadados é semelhante ao, onde `your-tenant` está o seu nome de inquilino Azure AD. Por exemplo:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Execute estes passos para cada inquilino da AD Azure que deve ser usado para iniciar sessão:

1. Abra o seu navegador e vá ao URL de metadados OpenID Connect para o inquilino. Encontre o objeto **emitente** e grave o seu valor. Deve parecer semelhante `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`a.
1. Copiar e colar o valor na chave **ValidTokenIssuerPrefixes.** Separe vários emitentes com uma vírem. Um exemplo com dois emitentes `ClaimsProvider` aparece na amostra xml anterior.

### <a name="upload-the-extension-file-for-verification"></a>Faça upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com os teus diretórios da AD Azure. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **Políticas Personalizadas** do seu inquilino Azure AD B2C, selecione **Política de Upload**.
2. Ativar **a sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Selecione **Upload**.

## <a name="register-the-claims-provider"></a>Registe o prestador de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição/inscrição. Para disponibilizá-lo, cria-se uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modificá-la de modo a que também tenha o fornecedor de identidade Azure AD.

1. Abra o ficheiro *TrustFrameworkBase.xml* a partir do pacote de arranque.
2. Encontre e copie todo o conteúdo `Id="SignUpOrSignIn"`do elemento **UserJourney** que inclui .
3. Abra as *Extensões TrustFramework.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Colhe todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem do utilizador. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição/inscrição. Se adicionar um elemento **ClaimsProviderSelection** para AD Azure, aparece um novo botão quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na viagem de utilizador que criou em *TrustFrameworkExtensions.xml*.
1. Em **ClaimsProviderSelects,** adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para `AzureADExchange`um valor adequado, por exemplo:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de ligá-lo a uma ação. A ação, neste caso, é que o Azure AD B2C comunique com a Azure AD para receber um símbolo. Ligue o botão a uma ação ligando o perfil técnico para o seu fornecedor de reclamações da AD Azure.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para **id** que utilizou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para o **ID** do perfil técnico que criou anteriormente. Por exemplo, `Common-AAD`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que se regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que depende

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou:

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho e mude o nome. Por exemplo, mude o nome para *SignUpSignContoso.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInContoso`.
1. Atualizar o valor da **PublicPolicyUri** com o URI para a política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da viagem de utilizador que criou anteriormente. Por exemplo, *SignUpSignInContoso*.
1. Guarde as suas alterações e carregue o ficheiro.
1. De acordo com **as políticas personalizadas,** selecione a nova política na lista.
1. Na **desistência da aplicação Select,** selecione a aplicação Azure AD B2C que criou anteriormente. Por exemplo, *testapp1*.
1. Copie o **ponto final do Run agora** e abra-o numa janela de navegador privado, por exemplo, modo Incógnito no Google Chrome ou numa janela InPrivate no Microsoft Edge. A abertura numa janela de navegador privado permite-lhe testar a viagem completa do utilizador, não utilizando quaisquer credenciais atuais do Azure AD.
1. Selecione o sinal de AD Azure no botão, por exemplo, *Empregado da Contoso,* e, em seguida, insira as credenciais para um utilizador num dos seus inquilinos organizacionais Azure AD. É-lhe pedido que autorize o pedido e, em seguida, insira informações para o seu perfil.

Se o sinal em processo for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms`, que exibe o conteúdo do token devolvido pelo Azure AD B2C.

Para testar a capacidade de entrada de multi-inquilinos, execute os dois últimos passos usando as credenciais para um utilizador que existe outro inquilino da AD Azure.

## <a name="next-steps"></a>Passos seguintes

Ao trabalhar com políticas personalizadas, pode por vezes precisar de informações adicionais para resolver uma política durante o seu desenvolvimento.

Para ajudar a diagnosticar problemas, pode colocar temporariamente a apólice em "modo de desenvolvimento" e recolher registos com insights de aplicação do Azure. Saiba como em [Azure Ative Directory B2C: Collecting Logs](troubleshoot-with-application-insights.md).
