---
title: Adicionar o ADFS como um provedor de identidade SAML usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Configurar o ADFS 2016 usando o protocolo SAML e políticas personalizadas no Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 595f8a174e615fb08a042b0e9c4cfe6da6ac1b7e
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68773429"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar o ADFS como um provedor de identidade SAML usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para uma conta de usuário do ADFS usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C. Habilite a entrada adicionando um [perfil técnico SAML](saml-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em introdução [às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Verifique se você tem acesso a um arquivo. pfx de certificado com uma chave privada. Você pode gerar seu próprio certificado assinado e carregá-lo no Azure AD B2C. Azure AD B2C usa esse certificado para assinar a solicitação SAML enviada ao seu provedor de identidade SAML.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar seu certificado em seu locatário de Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório e o filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Na página Visão geral, selecione **Identity Experience Framework**.
5. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções**, escolha `Upload`.
7. Insira um **nome** para a chave de política. Por exemplo, `SamlCert`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Navegue até e selecione o arquivo. pfx do certificado com a chave privada.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando uma conta do ADFS, você precisará definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado. 

Você pode definir uma conta do ADFS como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra o *TrustFrameworkExtensions. xml*.
2. Localize o elemento **ClaimsProviders** . Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo Claims da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

4. Substitua `your-ADFS-domain` pelo nome do seu domínio do ADFS e substitua o valor da Declaração de saída identityprovider pelo seu DNS (valor arbitrário que indica seu domínio).
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com a conta do ADFS. Tente carregar o arquivo de extensão da política apenas para confirmar que ele não tem nenhum problema até o momento.

1. Na página **políticas personalizadas** no locatário do Azure ad B2C, selecione **carregar política**.
2. Habilite **substituir a política se ela existir**e, em seguida, navegue até e selecione o arquivo *TrustFrameworkExtensions. xml* .
3. Clique em **Carregar**.

> [!NOTE]
> A extensão do Visual Studio Code B2C usa "socialIdpUserId". Uma política social também é necessária para o ADFS.
>

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi configurado, mas não está disponível em nenhuma das telas de inscrição ou de entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade do ADFS.

1. Abra o arquivo *TrustFrameworkBase. xml* do pacote inicial.
2. Localize e copie todo o conteúdo do elemento **userjornada** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions. xml* e localize o elemento userjornadas. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **userjornada** que você copiou como um filho do elemento userjornadas.
5. Renomeie a ID da jornada do usuário. Por exemplo, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de provedor de identidade em uma tela de inscrição ou entrada. Se você adicionar um elemento **ClaimsProviderSelection** para uma conta do ADFS, um novo botão será exibido quando um usuário chegar à página.

1. Localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
2. Em **ClaimsProviderSelections**, adicione o seguinte elemento. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que você tem um botão em vigor, é necessário vinculá-lo a uma ação. Nesse caso, a ação é para Azure AD B2C se comunicar com uma conta do ADFS para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` na jornada do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** , certificando-se de usar o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Atualize o valor de **TechnicalProfileReferenceId** para a ID do perfil técnico criado anteriormente. Por exemplo, `Contoso-SAML2`.

3. Salve o arquivo *TrustFrameworkExtensions. xml* e carregue-o novamente para verificação.


## <a name="configure-an-adfs-relying-party-trust"></a>Configurar um confiança de terceira parte confiável do ADFS

Para usar o ADFS como um provedor de identidade no Azure AD B2C, você precisa criar uma terceira parte confiável do ADFS com os metadados de SAML do Azure AD B2C. O exemplo a seguir mostra um endereço URL para os metadados SAML de um perfil técnico Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **seu locatário** com o nome do locatário, como Your-Tenant.onmicrosoft.com.
- **sua política** com o nome da política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome do seu perfil técnico do provedor de identidade SAML. Por exemplo, contoso-SAML2.
 
Abra um navegador e navegue até a URL. Verifique se você digitou a URL correta e se tem acesso ao arquivo de metadados XML. Para adicionar uma nova relação de confiança de terceira parte confiável usando o snap-in de gerenciamento do ADFS e definir manualmente as configurações, execute o procedimento a seguir em um servidor de Federação. A associação em **Administradores** ou equivalente no computador local é o mínimo necessário para concluir este procedimento.

1. Em Gerenciador do Servidor, selecione **ferramentas**e, em seguida, selecione **Gerenciamento do ADFS**.
2. Selecione **Adicionar confiança de terceira parte confiável**.
3. Na página de **boas-vindas** , escolha **reconhecimento de declarações**e clique em **Iniciar**.
4. Na página **selecionar fonte de dados** , selecione **importar dados sobre a publicação de terceira parte confiável online ou em uma rede local**, forneça a URL de metadados do Azure ad B2C e clique em **Avançar**.
5. Na página **especificar nome para exibição** , insira um **nome de exibição**, em **observações**, insira uma descrição para essa relação de confiança de terceira parte confiável e clique em **Avançar**.
6. Na página **escolher política de controle de acesso** , selecione uma política e clique em **Avançar**.
7. Na página **pronto para adicionar confiança** , examine as configurações e clique em **Avançar** para salvar as informações de confiança de terceira parte confiável.
8. Na página **concluir** , clique em **fechar**. essa ação exibe automaticamente a caixa de diálogo **Editar regras de declaração** .
9. Selecione **Adicionar regra**.  
10. Em **modelo de regra de declaração**, selecione **Enviar atributos LDAP como declarações**.
11. Forneça um **nome de regra de declaração**. Para o **repositório de atributos**, selecione **selecionar Active Directory**, adicione as seguintes declarações e clique em **concluir** e em **OK**.

    | Atributo LDAP | Tipo de declaração de saída |
    | -------------- | ------------------- |
    | Nome da entidade de usuário | userPrincipalName |
    | Apelido | family_name |
    | Nome fornecido | given_name |
    | Endereço de email | email |
    | Nome de exibição | name |
    
    Observe que esses nomes não serão exibidos na lista suspensa tipo de declaração de saída. Você precisa digitá-los manualmente no. (A lista suspensa é realmente editável). 
    
12.  Com base em seu tipo de certificado, talvez seja necessário definir o algoritmo de HASH. Na janela Propriedades da terceira parte confiável (demonstração do B2C), selecione a guia **avançado** , altere o **algoritmo de hash seguro** para `SHA-256`e clique em **OK**.  
13. Em Gerenciador do Servidor, selecione **ferramentas**e, em seguida, selecione **Gerenciamento do ADFS**.
14. Selecione a terceira parte confiável que você criou, selecione **Atualizar de metadados de Federação**e clique em **Atualizar**. 

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre por meio de um aplicativo que você cria em seu locatário. Esta seção lista as etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo, por exemplo, *testapp1*.
6. Para **aplicativo Web/API Web**, selecione `Yes`e, em seguida `https://jwt.ms` , insira para a **URL de resposta**.
7. Clique em **Criar**.

### <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn. xml* em seu diretório de trabalho e renomeie-o. Por exemplo, renomeie-o como *SignUpSignInADFS. xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInADFS`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignInADFS).
5. Salve as alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se Azure AD B2C aplicativo que você criou está selecionado no campo **Selecionar aplicativo** e, em seguida, teste-o clicando em **executar agora**.

