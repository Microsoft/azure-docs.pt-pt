---
title: Configurar o início de sessão com um provedor de SAML do Salesforce ao utilizar políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Configure o início de sessão com um provedor de SAML do Salesforce ao utilizar políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e565822c006191615dbc10b980da24dcd9ed787a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508309"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com um provedor de SAML do Salesforce ao utilizar políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para os utilizadores de uma organização de Salesforce utilizarem [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C. Ativar início de sessão adicionando um [perfil técnico SAML](saml-technical-profile.md) para uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua os passos na [introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se ainda não o fez, inscreva-se para uma [Developer Edition conta gratuita](https://developer.salesforce.com/signup). Este artigo utiliza a [Salesforce extremamente experiência](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Configurar um domínio My](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para a sua organização do Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como um fornecedor de identidade

1. [Inicie sessão no Salesforce](https://login.salesforce.com/). 
2. No menu da esquerda, em **configurações**, expanda **identidade**e, em seguida, selecione **fornecedor de identidade**.
3. Selecione **ativar o fornecedor de identidade**.
4. Sob **selecionar o certificado**, selecione o certificado que pretende que o Salesforce para utilizar para comunicar com o Azure AD B2C. Pode utilizar o certificado predefinido. 
5. Clique em **Guardar**. 

### <a name="create-a-connected-app-in-salesforce"></a>Criar uma aplicação ligada no Salesforce

1. Sobre o **fornecedor de identidade** página, selecione **fornecedores de serviços agora são criados através de aplicações ligadas. Clique aqui.**
2. Sob **informações básicas**, introduza os valores necessários para a sua aplicação ligada.
3. Sob **definições da aplicação Web**, verifique o **ativar SAML** caixa.
4. Na **ID de entidade** , insira o seguinte URL. Certifique-se de que substitui o valor `your-tenant` com o nome do seu inquilino do Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Na **URL de ACS** , insira o seguinte URL. Certifique-se de que substitui o valor `your-tenant` com o nome do seu inquilino do Azure AD B2C.
      
      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Desloque-se na parte inferior da lista e, em seguida, clique em **guardar**.

### <a name="get-the-metadata-url"></a>Obter o URL de metadados

1. Na página de descrição geral da sua aplicação ligada, clique em **gerir**.
2. Copie o valor para **ponto final de deteção de metadados**e, em seguida, guarde-o. Irá utilizá-lo neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurar utilizadores do Salesforce para federar

1. Sobre o **gerir** página da sua aplicação ligada, clique em **Spravovat Profily**.
2. Selecione os perfis (ou grupos de utilizadores) que pretende federar com o Azure AD B2C. Como administrador do sistema, selecione o **administrador de sistema** caixa de verificação para que pode federar usando sua conta do Salesforce.

## <a name="generate-a-signing-certificate"></a>Gerar um certificado de assinatura

Pedidos enviados para o Salesforce tem de ser assinado pelo Azure AD B2C. Para gerar um certificado de assinatura, abra o Azure PowerShell e, em seguida, execute os seguintes comandos.

> [!NOTE]
> Certifique-se de que Atualize o nome de inquilino e a palavra-passe nas linhas duas principais.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Criar uma chave de política

Tem de armazenar o certificado que criou no seu inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Na página de descrição geral, selecione **arquitetura de experiências de identidade - pré-visualização**.
5. Selecione **chaves de política** e, em seguida, selecione **Add**.
6. Para **opções**, escolha `Upload`.
7. Introduza um **Nome** para a política. Por exemplo, SAMLSigningCert. O prefixo `B2C_1A_` é adicionado automaticamente para o nome da sua chave.
8. Procure e selecione o certificado de B2CSigningCert.pfx que criou. 
9. Introduza o **palavra-passe** para o certificado.
3. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um fornecedor de afirmações

Se pretender que os utilizadores iniciem sessão com uma conta do Salesforce, terá de definir a conta como um fornecedor de afirmações com Azure AD B2C possam comunicar através de um ponto de extremidade. O ponto final fornece um conjunto de afirmações que são utilizadas pelo Azure AD B2C para verificar que um utilizador específico foi autenticado. 

Pode definir uma conta do Salesforce como um fornecedor de afirmações ao adicioná-la para o **ClaimsProviders** elemento no arquivo de extensão da sua política.

1. Abra o *TrustFrameworkExtensions.xml*.
2. Encontrar o **ClaimsProviders** elemento. Se não existir, adicione-o sob o elemento raiz.
3. Adicionar um novo **ClaimsProvider** da seguinte forma:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Atualize o valor de **PartnerEntity** com o URL de metadados do Salesforce que copiou anteriormente.
5. Atualize o valor de ambas as instâncias de **StorageReferenceId** para o nome da chave do seu certificado de assinatura. Por exemplo, B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o ficheiro de extensão de verificação

Agora, configurou sua política para que o Azure AD B2C sabe como se comunicar com a sua conta do Salesforce. Tente carregar o ficheiro de extensão da sua política só para confirmar que ele não tem quaisquer problemas até agora.

1. Sobre o **políticas personalizadas** página no seu inquilino do Azure AD B2C, selecione **carregar política**.
2. Ativar **substituir a política, se existir**e, em seguida, procure e selecione o *TrustFrameworkExtensions.xml* ficheiro.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de afirmações

Neste momento, o fornecedor de identidade tiver sido configurado, mas não está disponível em qualquer um dos ecrãs de inscrição ou início de sessão. Para disponibilizá-lo, pode criar um duplicado de um percurso do utilizador modelo existente e, em seguida, modificá-lo para que ele também tem o fornecedor de identidade do Salesforce.

1. Abra o *TrustFrameworkBase.xml* ficheiro do pacote de iniciante.
2. Localize e copie o conteúdo inteiro dos **UserJourney** elemento que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize a **UserJourneys** elemento. Se o elemento não existir, adicione um.
4. Colar o conteúdo inteiro do **UserJourney** elemento que copiou como subordinado da **UserJourneys** elemento.
5. Mudar o nome o ID do percurso do utilizador. Por exemplo, `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Exibir o botão

O **ClaimsProviderSelection** elemento é semelhante a um botão do fornecedor de identidade numa tela de inscrição ou início de sessão. Se adicionar um **ClaimsProviderSelection** elemento de uma conta do LinkedIn, um novo botão exibido quando um utilizador que chegam na página.

1. Encontrar o **OrchestrationStep** elemento que inclui `Order="1"` no percurso do utilizador que acabou de criar.
2. Sob **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor da **TargetClaimsExchangeId** para um valor adequado, por exemplo `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligar o botão para uma ação

Agora que tem um botão no local, terá de ligá-lo a uma ação. A ação, neste caso, é para o Azure AD B2C comunicar com uma conta do Salesforce para receber um token.

1. Encontrar o **OrchestrationStep** que inclua `Order="2"` no percurso do utilizador.
2. Adicione as seguintes **ClaimsExchange** elemento certificar-se de que usar o mesmo valor para **Id** que utilizou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```
    
    Atualize o valor de **TechnicalProfileReferenceId** para o **Id** do perfil técnico que criou anteriormente. Por exemplo, `LinkedIn-OAUTH`.

3. Guardar a *TrustFrameworkExtensions.xml* de ficheiro e carregá-lo novamente para a verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação do Azure AD B2C

Comunicação com o Azure AD B2c ocorre por meio de um aplicativo que criar no seu inquilino. Esta secção lista os passos opcionais que pode seguir para criar uma aplicação de teste, se ainda não o tiver feito.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para o aplicativo, por exemplo *testapp1*.
6. Para **aplicação Web / Web API**, selecione `Yes`e, em seguida, introduza `https://jwt.ms` para o **URL de resposta**.
7. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro da entidade confiadora de terceiros

Atualize o ficheiro da entidade confiadora de terceiros (RP) que inicia o percurso do utilizador que acabou de criar:

1. Faça uma cópia deles *SignUpOrSignIn.xml* no diretório de trabalho e renomeá-lo. Por exemplo, mude o nome para *SignUpSignInSalesforce.xml*.
2. Abra o ficheiro novo e atualize o valor do **PolicyId** atributo **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInSalesforce`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Atualize o valor do **ReferenceId** atributo **DefaultUserJourney** de acordo com o ID do percurso do utilizador novo que criou (SignUpSignInSalesforce).
5. Guardar as alterações, carregue o ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação do Azure AD B2C que criou está selecionada na **selecione aplicativo** campo e, em seguida, testá-lo ao clicar em **executar agora**.
