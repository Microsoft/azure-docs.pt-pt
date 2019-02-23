---
title: Adicionar o ADFS como um fornecedor de identidade com as políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Configurar 2016 AD FS com o protocolo SAML e as políticas personalizadas no Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b76162606c4d21cb6979d410fbaaaafc539cf403
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668659"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar o ADFS como um fornecedor de identidade com as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para uma conta de utilizador do AD FS, utilizando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C. Ativar início de sessão adicionando um [perfil técnico SAML](saml-technical-profile.md) para uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua os passos na [introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Certifique-se de que tem acesso a um ficheiro de certificado. pfx com uma chave privada. Pode gerar seu próprio certificado assinado e carregue-o para o Azure AD B2C. O Azure AD B2C utiliza este certificado para assinar o pedido SAML enviado para o fornecedor de identidade SAML.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Precisa armazenar o certificado no seu inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Na página de descrição geral, selecione **arquitetura de experiências de identidade - pré-visualização**.
5. Selecione **chaves de política** e, em seguida, selecione **Add**.
6. Para **opções**, escolha `Upload`.
7. Introduza um **nome** para a chave de política. Por exemplo, `SamlCert`. O prefixo `B2C_1A_` é adicionado automaticamente o nome da sua chave.
8. Procure e selecione o ficheiro. pfx de certificado com a chave privada.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um fornecedor de afirmações

Se pretender que os utilizadores iniciem sessão com uma conta do AD FS, terá de definir a conta como um fornecedor de afirmações com Azure AD B2C possam comunicar através de um ponto de extremidade. O ponto final fornece um conjunto de afirmações que são utilizadas pelo Azure AD B2C para verificar que um utilizador específico foi autenticado. 

Pode definir uma conta ADFS como um fornecedor de afirmações ao adicioná-la para o **ClaimsProviders** elemento no arquivo de extensão da sua política.

1. Abra o *TrustFrameworkExtensions.xml*.
2. Encontrar o **ClaimsProviders** elemento. Se não existir, adicione-o sob o elemento raiz.
3. Adicionar um novo **ClaimsProvider** da seguinte forma:

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
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
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

4. Substitua `your-ADFS-domain` com o nome do seu domínio do AD FS e substitua o valor da **identityProvider** afirmações de saída com o seu DNS (valor arbitrário que indica o seu domínio).
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o ficheiro de extensão de verificação

Agora, configurou sua política para que o Azure AD B2C sabe como se comunicar com a conta do AD FS. Tente carregar o ficheiro de extensão da sua política só para confirmar que ele não tem quaisquer problemas até agora.

1. Sobre o **políticas personalizadas** página no seu inquilino do Azure AD B2C, selecione **carregar política**.
2. Ativar **substituir a política, se existir**e, em seguida, procure e selecione o *TrustFrameworkExtensions.xml* ficheiro.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de afirmações

Neste momento, o fornecedor de identidade tiver sido configurado, mas não está disponível em qualquer um dos ecrãs de inscrição ou início de sessão. Para disponibilizá-lo, pode criar um duplicado de um percurso do utilizador modelo existente e, em seguida, modificá-lo para que ele também tem o fornecedor de identidade do ADFS.

1. Abra o *TrustFrameworkBase.xml* ficheiro do pacote de iniciante.
2. Localize e copie o conteúdo inteiro dos **UserJourney** elemento que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize a **UserJourneys** elemento. Se o elemento não existir, adicione um.
4. Colar o conteúdo inteiro do **UserJourney** elemento que copiou como subordinado da **UserJourneys** elemento.
5. Mudar o nome o ID do percurso do utilizador. Por exemplo, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Exibir o botão

O **ClaimsProviderSelection** elemento é semelhante a um botão do fornecedor de identidade numa tela de inscrição ou início de sessão. Se adicionar um **ClaimsProviderSelection** elemento de uma conta ADFS, um novo botão exibido quando um utilizador que chegam na página.

1. Encontrar o **OrchestrationStep** elemento que inclui `Order="1"` no percurso do utilizador que criou.
2. Sob **ClaimsProviderSelections**, adicione o seguinte elemento. Defina o valor da **TargetClaimsExchangeId** para um valor adequado, por exemplo `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligar o botão para uma ação

Agora que tem um botão no local, terá de ligá-lo a uma ação. A ação, neste caso, é para o Azure AD B2C comunicar com uma conta do AD FS para receber um token.

1. Encontrar o **OrchestrationStep** que inclua `Order="2"` no percurso do utilizador.
2. Adicione as seguintes **ClaimsExchange** elemento certificar-se de que usar o mesmo valor para **Id** que utilizou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Atualize o valor de **TechnicalProfileReferenceId** para o **Id** do perfil técnico que criou anteriormente. Por exemplo, `Contoso-SAML2`.

3. Guardar a *TrustFrameworkExtensions.xml* de ficheiro e carregá-lo novamente para a verificação.


## <a name="configure-an-adfs-relying-party-trust"></a>Configurar uma confiança de entidade confiadora de ADFS

Para utilizar o ADFS como um fornecedor de identidade no Azure AD B2C, terá de criar um ADFS entidade Confiadora com os metadados SAML do Azure AD B2C. O exemplo seguinte mostra um endereço de URL para os metadados SAML de um perfil técnico do Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os valores seguintes:

- **o inquilino** com o nome de inquilino, como seu tenant.onmicrosoft.com.
- **sua política** com o nome da sua política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome de tha do seu perfil de técnica de fornecedor de identidade SAML. Por exemplo, Contoso-SAML2.
 
Abra um browser e navegue para o URL. Certifique-se de que digite a URL correta e de que tem acesso para o ficheiro de metadados XML. Para adicionar uma nova confiança da entidade confiadora, utilizando o snap-in Gestão do AD FS e configurar manualmente as definições, execute o seguinte procedimento num servidor de Federação. A associação **administradores** ou equivalente no computador local é o mínimo requerido para concluir este procedimento.

1. No Gestor de servidor, selecione **ferramentas**e, em seguida, selecione **gestão de ADFS**.
2. Selecione **adicionar confiança de entidade Confiadora**.
3. Sobre o **bem-vindo** página, selecione **com suporte para afirmações**e, em seguida, clique em **iniciar**.
4. Sobre o **selecionar origem de dados** , selecione **importar dados sobre a entidade confiadora publicar online ou numa rede local**, forneça o URL de metadados do Azure AD B2C e, em seguida, clique em **próxima**.
5. Sobre o **Especificar nome a apresentar** página, introduza um **nome a apresentar**, em **notas**, introduza uma descrição para esta fidedignidade de entidade confiadora e, em seguida, clique em **seguinte**.
6. Sobre o **escolher política de controlo de acesso** página, selecione uma política e, em seguida, clique em **próxima**.
7. Sobre o **pronto para adicionar confiança** página, reveja as definições e, em seguida, clique em **próxima** para guardar a sua entidade confiadora confiar informações.
8. Sobre o **concluir** página, clique em **fechar**, esta ação apresenta automaticamente o **editar regras de afirmação** caixa de diálogo.
9. Selecione **Adicionar regra**.  
10. Na **modelo de regra de afirmação**, selecione **enviar atributos LDAP como afirmações**.
11. Fornecer um **nome da regra de afirmação**. Para o **arquivo de atributos**, selecione **selecione Active Directory**, adicione as seguintes declarações, em seguida, clique em **concluir** e **OK**.

    | Attrubute LDAP | Tipo de afirmação de saída |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Apelido | family_name |
    | Fornecido nome | given_name |
    | Endereço do correio electrónico | e-mail |
    | Nome a apresentar | nome |
    
12.  Com base no seu tipo de certificado, poderá ter de definir o algoritmo HASH. A janela Propriedades de confiança (demonstração de B2C) da parte da entidade confiadora, selecione o **avançadas** separador e altere a **algoritmo hash seguro** para `SHA-256`e clique em **Ok**.  
13. No Gestor de servidor, selecione **ferramentas**e, em seguida, selecione **gestão de ADFS**.
14. Selecione a confiança de entidade confiadora que criou, selecione **atualização de metadados de Federação**e, em seguida, clique em **atualização**. 

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação do Azure AD B2C

Comunicação com o Azure AD B2c ocorre por meio de um aplicativo que criar no seu inquilino. Esta secção lista os passos opcionais que pode seguir para criar uma aplicação de teste, se ainda não o tiver feito.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para o aplicativo, por exemplo *testapp1*.
6. Para **aplicação Web / Web API**, selecione `Yes`e, em seguida, introduza `https://jwt.ms` para o **URL de resposta**.
7. Clique em **Criar**.

### <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro da entidade confiadora de terceiros

Atualize o ficheiro da entidade confiadora de terceiros (RP) que inicia o percurso do utilizador que criou.

1. Faça uma cópia deles *SignUpOrSignIn.xml* no diretório de trabalho e renomeá-lo. Por exemplo, mude o nome para *SignUpSignInADFS.xml*.
2. Abra o ficheiro novo e atualize o valor do **PolicyId** atributo **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInADFS`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo, "http://contoso.com/B2C_1A_signup_signin_adfs" >
4. Atualize o valor do **ReferenceId** atributo **DefaultUserJourney** de acordo com o ID do percurso do utilizador novo que criou (SignUpSignInADFS).
5. Guardar as alterações, carregue o ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação do Azure AD B2C que criou está selecionada na **selecione aplicativo** campo e, em seguida, testá-lo ao clicar em **executar agora**.

