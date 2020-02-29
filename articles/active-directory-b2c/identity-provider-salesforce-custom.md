---
title: Configurar o início de sessão com um fornecedor SAML Salesforce utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Instale o início de sessão com um fornecedor SAML Salesforce utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67fe9ef4ad2b025d11f88976973658c9cd8ae693
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187955"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com um fornecedor SAML Salesforce utilizando políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para utilizadores de uma organização salesforce utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C). Habilita o início de sessão adicionando um [perfil técnico SAML](saml-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Get started com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).
- Se ainda não o fez, inscreva-se numa conta gratuita da [Developer Edition](https://developer.salesforce.com/signup). Este artigo utiliza a [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Cria um My Domain](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para a tua organização Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Criar a Salesforce como fornecedor de identidade

1. [Inscreva-se na Salesforce.](https://login.salesforce.com/)
2. No menu esquerdo, em **Definições,** expandir **identidade,** e, em seguida, selecionar **Fornecedor de Identidade**.
3. Selecione **Ativar fornecedor de identidade**.
4. Em **selecione o certificado,** selecione o certificado que pretende que a Salesforce utilize para comunicar com o Azure AD B2C. Pode utilizar o certificado predefinido.
5. Clique em **Guardar**.

### <a name="create-a-connected-app-in-salesforce"></a>Criar uma app conectada no Salesforce

1. Na página do **Fornecedor de Identidade,** os Fornecedores de **Serviços selecionados são agora criados através de Aplicações Conectadas. Clique aqui.**
2. Em **Informações Básicas,** introduza os valores necessários para a sua aplicação conectada.
3. Em definições de **aplicações web,** verifique a caixa **Ativas SAML.**
4. No campo **ID da Entidade,** introduza o seguinte URL. Certifique-se de que substitui o valor por `your-tenant` pelo nome do seu inquilino Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. No campo **URL ACS,** introduza o seguinte URL. Certifique-se de que substitui o valor por `your-tenant` pelo nome do seu inquilino Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Percorra para a parte inferior da lista e, em seguida, clique em **Guardar**.

### <a name="get-the-metadata-url"></a>Obtenha o URL dos metadados

1. Na página geral da sua aplicação conectada, clique em **Gerir**.
2. Copie o valor para **Metadados Discovery Endpoint**e, em seguida, guarde-o. Vaiusá-lo mais tarde neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurar utilizadores da Salesforce para federar

1. Na página **'Gerir'** da sua aplicação conectada, clique em **Gerir Perfis**.
2. Selecione os perfis (ou grupos de utilizadores) que pretende federar com o Azure AD B2C. Como administrador do sistema, selecione a caixa de verificação do Administrador do **Sistema,** para que possa federar utilizando a sua conta Salesforce.

## <a name="generate-a-signing-certificate"></a>Gerar um certificado de assinatura

Os pedidos enviados à Salesforce têm de ser assinados pela Azure AD B2C. Para gerar um certificado de assinatura, abra o Azure PowerShell e, em seguida, executar os seguintes comandos.

> [!NOTE]
> Certifique-se de que atualiza o nome do inquilino e a palavra-passe nas duas primeiras linhas.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Criar uma chave política

Você precisa armazenar o certificado que criou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
5. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **opções,** escolha `Upload`.
7. Introduza um **Nome** para a política. Por exemplo, SAMLSigningCert. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Navegue e selecione o certificado B2CSigningCert.pfx que criou.
9. Introduza a **Palavra-passe** para o certificado.
3. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se pretender que os utilizadores assinem através de uma conta Salesforce, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são utilizadas pelo Azure AD B2C para verificar se um utilizador específico se autenticou.

Pode definir uma conta Salesforce como fornecedor de sinistros adicionando-a ao elemento **Reclamadores** no ficheiro de extensão da sua apólice. Para mais informações, consulte [definir um perfil técnico SAML](saml-technical-profile.md).

1. Abra as *Extensões TrustFramework.xml*.
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo Fornecedor de **Reclamações** da seguinte forma:

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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Atualize o valor da **PartnerEntity** com o URL de metadados Salesforce que copiou anteriormente.
1. Atualize o valor de ambas as instâncias do **StorageReferenceId** para o nome da chave do seu certificado de assinatura. Por exemplo, B2C_1A_SAMLSigningCert.
1. Localize a secção `<ClaimsProviders>` e adicione o seguinte corte XML. Se a sua política já contiver o perfil técnico `SM-Saml-idp`, salte para o próximo passo. Para mais informações, consulte [a gestão da sessão de inscrição única](custom-policy-reference-sso.md).

    ```XML
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

### <a name="upload-the-extension-file-for-verification"></a>Faça upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a sua política para que o Azure AD B2C saiba comunicar com a sua conta Salesforce. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **Políticas Personalizadas** do seu inquilino Azure AD B2C, selecione **Política de Upload**.
2. Ativar **a sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registe o prestador de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição ou inscrição. Para disponibilizá-lo, cria-se uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modificá-la de modo a que também tenha o fornecedor de identidade Salesforce.

1. Abra o ficheiro *TrustFrameworkBase.xml* a partir do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclua `Id="SignUpOrSignIn"`.
3. Abra as *Extensões TrustFramework.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Colhe todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem do utilizador. Por exemplo, `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição ou de inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta LinkedIn, aparece um novo botão quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na viagem de utilizador que acabou de criar.
2. Em **ClaimsProviderSelects,** adicione o seguinte elemento. Defino o valor do **TargetClaimsExchangeId** para um valor adequado, por exemplo, `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de ligá-lo a uma ação. Neste caso, a ação é que o Azure AD B2C comunique com uma conta Salesforce para receber um símbolo.

1. Encontre a **OrquestrationStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para **id** que utilizou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para o **ID** do perfil técnico que criou anteriormente. Por exemplo, `LinkedIn-OAUTH`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que se regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que depende

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que acabou de criar:

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho e mude o nome. Por exemplo, mude o nome para *SignUpSignInSalesforce.xml*.
2. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInSalesforce`.
3. Atualizar o valor da **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova viagem de utilizador que criou (SignUpSignInSalesforce).
5. Guarde as suas alterações, faça o upload do ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação Azure AD B2C que criou é selecionada no campo de **aplicação Select** e, em seguida, testá-la clicando agora em **Executar**.
