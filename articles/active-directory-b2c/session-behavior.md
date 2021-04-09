---
title: Configure comportamento da sessão - Azure Ative Directory B2C | Microsoft Docs
description: Saiba como configurar o comportamento da sessão no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a3cdb93ee4cbf4a2e15540b9daf78b6c231d393
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579744"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurar o comportamento da sessão no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O sign-on único (SSO) adiciona segurança e comodidade quando os utilizadores assinam em todas as aplicações no Azure Ative Directory B2C (Azure AD B2C). Este artigo descreve os métodos de inscrição únicos utilizados no Azure AD B2C e ajuda-o a escolher o método SSO mais adequado ao configurar a sua política.

Com uma única súmis, os utilizadores insinuam-se uma vez com uma única conta e têm acesso a várias aplicações. A aplicação pode ser uma aplicação web, móvel ou de página única, independentemente do nome de plataforma ou de domínio.

Quando o utilizador inicialmente assina uma aplicação, o Azure AD B2C persiste numa sessão baseada em cookies. Após pedidos de autenticação subsequentes, o Azure AD B2C lê e valida a sessão baseada em cookies, e emite um token de acesso sem levar o utilizador a iniciar novamente a sessão. Se a sessão baseada em cookies expirar ou se tornar inválida, o utilizador é solicitado a iniciar novamente a sessão.  

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Visão geral da sessão de Azure AD B2C

A integração com o Azure AD B2C envolve três tipos de sessões SSO:

- **Azure AD B2C** - Sessão gerida por Azure AD B2C
- **Fornecedor de identidade federada** - Sessão gerida pelo fornecedor de identidade, por exemplo Facebook, Salesforce ou conta Microsoft
- **Aplicação** - Sessão gerida pela aplicação web, móvel ou de página única

![Sessão de SSO](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Sessão Azure AD B2C 

Quando um utilizador autentica com sucesso uma conta local ou social, o Azure AD B2C armazena uma sessão baseada em cookies no navegador do utilizador. O cookie é armazenado sob o nome de domínio do inquilino Azure AD B2C, tais como `https://contoso.b2clogin.com` .

Se um utilizador inicialmente assinar com uma conta federada e, em seguida, durante a janela do tempo de sessão (tempo de vida, ou TTL) assinar na mesma app ou numa aplicação diferente, o Azure AD B2C tenta adquirir um novo token de acesso ao fornecedor de identidade federado. Se a sessão de fornecedor de identidade federada for expirada ou inválida, o fornecedor de identidade federado solicita ao utilizador as suas credenciais. Se a sessão ainda estiver ativa (ou se o utilizador tiver assinado com uma conta local em vez de uma conta federada), o Azure AD B2C autoriza o utilizador e elimina mais solicitações.

Pode configurar o comportamento da sessão, incluindo a sessão TTL e como o Azure AD B2C partilha a sessão entre políticas e aplicações.

### <a name="federated-identity-provider-session"></a>Sessão de provedor de identidade federada

Um prestador de identidade social ou empresarial gere a sua própria sessão. O cookie é armazenado sob o nome de domínio do fornecedor de identidade, tais como `https://login.salesforce.com` . A Azure AD B2C não controla a sessão de fornecedor de identidade federada. Em vez disso, o comportamento da sessão é determinado pelo fornecedor de identidade federado. 

Considere o seguinte cenário:

1. Um utilizador assina no Facebook para verificar o seu feed.
2. Mais tarde, o utilizador abre a sua aplicação e inicia o processo de início de sação. A aplicação redireciona o utilizador para Azure AD B2C para completar o processo de inscrição.
3. Na página de inscrição ou inscrição do Azure AD B2C, o utilizador opta por iniciar seduca com a sua conta de Facebook. O utilizador é redirecionado para o Facebook. Se houver uma sessão ativa no Facebook, o utilizador não é solicitado a fornecer as suas credenciais e é imediatamente redirecionado para Azure AD B2C com um token do Facebook.

### <a name="application-session"></a>Sessão de candidatura

Uma aplicação web, móvel ou de página única pode ser protegida por acesso OAuth, fichas de ID ou fichas SAML. Quando um utilizador tenta aceder a um recurso protegido na aplicação, a aplicação verifica se existe uma sessão ativa no lado da aplicação. Se não houver sessão de aplicações ou a sessão tiver expirado, a aplicação levará o utilizador ao Azure AD B2C para a página de inscrição.

A sessão de aplicação pode ser uma sessão baseada em cookies armazenada sob o nome de domínio da aplicação, como `https://contoso.com` . As aplicações móveis podem armazenar a sessão de uma forma diferente, mas usando uma abordagem semelhante.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Configurar comportamento de sessão Ad B2C

Pode configurar o comportamento da sessão Azure AD B2C, incluindo:

- **Duração da sessão de aplicações web (minutos)** - A quantidade de tempo que o cookie de sessão AZure AD B2C é armazenado no navegador do utilizador após a autenticação bem sucedida. Pode definir a vida útil da sessão até 24 horas.

- **Tempo limite de sessão de aplicações web** - Indica como uma sessão é prolongada pela definição de vida útil da sessão ou pela definição Keep me signed in (KMSI).
  - **Rolar** - Indica que a sessão é prolongada sempre que o utilizador realiza uma autenticação baseada em cookies (predefinição).
  - **Absoluto** - Indica que o utilizador é obrigado a reautensar após o período de tempo especificado.

- **Configuração única de sinal** - A sessão Azure AD B2C pode ser configurada com os seguintes âmbitos:
  - **Inquilino** - Esta definição é o padrão. A utilização desta definição permite que várias aplicações e fluxos de utilizador no seu inquilino B2C partilhem a mesma sessão de utilizador. Por exemplo, uma vez que um utilizador assina uma aplicação, o utilizador também pode assinar perfeitamente em outra ao aceder à sua aplicação.
  - **Aplicação** - Esta definição permite manter uma sessão de utilizador exclusivamente para uma aplicação, independente de outras aplicações. Por exemplo, pode utilizar esta definição se quiser que o utilizador faça o sôm.
  - **Política** - Esta definição permite manter uma sessão de utilizador exclusivamente para um fluxo de utilizador, independente das aplicações que a utilizam. Por exemplo, se o utilizador já tiver assinado e concluído um passo de autenticação multi-factor (MFA), o utilizador pode ter acesso a partes de segurança superior de várias aplicações, desde que a sessão ligada ao fluxo do utilizador não expire.
  - **Suprimido** - Esta definição obriga o utilizador a executar todo o fluxo do utilizador em cada execução da apólice.
- **Mantenha-me inscrito (KMSI)** - Prolonga a duração da sessão através da utilização de um cookie persistente. Se esta funcionalidade estiver ativada e o utilizador a selecionar, a sessão permanece ativa mesmo depois de o utilizador fechar e reabrir o navegador. A sessão só é revogada quando o utilizador assinar. A funcionalidade KMSI aplica-se apenas ao sinsub-in com contas locais. A funcionalidade KMSI tem precedência ao longo da vida útil da sessão.

::: zone pivot="b2c-user-flow"

Para configurar o comportamento da sessão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Abra o fluxo de utilizador que criou anteriormente.
1. Selecione **Propriedades**.
1. Configure a **vida útil da sessão de aplicações Web (minutos)**, o tempo limite de sessão de **aplicações web,** **a configuração de inscrição única** e o **ID Token em pedidos de logout,** conforme necessário.
1. Clique em **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Para alterar o comportamento da sessão e as configurações SSO, adicione um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty.](relyingparty.md)  O elemento **UserJourneyBehaviors** deve seguir imediatamente o **DefaultUserJourney**. O elemento **UserJourneyBehavors** deve parecer-se com este exemplo:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Enable Keep me signed in (KMSI)

Pode ativar a funcionalidade KMSI para utilizadores da sua web e aplicações nativas que tenham contas locais no seu diretório Azure AD B2C. Quando ativar a funcionalidade, os utilizadores podem optar por permanecer assinados para que a sessão permaneça ativa após o fecho do navegador. Em seguida, podem reabrir o navegador sem serem solicitados a reentrar no seu nome de utilizador e senha. Este acesso é revogado quando um utilizador assina.

![Página de inscrição de exemplo mostrando um Mantenha-me assinado na caixa de verificação](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

O KMSI é configurável ao nível de fluxo de cada utilizador. Antes de permitir kmsi para os fluxos do seu utilizador, considere o seguinte:

- O KMSI é suportado apenas para as versões **recomendadas** de inscrição e inscrição (SUSI), entrada e edição de perfis fluxos de utilizador. Se atualmente tiver **pré-visualização** **Standard** ou Legacy - versões V2 destes fluxos de utilizador e quiser ativar o KMSI, terá de criar novas versões **recomendadas** destes fluxos de utilizador.
- O KMSI não é suportado com fluxos de utilizador de redefinição de palavra-passe ou de inscrição.
- Se pretender ativar o KMSI para todas as aplicações no seu inquilino, recomendamos que adi ative o KMSI para todos os fluxos de utilizador no seu inquilino. Como um utilizador pode ser apresentado com múltiplas políticas durante uma sessão, é possível que possam encontrar uma que não tenha KMSI ativado, o que removeria o cookie KMSI da sessão.
- O KMSI não deve ser ativado em computadores públicos.

### <a name="configure-kmsi-for-a-user-flow"></a>Configure KMSI para um fluxo de utilizador

Para ativar o KMSI para o fluxo do seu utilizador:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +**   no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços**   no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (políticas)**.
5. Abra o fluxo de utilizador que criou anteriormente.
6. Selecione **Propriedades**.

7. Em  **comportamento de Sessão**, selecione **Mantenha-me assinado na sessão**. Ao lado **de Manter-me assinado em sessão (dias)**, insira um valor de 1 a 90 para especificar o número de dias em que uma sessão pode permanecer aberta.


   ![Habilitar manter-me assinado na sessão](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Os utilizadores não devem ativar esta opção em computadores públicos.

### <a name="configure-the-page-identifier"></a>Configurar o identificador de página

Para ativar o KMSI, desafine o elemento de definição de conteúdo `DataUri` para [o identificador de página](contentdefinitions.md#datauri) e a `unifiedssp` [versão](page-layout.md) *1.1.0* ou superior.

1. Abra o ficheiro de extensão da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Este ficheiro de extensão é um dos ficheiros de política incluídos no pacote de iniciação de políticas personalizadas, que deveria ter obtido no pré-requisito, [começar com políticas personalizadas](custom-policy-get-started.md).
1. Procure o elemento **Blocos de Construção.** Se o elemento não existir, adicione-o.
1. Adicione o elemento **ContentDefinitions** ao elemento Blocos de **Construção** da política.

    A sua política personalizada deve parecer o seguinte corte de código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Adicione os metadados ao perfil técnico autoafirmado

Para adicionar a caixa de verificação KMSI à página de inscrição e inscrição, desenverda os `setting.enableRememberMe` metadados como verdadeiros. Substitua os perfis técnicos SelfAsserted-LocalAccountSignin-Email no ficheiro de extensão.

1. Encontre o elemento ClaimsProviders. Se o elemento não existir, adicione-o.
1. Adicione o seguinte fornecedor de reclamações ao elemento ClaimsProviders:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Guarde o ficheiro de extensões.

### <a name="configure-a-relying-party-file"></a>Configurar um ficheiro de partido de confiança

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou. O parâmetro keepAliveInDays permite-lhe configurar como o cookie de sessão de manter-me assinado (KMSI) deve persistir. Por exemplo, se definir o valor para 30, então o cookie de sessão KMSI persistirá durante 30 dias. O intervalo para o valor é de 1 a 90 dias.

1. Abra o seu ficheiro de política personalizado. Por exemplo, *SignUpOrSignin.xml.*
1. Se já não existir, adicione um `<UserJourneyBehaviors>` nó de criança ao `<RelyingParty>` nó. Deve ser colocado imediatamente após `<DefaultUserJourney ReferenceId="User journey Id" />` , por exemplo: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Adicione o nó seguinte como uma criança do `<UserJourneyBehaviors>` elemento.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

Recomendamos que defina o valor do SessionExpiryInSegundos para um período curto (1200 segundos), enquanto o valor do KeepAliveInDays pode ser definido para um período relativamente longo (30 dias), como mostra o seguinte exemplo:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Terminar sessão

Quando pretender assinar o utilizador fora da aplicação, não basta limpar os cookies da aplicação ou terminar a sessão com o utilizador. Tem de redirecionar o utilizador para Azure AD B2C para se inscrever. Caso contrário, o utilizador poderá voltar a autenticar as suas aplicações sem voltar a introduzir as suas credenciais.

Após um pedido de sinalização, Azure AD B2C:

1. Invalida a sessão baseada em cookies Azure AD B2C.
::: zone pivot="b2c-user-flow"
2. Tentativas de assinatura de fornecedores de identidade federados
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Tentativas de assinatura de fornecedores de identidade federados:
   - OpenId Connect - Se o ponto final de configuração bem conhecido do fornecedor de identidade especificar uma `end_session_endpoint` localização. O pedido de aprovação não passa no `id_token_hint` parâmetro. Se o fornecedor de identidade federado necessitar deste parâmetro, o pedido de assinatura falhará.
   - OAuth2 - Se os [metadados do fornecedor de identidade](oauth2-technical-profile.md#metadata) contiver a `end_session_endpoint` localização.
   - SAML - Se os [metadados do fornecedor de identidade](identity-provider-generic-saml.md) contiver a `SingleLogoutService` localização.
4. Opcionalmente, o sinal de saída de outras aplicações. Para mais informações, consulte a secção [de sinalização única.](#single-sign-out)

> [!NOTE]
> Pode desativar a sinalização dos fornecedores de identidade federados, definindo os metadados de perfil técnico do fornecedor de identidade `SingleLogoutEnabled` para `false` .
::: zone-end

A sessão de sinalização apaga o estado de inscrição do utilizador com o Azure AD B2C, mas pode não assinar o utilizador fora da sua sessão de provedor de identidade social. Se o utilizador selecionar o mesmo fornecedor de identidade durante uma sintrodução subsequente, poderá reautentar sem introduzir as suas credenciais. Se um utilizador quiser assinar a aplicação, não significa necessariamente que queira assinar fora da sua conta do Facebook. No entanto, se forem utilizadas contas locais, a sessão do utilizador termina corretamente.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Fim de sessão único 

Quando redireciona o utilizador para o ponto final de assinatura Azure AD B2C (tanto para os protocolos OAuth2 como PARA SAML), o Azure AD B2C limpa a sessão do utilizador do navegador. No entanto, o utilizador poderá ainda ser inscrito noutras aplicações que utilizem o Azure AD B2C para autenticação. Para permitir que essas aplicações assinem o utilizador em simultâneo, o Azure AD B2C envia um pedido HTTP GET ao registo `LogoutUrl` de todas as aplicações a que o utilizador está atualmente inscrito.

As aplicações devem responder a este pedido, limpando qualquer sessão que identifique o utilizador e devolvendo uma `200` resposta. Se pretender apoiar uma única sinseção na sua aplicação, tem de implementar uma `LogoutUrl` no código da sua aplicação. 

Para apoiar uma única assinatura, os perfis técnicos do emitente simbólico para o JWT e para a SAML devem especificar:

- O nome do protocolo, como `<Protocol Name="OpenIdConnect" />`
- A referência ao perfil técnico da sessão, tal como `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

O exemplo a seguir ilustra os emitentes JWT e SAML com uma única súmia:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>Fixe o seu redirecionamento de logout

Após o logout, o utilizador é redirecionado para o URI especificado no `post_logout_redirect_uri` parâmetro, independentemente dos URLs de resposta especificados para a aplicação. No entanto, se um válido `id_token_hint` for aprovado e o **Token de ID requere em pedidos de logout** é ligado, Azure AD B2C verifica que o valor da `post_logout_redirect_uri` correspondência corresponde a um dos URIs de redirecionamento configurados da aplicação antes de efetuar o redirecionamento. Se não tiver sido configurado um URL de resposta correspondente para a aplicação, é apresentada uma mensagem de erro e o utilizador não é redirecionado. 

::: zone pivot="b2c-user-flow"

Para exigir um ID Token em pedidos de logout:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Abra o fluxo de utilizador que criou anteriormente.
1. Selecione **Propriedades**.
1. Ativar o **Token ID Do Requerendo em pedidos de logout**.
1. Volte para  **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione a sua aplicação.
1. Selecione **Autenticação**.
1. Na caixa de texto **URL logout,** digite o seu logout post redirecione URI e, em seguida, selecione **Save**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Para exigir um Token de ID em pedidos de logout, adicione um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty.](relyingparty.md) Em seguida, desloque o **EnforceIdTokenHintOnLogout** do elemento **SingleSignOn** para `true` . O elemento **UserJourneyBehaviors** deve parecer-se com este exemplo:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Para configurar a sua aplicação Logout URL:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione a sua aplicação.
1. Selecione **Autenticação**.
1. Na caixa de texto **URL logout,** digite o seu logout post redirecione URI e, em seguida, selecione **Save**.

## <a name="next-steps"></a>Passos seguintes

- Saiba [como configurar fichas em Azure AD B2C](configure-tokens.md).
