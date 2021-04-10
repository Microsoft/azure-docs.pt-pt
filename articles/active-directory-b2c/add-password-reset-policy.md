---
title: Configurar um fluxo de reset de palavra-passe em Azure AD B2C
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de reset de palavra-passe no Azure Ative Directory B2C.
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
ms.openlocfilehash: f451d08dfbde643d91705f54296e9757a51c9d88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798398"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de reset de palavra-passe no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Fluxo de reset de palavra-passe

A [jornada de inscrição e de inscrição](add-sign-up-and-sign-in-policy.md) permite que os utilizadores reiniciem a sua própria palavra-passe utilizando o link **Forgot your password?** O fluxo de reset da palavra-passe envolve os seguintes passos:

1. A partir da página de inscrição e inscrição, o utilizador clica no link **Esquecimento da sua palavra-passe?** Azure AD B2C inicia o fluxo de reset da palavra-passe. 
2. O utilizador fornece e verifica o seu endereço de e-mail com uma senha de tempo cronometrada.
3. O utilizador pode então introduzir uma nova palavra-passe.

![Fluxo de reset de palavra-passe](./media/add-password-reset-policy/password-reset-flow.png)

O fluxo de reset da palavra-passe aplica-se às contas locais em Azure AD B2C que utilizam um [endereço de e-mail](identity-provider-local.md#email-sign-in) ou [nome de utilizador](identity-provider-local.md#username-sign-in) com uma palavra-passe para iniciar sposição.

> [!TIP]
> O fluxo de reset da palavra-passe de autosserviço permite que os utilizadores alterem a sua palavra-passe quando o utilizador esquece a sua palavra-passe e quer repor a sua palavra-passe. Considere configurar um [fluxo de mudança de senha](add-password-change-policy.md) para suportar casos em que um utilizador saiba a sua palavra-passe e queira alterá-la.

Uma prática comum após migrar os utilizadores para Azure AD B2C com senhas aleatórias é fazer com que os utilizadores verifiquem os seus endereços de e-mail e repuem as suas palavras-passe durante a sua primeira sação. Também é comum forçar o utilizador a redefinir a sua palavra-passe depois de um administrador alterar a sua palavra-passe; ver [redefinição da palavra-passe](force-password-reset.md) de força para ativar esta funcionalidade.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Reset da palavra-passe de autosserviço (recomendado)

A nova experiência de reset de palavra-passe faz agora parte da política de inscrição ou de inscrição. Quando o utilizador seleciona o link **Forgot your password?** A sua aplicação já não necessita de lidar com o [código de erro AADB2C90118](#password-reset-policy-legacy), e não necessita de uma política separada para reiniciar a palavra-passe.

::: zone pivot="b2c-user-flow"

A experiência de reset da palavra-passe de autosserviço pode ser configurada para o **Sign-in (Recomendado)** ou **iniciar sposição e iniciar sposição nos** fluxos de utilizador (recomendados). Se não tiver tal fluxo de utilizador, crie um sinal de fluxo de utilizador [e inscreva-se.](add-sign-up-and-sign-in-policy.md) 

Para permitir a redefinição da palavra-passe de autosserviço para o fluxo do utilizador de inscrição ou de entrada:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione um fluxo de utilizador de inscrição ou de inscrição (do tipo **Recomendado)** que pretenda personalizar.
1. Em **Definições** no menu esquerdo, selecione **Propriedades**.
1. Sob **a complexidade da palavra-passe**, selecione **redefinição da palavra-passe de self-service**.
1. Selecione **Guardar**.
1. **Em Personalizar** no menu esquerdo, selecione **layouts de página**.
1. Na versão layout da **página,** escolha **2.1.2 - Corrente** ou superior.
1. Selecione **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

As secções seguintes descrevem como adicionar uma experiência de senha de autosserviço a uma política personalizada. A amostra baseia-se nos ficheiros de política incluídos no pacote de arranque de [políticas personalizadas](./custom-policy-get-started.md). 

> [!TIP]
> Pode encontrar uma amostra completa da política de "iniciar sposição ou iniciar sposição com a palavra-passe reset" no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Indicar que um utilizador selecionou a palavra-passe Esquecida? ligação

Para indicar à política que o utilizador selecionou o link **Esqueceu-se da sua palavra-passe,** defina uma reclamação booleana. Esta alegação será utilizada para direcionar a viagem do utilizador para o perfil técnico da Palavra-Passe Esquecida. Esta reclamação também pode ser emitida para o token, pelo que a aplicação está ciente de que o utilizador assinou através do fluxo de Senha Esquecida.

Declara as suas alegações no esquema de [reclamações.](claimsschema.md) Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione a seguinte reclamação ao elemento **ClaimsSchema.** 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>Atualizar a versão de layout da página

[Versão de layout de página](contentdefinitions.md#migrating-to-page-layout) `2.1.2` é necessário para permitir o fluxo de reset da palavra-passe de autosserviço dentro da jornada de inscrição ou de inscrição.

1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localizar o elemento [Definições de Conteúdo.](contentdefinitions.md) Se o elemento não existir, adicione-o.
1. Modificar o elemento **DataURI** dentro do elemento **ContentDefinition** com Id **api.signuporsignin,** conforme mostrado abaixo.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

Para dar início à `isForgotPassword` reclamação, é utilizado um perfil técnico de transformação de sinistros. Este perfil técnico será referenciado mais tarde. Quando invocado, definirá o valor da `isForgotPassword` reclamação para `true` . Encontre o `ClaimsProviders` elemento. Se o elemento não existir, adicione-o. Em seguida, adicione o seguinte provedor de sinistros:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

O `SelfAsserted-LocalAccountSignin-Email` perfil técnico define a troca de `setting.forgotPasswordLinkOverride` pedidos de redefinição de pedidos de palavra-passe a ser executada na sua jornada de utilizador. 

### <a name="add-the-password-reset-sub-journey"></a>Adicione a sub-jornada de reset da palavra-passe

A sua viagem incluirá agora a capacidade de o utilizador iniciar sposição, iniciar sposição e fazer o reset da palavra-passe. Para organizar melhor a viagem do utilizador, uma [sub-viagem](subjourneys.md) pode ser usada para lidar com o fluxo de reset da palavra-passe.

A sub-viagem será chamada da viagem do utilizador e executará os passos específicos para entregar a experiência de reset da palavra-passe ao utilizador. Utilize o `Call` tipo de sub-viagem de modo a que, uma vez concluída a subtração, o controlo seja devolvido ao passo de orquestração que iniciou a sub-jornada.

Encontre o `SubJourneys` elemento. Se o elemento não existir, adicione-o depois do `User Journeys` elemento. Em seguida, adicione a seguinte sub-viagem:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Prepare a sua jornada de utilizador

Terá de ligar a **palavra-passe esquecida à** sub-jornada da Palavra-Passe Esquecida. Para isso, faça referência ao id do sub-journey da Palavra-passe Esquecido dentro do elemento **ClaimsProviderSelection** do passo **CombinedSignInAndSignUp.**

Se não tiver a sua própria viagem de utilizador personalizada com um passo **CombinadoSInSignUp,** utilize o seguinte procedimento para duplicar uma viagem de utilizador ou inscrição ou de inscrição existente. Caso contrário, continue para a secção seguinte.

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
3. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Crie um elemento infantil do elemento **UserJourneys** colando todo o conteúdo do elemento **UserJourney** que copiou no passo 2.
5. Mude o nome da id da viagem de utilizador. Por exemplo, `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Ligue o Link palavra-passe esquecido à sub-jornada da Palavra-Passe Esquecida 

Na sua jornada de utilizador, pode representar a sub-viagem esquecida da Palavra-passe como **uma Seleção ClaimsProvider .** Adicionar este elemento liga a **palavra-passe Esquecida?**

1. Na viagem do utilizador, encontre o elemento do passo de orquestração que inclua `Type="CombinedSignInAndSignUp"` ou `Type="ClaimsProviderSelection"` . Normalmente é o primeiro passo de orquestração. O elemento **ClaimsProviderSelections** contém uma lista de fornecedores de identidade que um utilizador pode usar para iniciar sação. Adicione a seguinte linha:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. No próximo passo de orquestração, adicione um elemento **ClaimsExchange.** Adicione a seguinte linha:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Adicione o seguinte passo de orquestração entre o passo atual e o passo seguinte. O novo passo de orquestração que acrescenta, verifica se a `isForgotPassword` alegação existe. Se a reclamação existir, invoca a [sub-viagem de reset da palavra-passe](#add-the-password-reset-sub-journey). 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. Depois de adicionar o novo passo de orquestração, renumerar os passos sequencialmente sem saltar nenhum número inteiro de 1 a N.

### <a name="set-the-user-journey-to-be-executed"></a>Desemote a jornada do utilizador para ser executado

Agora que modificou ou criou uma viagem de utilizador, na secção **Do Partido De Relying** especifica a viagem que a Azure AD B2C executará para esta política personalizada. Dentro do elemento [RelyingParty,](relyingparty.md) encontre o elemento **DefaultUserJourney.** Atualize o  **DefaultUserJourney ReferenceId** para corresponder ao ID da viagem de utilizador em que adicionou as **ReivindicaçõesProviderSelections**.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Indicar o fluxo de palavra-passe esquecida para a sua App

A sua aplicação poderá ter de detetar se o utilizador inscreveu-se através do fluxo de utilizador da Palavra-passe Esquecida. A alegação **isForgotPassword** contém um valor boolean que indica isso, que pode ser emitido no token enviado para a sua aplicação. Se necessário, adicione `isForgotPassword` aos pedidos de saída na secção **Partido De Gestão.** A sua aplicação pode verificar a `isForgotPassword` reclamação para determinar se o utilizador reinicia a sua palavra-passe.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Faça o upload da política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **''Personal's Personal Policy ' 'Upload'** e, em seguida, faça o upload dos dois ficheiros de política que alterou na seguinte encomenda:
   1. A política de extensão, por `TrustFrameworkExtensions.xml` exemplo.
   2. A política partidária, por `SignUpSignIn.xml` exemplo.

::: zone-end

### <a name="test-the-password-reset-flow"></a>Teste o fluxo de reset da palavra-passe

1. Selecione um fluxo de utilizador de inscrição ou de inscrição (do tipo Recomendado) que pretende testar.
1. Selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione **Executar o fluxo do utilizador**.
1. A partir da página de inscrição ou de inscrição, **selecione Esquecir-se da sua palavra-passe?**
1. Verifique o endereço de e-mail da conta que criou anteriormente e, em seguida, selecione **Continue**.
1. Tem agora a oportunidade de alterar a palavra-passe para o utilizador. Altere a palavra-passe e **selecione Continue**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.
1. Verifique o valor de reclamação do token de `isForgotPassword` retorno. Se existir e for definido como verdadeiro, isto indica que o utilizador reiniciou a palavra-passe.

## <a name="password-reset-policy-legacy"></a>Política de reset de palavra-passe (legado)

Se a experiência [de reset da palavra-passe de autosserviço](#self-service-password-reset-recommended) não estiver ativada, clicar neste link não ativa automaticamente um fluxo de utilizador de redefinição de palavra-passe. Em vez disso, o código de erro `AADB2C90118` é devolvido à sua aplicação. A sua aplicação necessita de lidar com este código de erro, reiniciando a biblioteca de autenticação para autenticar uma palavra-passe AD B2C de redefinição do fluxo do utilizador.

No seguinte diagrama:

1. A partir da aplicação, o utilizador clica no sôm-in. A aplicação inicia um pedido de autorização e leva o utilizador ao Azure AD B2C para terminar a sessão. O pedido de autorização especifica o nome da política de inscrição ou inscrição, como **B2C_1_signup_signin**.
1. O utilizador seleciona o link **Esqueceu-se da sua palavra-passe?** Azure AD B2C devolve o código de erro AADB2C90118 à aplicação.
1. A aplicação trata do código de erro e inicia um novo pedido de autorização. O pedido de autorização especifica o nome da política de reset da palavra-passe, como **B2C_1_pwd_reset**.

![Fluxo de utilizador de redefinição de palavra-passe legacy](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Para ver um exemplo, veja uma [simples amostra de ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI), que demonstra a ligação dos fluxos dos utilizadores.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de utilizador de redefinição de palavra-passe

Para permitir que os utilizadores da sua aplicação reiniciem a sua palavra-passe, cria um fluxo de utilizador de redefinição de palavra-passe.

1. No menu geral do inquilino Azure AD B2C, selecione **os fluxos do utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.
1. Na página De fluxo do **utilizador,** selecione o fluxo do utilizador **de redefinição de palavra-passe.** 
1. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**.
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *passwordreste1*.
1. Para **os fornecedores de identidade,** ative **a palavra-passe Reset utilizando o endereço de e-mail.**
1. Ao abrigo **de reclamações de Aplicação**, selecione **Mostrar mais** e escolha as reclamações que deseja devolvidas nos tokens de autorização enviados de volta para a sua aplicação. Por exemplo, selecione **ID de Objeto do Utilizador**.
1. Selecione **OK**.
1. Selecione **Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar,** verifique o endereço de e-mail da conta que criou anteriormente e, em seguida, selecione **Continue**.
1. Pode agora alterar a palavra-passe para o utilizador. Altere a palavra-passe e **selecione Continue**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Criar uma política de reposição de palavra-passe

As políticas personalizadas são um conjunto de ficheiros XML que envia para o seu inquilino Azure AD B2C para definir viagens de utilizador. Fornecemos pacotes de iniciação com várias políticas pré-construídas, incluindo: inscrição e início de sessão, reset de palavra-passe e política de edição de perfis. Para obter mais informações, consulte [Começar com políticas personalizadas em Azure AD B2C.](custom-policy-get-started.md)

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Configurar uma [redefinição da palavra-passe de força.](force-password-reset.md)
