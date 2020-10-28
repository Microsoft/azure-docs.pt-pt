---
title: Adicione a ADFS como fornecedor de identidade SAML utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Configurar a ADFS 2016 utilizando o protocolo SAML e as políticas personalizadas no Azure Ative Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60bc4623416eeb491d073dba9517ac13861a3e9e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633455"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicione a ADFS como fornecedor de identidade SAML utilizando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar a inscrição para uma conta de utilizador ADFS utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C). Você ativa o sessão adicionando um perfil técnico do fornecedor de [identidade SAML](saml-identity-provider-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Começar com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).
- Certifique-se de que tem acesso a um ficheiro certificado .pfx com uma chave privada. Pode gerar o seu próprio certificado assinado e enviá-lo para Azure AD B2C. A Azure AD B2C utiliza este certificado para assinar o pedido SAML enviado ao seu fornecedor de identidade SAML. Para obter mais informações sobre como gerar um certificado, consulte [Gerar um certificado de assinatura.](identity-provider-salesforce-custom.md#generate-a-signing-certificate)
- Para que o Azure aceite a palavra-passe de ficheiro .pfx, a palavra-passe deve ser encriptada com a opção TripleDES-SHA1 no utilitário de exportação da Loja de Certificados do Windows em oposição ao AES256-SHA256.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar seu certificado no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C** .
4. Na página 'Visão Geral', selecione **Identity Experience Framework** .
5. Selecione **As teclas de política** e, em seguida, selecione **Adicionar** .
6. Para **Opções,** escolha `Upload` .
7. Insira um **Nome** para a chave de política. Por exemplo, `SamlCert`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Navegue e selecione o seu ficheiro certificado .pfx com a chave privada.
9. Clique em **Criar** .

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se quiser que os utilizadores assinem através de uma conta ADFS, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir uma conta ADFS como fornecedor de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política. Para obter mais informações, consulte [definir um perfil técnico do fornecedor de identidade SAML.](saml-identity-provider-technical-profile.md)

1. Abra a *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

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
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlCert"/>
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

1. `your-ADFS-domain`Substitua-o pelo nome do seu domínio ADFS e substitua o valor da reclamação de saída **do Identitário** pelo seu DNS (valor arbitrário que indica o seu domínio).

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

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com a conta ADFS. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
2. Ativar **a política em caso de existência** e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar** .

> [!NOTE]
> A extensão do código de estúdio visual B2C utiliza "socialIdpUserId". É também necessária uma política social para a ADFS.
>

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição ou inscrição. Para disponibilizá-lo, cria uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifica-a de modo a que também tenha o fornecedor de identidade ADFS.

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
3. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição ou de inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta ADFS, um novo botão aparece quando um utilizador pousa na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que criou.
2. Em **SinistrosProviderSeles** , adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `ContosoExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, consiste em que o Azure AD B2C comunique com uma conta ADFS para receber um token.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para o ID que utilizou para **TargetClaimsExchangeId** :

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Atualizar o valor do **TécnicoProfileReferenceD** para o ID do perfil técnico que criou anteriormente. Por exemplo, `Contoso-SAML2`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.


## <a name="configure-an-adfs-relying-party-trust"></a>Configure uma ADFS confiando na confiança do partido

Para utilizar o ADFS como fornecedor de identidade no Azure AD B2C, é necessário criar um ADFS Relying Party Trust com os metadados Azure AD B2C SAML. O exemplo a seguir mostra um endereço URL para os metadados SAML de um perfil técnico AZure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **seu inquilino** com o seu nome de inquilino, como your-tenant.onmicrosoft.com.
- **sua política** com o seu nome de política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome do seu perfil técnico do fornecedor de identidade SAML. Por exemplo, Contoso-SAML2.

Abra um navegador e navegue para o URL. Certifique-se de que escreve o URL correto e que tem acesso ao ficheiro de metadados XML. Para adicionar uma nova confiança do partido, utilizando o snap-in da ADFS Management e configurar manualmente as definições, execute o seguinte procedimento num servidor da federação. A adesão em **Administradores** ou equivalente no computador local é o mínimo necessário para completar este procedimento.

1. No Gestor do Servidor, selecione **Ferramentas** e, em seguida, selecione **ADFS Management** .
2. **Selecione Add Relying Party Trust** .
3. Na página **Welcome,** escolha **Reclamações conscientes** e, em seguida, clique em **Iniciar** .
4. Na página **Select Data Source,** selecione **Dados de Importação sobre a parte que confia publicar online ou numa rede local,** forneça o URL de metadados Azure AD B2C e, em seguida, clique em **Seguinte** .
5. Na página **'Indicar o Nome do Visualização',** introduza um **nome de Exibição** , em **Notas** , introduza uma descrição para esta confiança do partido em suporte e, em seguida, clique em **Seguinte** .
6. Na página **'Escolha Política de Controlo de Acesso',** selecione uma política e, em seguida, clique em **Seguinte** .
7. No **pronto para adicionar confiança** página, reveja as definições e, em seguida, clique **seguinte** para guardar a sua entidade confiadora confiar informações.
8. Na página **'Terminar',** clique **em Fechar** , esta ação exibe automaticamente a caixa de diálogo **'Regras de Reclamação de Edição'.**
9. Selecione **'Adicionar Regra'.**
10. No **modelo de regra de reclamação,** selecione Enviar **atributos LDAP como reclamações** .
11. Fornecer um **nome de regra de reclamação** . Para a **loja Atributo** , selecione Select **Ative Directory** , adicione as seguintes reclamações, em seguida, clique em **Terminar** e **OK** .

    | Atributo LDAP | Tipo de reclamação de saída |
    | -------------- | ------------------- |
    | Nome principal do utilizador | userPrincipalName |
    | Apelido | family_name |
    | Given-Name | given_name |
    | E-mail-endereço | e-mail |
    | Display-Name | name |

    Note que estes nomes não serão apresentados no dropdown do tipo de reclamação de saída. Precisa digitá-las manualmente. (O dropdown é realmente editável).

12.  Com base no seu tipo de certificado, poderá ter de definir o algoritmo HASH. Na janela de propriedades do trust do partido (B2C Demo), selecione o separador **Advanced** e altere o **algoritmo de hash Secure** para , e clique em `SHA-256` **Ok** .
13. No Gestor do Servidor, selecione **Ferramentas** e, em seguida, selecione **ADFS Management** .
14. Selecione a confiança do partido que criou, selecione **Update from Federation Metadata** e, em seguida, clique em **Update** .

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação AZure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignInADFS.xml* .
2. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInADFS`.
3. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova jornada de utilizador que criou (SignUpSignInADFS).
5. Guarde as suas alterações, carre fique no upload do ficheiro e, em seguida, selecione a nova política da lista.
6. Certifique-se de que a aplicação AD B2C AD que criou está selecionada no campo **de aplicações Select** e, em seguida, testá-la clicando **em Executar agora** .

