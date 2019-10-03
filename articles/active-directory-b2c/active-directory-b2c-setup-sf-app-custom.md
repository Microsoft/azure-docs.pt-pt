---
title: Configurar a entrada com um provedor de SAML do Salesforce usando políticas personalizadas no Azure Active Directory B2C
description: Configure a entrada com um provedor de SAML do Salesforce usando políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2922aa32bab2d9d7146a03757850d4b724ad7570
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71822256"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com um provedor de SAML do Salesforce usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma organização do Salesforce usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory B2C (Azure ad B2C). Habilite a entrada adicionando um [perfil técnico SAML](saml-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em introdução [às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se você ainda não fez isso, Inscreva-se para uma [conta gratuita do Developer Edition](https://developer.salesforce.com/signup). Este artigo usa a [experiência do Salesforce Lightning](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Configure um meu domínio](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para sua organização do Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como um provedor de identidade

1. [Entre no Salesforce](https://login.salesforce.com/).
2. No menu à esquerda, em **configurações**, expanda **identidade**e, em seguida, selecione **provedor de identidade**.
3. Selecione **habilitar provedor de identidade**.
4. Em **selecionar o certificado**, selecione o certificado que você deseja que o Salesforce use para se comunicar com Azure ad B2C. Você pode usar o certificado padrão.
5. Clique em **Guardar**.

### <a name="create-a-connected-app-in-salesforce"></a>Criar um aplicativo conectado no Salesforce

1. Na página **provedor de identidade** , selecione **os provedores de serviço agora são criados por meio de aplicativos conectados. Clique aqui.**
2. Em **informações básicas**, insira os valores necessários para seu aplicativo conectado.
3. Em **configurações do aplicativo Web**, marque a caixa **habilitar SAML** .
4. No campo **ID da entidade** , insira a URL a seguir. Certifique-se de substituir o valor por `your-tenant` pelo nome do seu locatário de Azure ad B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. No campo **URL do ACS** , insira a URL a seguir. Certifique-se de substituir o valor por `your-tenant` pelo nome do seu locatário de Azure ad B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Role até a parte inferior da lista e clique em **salvar**.

### <a name="get-the-metadata-url"></a>Obter a URL de metadados

1. Na página Visão geral do seu aplicativo conectado, clique em **gerenciar**.
2. Copie o valor do **ponto de extremidade de descoberta de metadados**e salve-o. Você o usará posteriormente neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurar usuários do Salesforce para federar

1. Na página **gerenciar** do seu aplicativo conectado, clique em **gerenciar perfis**.
2. Selecione os perfis (ou grupos de usuários) que você deseja federar com Azure AD B2C. Como administrador do sistema, marque a caixa de seleção **administrador do sistema** para que você possa federar usando sua conta do Salesforce.

## <a name="generate-a-signing-certificate"></a>Gerar um certificado de autenticação

As solicitações enviadas para o Salesforce precisam ser assinadas por Azure AD B2C. Para gerar um certificado de autenticação, abra Azure PowerShell e, em seguida, execute os comandos a seguir.

> [!NOTE]
> Verifique se você atualizou o nome do locatário e a senha nas duas primeiras linhas.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o certificado que criou em seu locatário Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Na página Visão geral, selecione **Identity Experience Framework**.
5. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções**, escolha `Upload`.
7. Introduza um **Nome** para a política. Por exemplo, SAMLSigningCert. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Navegue até e selecione o certificado B2CSigningCert. pfx que você criou.
9. Insira a **senha** para o certificado.
3. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando uma conta do Salesforce, você precisará definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta do Salesforce como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra o *TrustFrameworkExtensions. xml*.
2. Localize o elemento **ClaimsProviders** . Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **Claims** da seguinte maneira:

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
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
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

4. Atualize o valor de **PartnerEntity** com a URL de metadados do Salesforce que você copiou anteriormente.
5. Atualize o valor de ambas as instâncias de **StorageReferenceId** para o nome da chave do seu certificado de autenticação. Por exemplo, B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com sua conta do Salesforce. Tente carregar o arquivo de extensão da política apenas para confirmar que ele não tem nenhum problema até o momento.

1. Na página **políticas personalizadas** no locatário do Azure ad B2C, selecione **carregar política**.
2. Habilite **substituir a política se ela existir**e, em seguida, navegue até e selecione o arquivo *TrustFrameworkExtensions. xml* .
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi configurado, mas não está disponível em nenhuma das telas de inscrição ou de entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade do Salesforce.

1. Abra o arquivo *TrustFrameworkBase. xml* do pacote inicial.
2. Localize e copie todo o conteúdo do elemento **userjornada** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions. xml* e localize o elemento **userjornadas** . Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **userjornada** que você copiou como um filho do elemento **userjornadas** .
5. Renomeie a ID da jornada do usuário. Por exemplo, `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de provedor de identidade em uma tela de inscrição ou entrada. Se você adicionar um elemento **ClaimsProviderSelection** para uma conta do LinkedIn, um novo botão será exibido quando um usuário chegar à página.

1. Localize o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você acabou de criar.
2. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que você tem um botão em vigor, é necessário vinculá-lo a uma ação. Nesse caso, a ação é para Azure AD B2C se comunicar com uma conta do Salesforce para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` na jornada do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** , certificando-se de usar o mesmo valor para **ID** que você usou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico criado anteriormente. Por exemplo, `LinkedIn-OAUTH`.

3. Salve o arquivo *TrustFrameworkExtensions. xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com Azure AD B2C ocorre por meio de um aplicativo que você registra em seu locatário B2C. Esta seção lista as etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você acabou de criar:

1. Faça uma cópia do *SignUpOrSignIn. xml* em seu diretório de trabalho e renomeie-o. Por exemplo, renomeie-o como *SignUpSignInSalesforce. xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInSalesforce`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignInSalesforce).
5. Salve as alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se Azure AD B2C aplicativo que você criou está selecionado no campo **Selecionar aplicativo** e, em seguida, teste-o clicando em **executar agora**.
