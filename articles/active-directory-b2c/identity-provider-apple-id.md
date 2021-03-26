---
title: Configurar inscrição e iniciar sedições com um Apple ID
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com Apple ID nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 63183eb6a77b3a7aecfb6f3e8a7c9ee7c2544de2
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543912"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Configurar o pré-serviço e iniciar seduca com um Apple ID utilizando o Azure Ative Directory B2C (Pré-visualização)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Criar uma aplicação Apple ID

Para permitir o insessão para utilizadores com um Apple ID em Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação em [https://developer.apple.com](https://developer.apple.com) . Para mais informações, consulte [Iniciar sção com a Apple.](https://developer.apple.com/sign-in-with-apple/get-started/) Se ainda não tiver uma conta de programador da Apple, pode inscrever-se no [Apple Developer Program](https://developer.apple.com/programs/enroll/).

1. Inscreva-se no [Portal do Desenvolvedor](https://developer.apple.com/account) da Apple com as credenciais da sua conta.
1. No menu, selecione **Certificados, IDs, perfis de &** e, em seguida, selecione **(+)**.
1. Para **registar um novo identificador,** selecione **IDs de aplicações** e, em seguida, selecione **Continue**.
1. Para **selecionar um tipo,** selecione **App** e, em seguida, selecione **Continue**.
1. Para **registar um ID da aplicação:**
    1. Insira uma **Descrição** 
    1. Introduza o **Bundle ID,** tal como `com.contoso.azure-ad-b2c` . 
    1. Para **capacidades**, **selecione Iniciar sedições com** a Apple na lista de capacidades. 
    1. Tome nota do prefixo ID da app (Team ID) a partir deste passo. Precisará dela mais tarde.
    1. **Selecione Continue** e, em seguida, **Registe-se**.
1. No menu, selecione **Certificados, IDs, perfis de &** e, em seguida, selecione **(+)**.
1. Para **registar um novo identificador,** selecione **IDs de serviços** e, em seguida, selecione **Continue**.
1. Para **registar uma ID de serviço:**
    1. Introduza uma **Descrição**. A descrição é mostrada ao utilizador no ecrã de consentimento.
    1. Introduza o **Identificador,** tal como `com.consoto.azure-ad-b2c-service` . O identificador é o seu ID cliente para o fluxo OpenID Connect.
    1. **Selecione Continuar** e, em seguida, selecione **Registar- se**.
1. A partir **de Identificadores,** selecione o identificador que criou.
1. Selecione **Iniciar S-S-In com a Apple** e, em seguida, selecione **Configure**.
    1. Selecione o **ID da aplicação primária** com o que pretende configurar Iniciar súm em apple com.
    1. Em **Domínios e Subdomínios,** insira `your-tenant-name.b2clogin.com` . Substitua o nome do seu inquilino pelo nome do seu inquilino. Se utilizar um [domínio personalizado,](custom-domain.md)insira `https://your-domain-name` .
    1. In **Return URLs**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Se utilizar um [domínio personalizado,](custom-domain.md)insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`Substitua-o pelo nome do seu inquilino e `your-domain-name` pelo seu domínio personalizado.
    1. Selecione **Seguinte** e, em seguida, selecione **Fazer**.
    1. Quando a janela pop-up estiver fechada, selecione **Continue** e, em seguida, selecione **Save**.

## <a name="creating-an-apple-client-secret"></a>Criar um segredo de cliente apple

1. A partir do menu do portal Apple Developer, selecione **Keys** e, em seguida, selecione **(+)**.
1. Para **registar uma nova chave:**
    1. Digite um **nome de chave.**
    1. Selecione **Iniciar sôms com a Apple** e, em seguida, selecione **Configure**.
    1. Para o **ID da aplicação primária,** selecione a aplicação que criou anteriormente e o select **Save**.
    1. Selecione **Configurar** e, em seguida, **selecione Registar-se** para terminar o processo de registo chave.
1. Para **baixar a sua chave**, selecione **Baixar** para descarregar um ficheiro .p8 que contém a sua chave.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Configuturar a Apple como fornecedor de identidade

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador global do seu inquilino Azure AD B2C.
1. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.
1. Selecione **fornecedores de identidade** e, em seguida, selecione **Apple (Preview)**.
1. Insira um **nome**. Por exemplo, *a Apple.*
1. Introduza o ID do desenvolvedor da **Apple (Team ID)**.
1. Introduza o ID do **serviço Apple (ID do cliente)**.
1. Introduza o **ID da chave Apple.**
1. Selecione e carrele os dados do **certificado da Apple.**
1. Selecione **Guardar**.


> [!IMPORTANT] 
> - O login com a Apple requer que o Administrador renove o segredo dos seus clientes a cada 6 meses. 
> - Durante a pré-visualização pública desta funcionalidade, terá de renovar manualmente o segredo do cliente da Apple se este expirar. Um aviso será apresentado antecipadamente nos fornecedores de identidade da Apple Configure a página de IDP social, mas recomendamos que desista de definir o seu próprio lembrete. 
> - Se precisar de renovar o segredo, abra o Azure AD B2C no portal Azure, vá aos **fornecedores de identidade**  >  **Apple,** e selecione **Renovar o segredo.**

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade apple a um fluxo de utilizador

Para permitir que os utilizadores entrem a trabalhar com um Apple ID, é necessário adicionar o fornecedor de identidade da Apple a um fluxo de utilizador. O sin-in com a Apple só pode ser configurado para a versão **recomendada** dos fluxos dos utilizadores. Para adicionar o fornecedor de identidade da Apple a um fluxo de utilizador:

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Selecione um fluxo de utilizador para o qual pretende adicionar o fornecedor de identidade Apple. 
1. Sob **os fornecedores de identidade social,** selecione **Apple (Preview)**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **de fluxo do utilizador Executar.**
1. A partir da página de inscrição ou de inscrição, selecione **Apple** para iniciar sedições com o Apple ID.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Assinando o segredo do cliente

Utilize o ficheiro .p8 que descarregou anteriormente para assinar o segredo do cliente num token JWT. Há muitas bibliotecas que podem criar e assinar o JWT para si. Utilize a [Função Azure que cria um símbolo](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) para si. 

1. Criar uma [Função Azure](../azure-functions/functions-create-function-app-portal.md).
1. No **Programador**, selecione **Código + Teste**. 
1. Copie o conteúdo do ficheiro [run.csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) e cole-o no editor.
1. Selecione **Guardar**.
1. Faça um pedido HTTP `POST` e forneça as seguintes informações:

    - **appleTeamId**: O seu ID da equipa de desenvolvedores da Apple
    - **appleServiceId**: O ID do Serviço apple (também o ID do cliente)
    - **p8key**: A tecla de formato PEM. Pode obtê-lo abrindo o ficheiro .p8 num editor de texto e copiando tudo entre `-----BEGIN PRIVATE KEY-----` e `-----END PRIVATE KEY-----` sem quebras de linha.
 
O seguinte json é um exemplo de uma chamada para a função Azure:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

A função Azure responde com um JWT secreto cliente devidamente formatado e assinado numa resposta, por exemplo:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que você já gravou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
2. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.
1. Na página **'Visão Geral',** selecione **Identity Experience Framework**.
1. Selecione **as teclas de** política e, em seguida, selecione **Adicionar**.
1. Para **opções**, escolha **Manual**.
1. Insira um **Nome** para a chave de política. Por exemplo, "AppleSecret". O prefixo "B2C_1A_" é adicionado automaticamente ao nome da sua chave.
1. Em **Segredo**, insira o valor de um símbolo devolvido pela Função Azure (um símbolo JWT).
1. Para **a utilização da chave**, selecione **Signature**.
1. Selecione **Criar**.

> [!IMPORTANT] 
> - O login com a Apple requer que o Administrador renove o segredo dos seus clientes a cada 6 meses.
> - Terá de renovar manualmente o segredo do cliente da Apple se expirar e armazenar o novo valor na chave de política.
> - Recomendamos que desista do seu próprio lembrete dentro de 6 meses para gerar um novo segredo de cliente. 

## <a name="configure-apple-as-an-identity-provider"></a>Configuturar a Apple como fornecedor de identidade

Para permitir que os utilizadores assinem através de um Apple ID, é necessário definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico é autenticado.

Pode definir um Apple ID como fornecedor de sinistros adicionando-o ao elemento **ClaimsProviders** no ficheiro de extensão da sua política.

1. Abra a *TrustFrameworkExtensions.xml.*
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Desa** esta client_id ao identificador de serviço. Por exemplo, `com.consoto.azure-ad-b2c-service`.
5. Guarde o ficheiro.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin` .
1. Para **Aplicação**, selecione uma aplicação web que já [se registou anteriormente.](troubleshoot-custom-policies.md#troubleshoot-the-runtime) A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**
1. A partir da página de inscrição ou de inscrição, selecione **Apple** para iniciar sedições com o Apple ID.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

::: zone-end
