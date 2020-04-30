---
title: Adicione a ADFS como fornecedor de identidade SAML utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Configurar a ADFS 2016 utilizando o protocolo SAML e políticas personalizadas no Diretório Ativo Azure B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 12845f09ac2eb2342cdb1ab82b703ebd3a67c706
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229736"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicione a ADFS como fornecedor de identidade SAML utilizando políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para uma conta de utilizador ADFS utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C). Permite o início de sessão adicionando um perfil técnico de fornecedor de [identidade SAML](saml-identity-provider-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Get started com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).
- Certifique-se de que tem acesso a um ficheiro certificado .pfx com uma chave privada. Pode gerar o seu próprio certificado assinado e carregá-lo para O Azure AD B2C. O Azure AD B2C utiliza este certificado para assinar o pedido SAML enviado ao seu fornecedor de identidade SAML.
- Para que o Azure aceite a senha de ficheiro .pfx, a palavra-passe deve ser encriptada com a opção TripleDES-SHA1 no utilitário de exportação da Windows Certificate Store, em oposição ao AES256-SHA256.

## <a name="create-a-policy-key"></a>Criar uma chave política

Você precisa armazenar seu certificado no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
5. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha. `Upload`
7. Introduza um **nome** para a chave política. Por exemplo, `SamlCert`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Navegue e selecione o seu ficheiro .pfx com a chave privada.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se pretender que os utilizadores assinem através de uma conta ADFS, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são utilizadas pelo Azure AD B2C para verificar se um utilizador específico se autenticou.

Pode definir uma conta ADFS como fornecedor de sinistros adicionando-a ao elemento **Reclamadores** no ficheiro de extensão da sua apólice. Para mais informações, consulte definir um perfil técnico do fornecedor de [identidade SAML](saml-identity-provider-technical-profile.md).

1. Abra as *Extensões TrustFramework.xml*.
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo Fornecedor de **Reclamações** da seguinte forma:

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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Substitua-o `your-ADFS-domain` pelo nome do seu domínio ADFS e substitua o valor da reivindicação de saída do Fornecedor de **identidade** pelo seu DNS (valor arbitrário que indica o seu domínio).

1. Localize `<ClaimsProviders>` a secção e adicione o seguinte corte XML. Se a sua `SM-Saml-idp` política já contiver o perfil técnico, salte para o próximo passo. Para mais informações, consulte [a gestão da sessão de inscrição única](custom-policy-reference-sso.md).

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

Por esta altura, já configuraste a sua política para que o Azure AD B2C saiba comunicar com a conta ADFS. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **Políticas Personalizadas** do seu inquilino Azure AD B2C, selecione **Política de Upload**.
2. Ativar **a sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

> [!NOTE]
> A extensão do código Do Estúdio Visual B2C utiliza "socialIdpUserId". É também necessária uma política social para a ADFS.
>

## <a name="register-the-claims-provider"></a>Registe o prestador de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição ou inscrição. Para disponibilizá-lo, cria-se uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modificá-la de modo a que também tenha o fornecedor de identidade ADFS.

1. Abra o ficheiro *TrustFrameworkBase.xml* a partir do pacote de arranque.
2. Encontre e copie todo o conteúdo `Id="SignUpOrSignIn"`do elemento **UserJourney** que inclui .
3. Abra as *Extensões TrustFramework.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Colhe todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem do utilizador. Por exemplo, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição ou de inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta ADFS, aparece um novo botão quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na viagem de utilizador que criou.
2. Ao abrigo **das seleções ClaimsProviderS,** adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para `ContosoExchange`um valor adequado, por exemplo:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de ligá-lo a uma ação. Neste caso, a ação é que o Azure AD B2C comunique com uma conta ADFS para receber um símbolo.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para o ID que utilizou para **targetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para o ID do perfil técnico que criou anteriormente. Por exemplo, `Contoso-SAML2`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.


## <a name="configure-an-adfs-relying-party-trust"></a>Configure uma Confiança partidária de confiança da ADFS

Para utilizar o ADFS como fornecedor de identidade no Azure AD B2C, é necessário criar um ADFS Relying Party Trust com os metadados Azure AD B2C SAML. O exemplo seguinte mostra um endereço URL para os metadados SAML de um perfil técnico Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **seu inquilino** com o seu nome de inquilino, como your-tenant.onmicrosoft.com.
- **sua política** com o seu nome de política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome do seu perfil técnico do fornecedor de identidade SAML. Por exemplo, Contoso-SAML2.

Abra um navegador e navegue para o URL. Certifique-se de que escreve o URL correto e que tem acesso ao ficheiro de metadados XML. Para adicionar uma nova confiança do partido, utilizando o snap-in da ADFS Management e configurar manualmente as definições, execute o seguinte procedimento num servidor da federação. A adesão a **Administradores** ou equivalente no computador local é o mínimo necessário para completar este procedimento.

1. No Gestor do Servidor, selecione **Ferramentas**, e, em seguida, selecione **ADFS Management**.
2. **Selecione Adicionar Confiança de Parte Fiducida**.
3. Na página **Welcome,** escolha **Reivindicações conscientes**, e, em seguida, clique **em Iniciar**.
4. Na página **Select Data Source,** selecione **dados de Importação sobre a parte que depende publicar online ou numa rede local,** forneça o seu URL de metadados Azure AD B2C e, em seguida, clique em **Next**.
5. Na página **'Especificar Nome do ecrã',** introduza um **nome de exibição,** em **Notas,** introduza uma descrição para esta confiança de parte fiducida e, em seguida, clique em **Seguinte**.
6. Na página **'Escolha a Política de Controlo de Acesso',** selecione uma política e, em seguida, clique **em Seguinte**.
7. No **pronto para adicionar confiança** página, reveja as definições e, em seguida, clique **seguinte** para guardar a sua entidade confiadora confiar informações.
8. Na página **'Terminar',** clique em **Fechar,** esta ação exibe automaticamente a caixa de diálogo regras de reclamação de **edição.**
9. Selecione **Adicionar Regra**.
10. No modelo de **regra de reclamação,** selecione **Enviar atributos LDAP como reclamações**.
11. Forneça um nome de **regra de reclamação**. Para a **loja Attribute,** selecione **'Select Active' ' Directivo'**, adicione as seguintes reclamações e, em seguida, clique em **Terminar** e **OK**.

    | Atributo LDAP | Tipo de reclamação de saída |
    | -------------- | ------------------- |
    | Nome-diretor do utilizador | userPrincipalName |
    | Apelido | family_name |
    | Nome dado | given_name |
    | Endereço de e-mail | e-mail |
    | Nome do ecrã | nome |

    Note que estes nomes não serão apresentados no tipo de reclamação de saída. Precisa digitá-los manualmente. (O dropdown é na verdade editável).

12.  Com base no seu tipo de certificado, poderá ter de definir o algoritmo HASH. Na janela de propriedades do trust trust (B2C Demo) fiduciário, `SHA-256`selecione o separador **Advanced** e altere o algoritmo de **hash Secure** para , e clique em **Ok**.
13. No Gestor do Servidor, selecione **Ferramentas**, e, em seguida, selecione **ADFS Management**.
14. Selecione a confiança da parte que criou, selecione **Atualizar a partir de Metadados da Federação,** e depois clique em **Atualizar**.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que se regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que depende

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho e mude o nome. Por exemplo, mude o nome para *SignUpSignInADFS.xml*.
2. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInADFS`.
3. Atualizar o valor da **PublicPolicyUri** com o URI para a política. Por exemplo`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova viagem de utilizador que criou (SignUpSignInADFS).
5. Guarde as suas alterações, faça o upload do ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação Azure AD B2C que criou é selecionada no campo de **aplicação Select** e, em seguida, testá-la clicando agora em **Executar**.

