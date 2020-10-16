---
title: Configurar o sº de saúde com um fornecedor de SAML Salesforce utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Instale o súb insusimento com um fornecedor de SAML Salesforce utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b709293360dfaae8b7aa694442e44e9e4ab8a019
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92054683"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o sôm-in com um fornecedor de SAML salesforce utilizando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de saúde dos utilizadores de uma organização salesforce utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C). Você ativa o sessão adicionando um perfil técnico do fornecedor de [identidade SAML](saml-identity-provider-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Começar com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).
- Se ainda não o fez, inscreva-se numa [conta gratuita da Developer Edition.](https://developer.salesforce.com/signup) Este artigo utiliza a [Experiência Relâmpago Salesforce.](https://developer.salesforce.com/page/Lightning_Experience_FAQ)
- [Crie um My Domain](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para a sua organização Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Criar a Salesforce como fornecedor de identidade

1. [Inscreva-se na Salesforce.](https://login.salesforce.com/)
2. No menu esquerdo, em **Definições,** expandir **identidade**e, em seguida, selecionar **Fornecedor de Identidade**.
3. Selecione **Enable Identity Provider**.
4. Em **Seleção do certificado**, selecione o certificado que pretende que a Salesforce utilize para comunicar com a Azure AD B2C. Pode utilizar o certificado predefinido.
5. Clique em **Guardar**.

### <a name="create-a-connected-app-in-salesforce"></a>Criar uma aplicação conectada no Salesforce

1. Na página **Fornecedor de Identidade,** os **Fornecedores de Serviços selecionados são agora criados através de Aplicações Conectadas.**
2. Em **Informação Básica,** insira os valores necessários para a sua aplicação conectada.
3. Nas **Definições de Aplicações Web,** verifique a caixa **Ativar SAML.**
4. No campo ID da **Entidade,** insira o seguinte URL. Certifique-se de que substitui o valor pelo `your-tenant` nome do seu inquilino Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. No campo **URL ACS,** insira o seguinte URL. Certifique-se de que substitui o valor pelo `your-tenant` nome do seu inquilino Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Percorra para a parte inferior da lista e, em seguida, clique em **Guardar**.

### <a name="get-the-metadata-url"></a>Obtenha o URL de metadados

1. Na página geral da sua aplicação conectada, clique em **Gerir**.
2. Copie o valor para **Metadata Discovery Endpoint**e guarde-o. Vai usá-lo mais tarde neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Criar utilizadores da Salesforce para federar

1. Na página **'Gerir'** da sua aplicação conectada, clique em **Gerir Perfis.**
2. Selecione os perfis (ou grupos de utilizadores) que pretende federar com Azure AD B2C. Como administrador de sistema, selecione a caixa de verificação **do Administrador do Sistema,** para que possa federar utilizando a sua conta Salesforce.

## <a name="generate-a-signing-certificate"></a>Gerar um certificado de assinatura

Os pedidos enviados à Salesforce têm de ser assinados pela Azure AD B2C. Para gerar um certificado de assinatura, abra a Azure PowerShell e, em seguida, execute os seguintes comandos.

> [!NOTE]
> Certifique-se de atualizar o nome do inquilino e a palavra-passe nas duas primeiras linhas.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o certificado que você criou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione **Identity Experience Framework**.
5. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha `Upload` .
7. Introduza um **Nome** para a política. Por exemplo, SAMLSigningCert. O prefixo `B2C_1A_` é automaticamente adicionado ao nome da sua chave.
8. Navegue e selecione o certificado B2CSigningCert.pfx que criou.
9. Introduza a **Palavra-passe** para o certificado.
3. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se pretender que os utilizadores assinem através de uma conta Salesforce, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir uma conta Salesforce como fornecedor de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política. Para obter mais informações, consulte [definir um perfil técnico do fornecedor de identidade SAML.](saml-identity-provider-technical-profile.md)

1. Abra a *TrustFrameworkExtensions.xml. *
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Atualize o valor da **Entidade Parceira** com o URL de metadados salesforce que copiou anteriormente.
1. Atualize o valor de ambas as instâncias de **StorageReferenceId** para o nome da chave do seu certificado de assinatura. Por exemplo, B2C_1A_SAMLSigningCert.
1. Localize a `<ClaimsProviders>` secção e adicione o seguinte corte XML. Se a sua apólice já contiver o `SM-Saml-idp` perfil técnico, salte para o passo seguinte. Para obter mais informações, consulte [a gestão de sessão de sessão de inscrição única.](custom-policy-reference-sso.md)

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Faça o upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com a tua conta Salesforce. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
2. Ativar **a política em caso de existência**e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição ou inscrição. Para disponibilizá-lo, cria uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifica-a de modo a que também tenha o fornecedor de identidade Salesforce.

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
3. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição ou de inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta LinkedIn, um novo botão aparece quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que acabou de criar.
2. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `SalesforceExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, é que a Azure AD B2C comunique com uma conta Salesforce para receber um token.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para **iD** que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Atualizar o valor do **TécnicoProfileReferenceD** para o **ID** do perfil técnico que criou anteriormente. Por exemplo, `salesforce` ou `LinkedIn-OAUTH`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação AZure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do grupo de cdações (RP) que inicia a jornada do utilizador que acabou de criar:

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignInSalesforce.xml*.
2. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInSalesforce`.
3. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova jornada de utilizador que criou (SignUpSignInSalesforce).
5. Guarde as suas alterações, carre fique no upload do ficheiro e, em seguida, selecione a nova política da lista.
6. Certifique-se de que a aplicação AD B2C AD que criou está selecionada no campo **de aplicações Select** e, em seguida, testá-la clicando **em Executar agora**.
