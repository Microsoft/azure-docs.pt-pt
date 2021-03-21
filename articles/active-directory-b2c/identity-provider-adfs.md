---
title: Adicione AD FS como um fornecedor de identidade SAML usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Configurar AD FS 2016 usando o protocolo SAML e políticas personalizadas em Azure Ative Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 292a244a4804f97e8622d6841c33b153af373290
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489173"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar AD FS como fornecedor de identidade SAML usando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar a inscrição para uma conta de utilizador AD FS utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C). Ativa o sismo adicionando um fornecedor de [identidade SAML](identity-provider-generic-saml.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar seu certificado no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione **Identity Experience Framework**.
5. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha `Upload` .
7. Insira um **Nome** para a chave de política. Por exemplo, `SAMLSigningCert`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Navegue e selecione o seu ficheiro certificado .pfx com a chave privada.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se quiser que os utilizadores assinem através de uma conta AD FS, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir uma conta AD FS como fornecedor de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política. Para obter mais informações, consulte [um fornecedor de identidade SAML.](identity-provider-generic-saml.md)

1. Abra a *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
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

1. `your-AD-FS-domain`Substitua-o pelo nome do seu domínio AD FS e substitua o valor da reclamação de saída **do Identitário** pelo seu DNS (valor arbitrário que indica o seu domínio).

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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>Configure um AD FS confiando na confiança do partido

Para utilizar o AD FS como fornecedor de identidade no Azure AD B2C, é necessário criar um AD FS Relying Party Trust com os metadados Azure AD B2C SAML. O exemplo a seguir mostra um endereço URL para os metadados SAML de um perfil técnico AZure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ao utilizar um [domínio personalizado,](custom-domain.md)utilize o seguinte formato:

```
https://your-domain-name/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **seu nome de inquilino** com o seu nome de inquilino, como your-tenant.onmicrosoft.com.
- **o seu nome de domínio** com o seu nome de domínio personalizado, como login.contoso.com.
- **sua política** com o seu nome de política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome do seu perfil técnico do fornecedor de identidade SAML. Por exemplo, Contoso-SAML2.

Abra um navegador e navegue para o URL. Certifique-se de que escreve o URL correto e que tem acesso ao ficheiro de metadados XML. Para adicionar uma nova confiança do partido, utilizando o snap-in da AD FS Management e configurar manualmente as definições, execute o seguinte procedimento num servidor da federação. A adesão em **Administradores** ou equivalente no computador local é o mínimo necessário para completar este procedimento.

1. No Gestor do Servidor, selecione **Ferramentas** e, em seguida, selecione **AD FS Management**.
2. **Selecione Add Relying Party Trust**.
3. Na página **Welcome,** escolha **Reclamações conscientes** e, em seguida, clique em **Iniciar**.
4. Na página **Select Data Source,** selecione **Dados de Importação sobre a parte que confia publicar online ou numa rede local,** forneça o URL de metadados Azure AD B2C e, em seguida, clique em **Seguinte**.
5. Na página **'Indicar o Nome do Visualização',** introduza um **nome de Exibição**, em **Notas**, introduza uma descrição para esta confiança do partido em suporte e, em seguida, clique em **Seguinte**.
6. Na página **'Escolha Política de Controlo de Acesso',** selecione uma política e, em seguida, clique em **Seguinte**.
7. No **pronto para adicionar confiança** página, reveja as definições e, em seguida, clique **seguinte** para guardar a sua entidade confiadora confiar informações.
8. Na página **'Terminar',** clique **em Fechar**, esta ação exibe automaticamente a caixa de diálogo **'Regras de Reclamação de Edição'.**
9. Selecione **'Adicionar Regra'.**
10. No **modelo de regra de reclamação,** selecione Enviar **atributos LDAP como reclamações**.
11. Fornecer um **nome de regra de reclamação**. Para a **loja Atributo**, selecione Select **Ative Directory**, adicione as seguintes reclamações, em seguida, clique em **Terminar** e **OK**.

    | Atributo LDAP | Tipo de reclamação de saída |
    | -------------- | ------------------- |
    | Nome principal do utilizador | userPrincipalName |
    | Apelido | family_name |
    | Given-Name | given_name |
    | E-mail-endereço | e-mail |
    | Display-Name | name |

    Note que estes nomes não serão apresentados no dropdown do tipo de reclamação de saída. Precisa digitá-las manualmente. (O dropdown é realmente editável).

12.  Com base no seu tipo de certificado, poderá ter de definir o algoritmo HASH. Na janela de propriedades do trust do partido (B2C Demo), selecione o separador **Advanced** e altere o **algoritmo de hash Secure** para , e clique em `SHA-256` **Ok**.
13. No Gestor do Servidor, selecione **Ferramentas** e, em seguida, selecione **AD FS Management**.
14. Selecione a confiança do partido que criou, selecione **Update from Federation Metadata** e, em seguida, clique em **Update**.

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. No âmbito **de políticas**, selecione Identity **Experience Framework**
1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin` .
1. Para **Aplicação**, selecione uma aplicação web que já [se registou anteriormente.](tutorial-register-applications.md) A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**
1. A partir da página de inscrição ou inscrição, **selecione Contoso AD FS** para iniciar sinsução com o fornecedor de identidade DaD FS contoso.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

## <a name="troubleshooting-ad-fs-service"></a>Serviço de resolução de problemas AD FS  

O AD FS está configurado para utilizar o registo de aplicações do Windows. Se sentir desafios na configuração do AD FS como fornecedor de identidade SAML utilizando políticas personalizadas em Azure AD B2C, pode querer verificar o registo de eventos AD FS:

1. Na **barra de pesquisa** do Windows, escreva o Observador de **Eventos** e, em seguida, selecione a aplicação de ambiente de trabalho **do Observador de Eventos.**
1. Para ver o registo de um computador diferente, clique à direita **no Observador de Eventos (local)**. Selecione **Connect a outro computador** e preencha os campos para completar a caixa de diálogo Select **Computer.**
1. No **Visualizador de Eventos,** abra os **Registos de Aplicações e Serviços**.
1. Selecione **AD FS** e, em seguida, selecione **Administrador**. 
1. Para ver mais informações sobre um evento, clique duas vezes no evento.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>Pedido SAML não é assinado com evento de algoritmo de assinatura esperado

Este erro indica que o pedido SAML enviado pelo Azure AD B2C não é assinado com o algoritmo de assinatura esperado configurado em AD FS. Por exemplo, o pedido DE SAML é assinado com o algoritmo de `rsa-sha256` assinatura, mas o algoritmo de assinatura esperado é `rsa-sha1` . Para corrigir este problema, certifique-se de que tanto o Azure AD B2C como o AD FS estão configurados com o mesmo algoritmo de assinatura.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Opção 1: Definir o algoritmo de assinatura em Azure AD B2C  

Pode configurar como assinar o pedido DA SAML em Azure AD B2C. Os metadados [XmlSignatureAlgorithm controlam](identity-provider-generic-saml.md) o valor do parâmetro (linha de `SigAlg` consulta ou parâmetro pós)no pedido DEL. O exemplo a seguir configura Azure AD B2C para usar o `rsa-sha256` algoritmo de assinatura.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Opção 2: Definir o algoritmo de assinatura em AD FS 

Em alternativa, pode configurar o algoritmo de assinatura de pedido SAML esperado em AD FS.

1. No Gestor do Servidor, selecione **Ferramentas** e, em seguida, selecione **AD FS Management**.
1. Selecione a Confiança do **Partido Que** criou anteriormente.
1. Selecione **Propriedades** e, em seguida, selecione **Advance**
1. Configure o **algoritmo de haxixe Secure** e selecione **OK** para guardar as alterações.

::: zone-end
