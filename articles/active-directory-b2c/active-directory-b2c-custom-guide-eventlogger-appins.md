---
title: Controlar o comportamento do utilizador com eventos no Application Insights do Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como ativar registos de eventos no Application Insights do Azure AD B2C jornadas de utilizador ao utilizar políticas personalizadas (pré-visualização).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 51342a7544295b465d1f2dfc3e1b27d8285b3588
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403821"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Controlar o comportamento do utilizador no Azure Active Directory B2C com o Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Quando utiliza o Azure Active Directory (Azure AD) B2C, juntamente com o Azure Application Insights, pode entrar em detalhes sobre e personalizadas registos de eventos para o seu jornadas de utilizador. Neste artigo, vai aprender a:

* Obtenha informações sobre o comportamento do utilizador.
* Resolver problemas relacionados com as suas políticas no desenvolvimento ou em produção.
* Medir o desempenho.
* Crie notificações do Application Insights.

## <a name="how-it-works"></a>Como funciona

O Framework de experiência de identidade no Azure AD B2C inclui o provedor `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Enviar dados de eventos diretamente para o Application Insights utilizando a chave de instrumentação fornecida para o Azure AD B2C.

Um perfil técnico usa esse provedor para definir um evento do Azure AD B2C. O perfil Especifica o nome do evento, as afirmações que são registadas e a chave de instrumentação. Para publicar um evento, o perfil técnico, em seguida, é adicionado como um `orchestration step`, ou como um `validation technical profile` num percurso do utilizador personalizada.

O Application Insights pode unificar os eventos ao utilizar um ID de correlação para gravar uma sessão do utilizador. Application Insights disponibiliza o evento e a sessão dentro de segundos e apresenta muitos visualização, exportação e ferramentas analíticas.

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos na [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md). Este artigo pressupõe que está a utilizar o pacote de iniciante da política personalizada. Mas o pacote de iniciante não é necessário.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando estiver a utilizar o Application Insights com o Azure AD B2C, tudo o que precisa fazer é criar um recurso e obtenha a chave de instrumentação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém a sua subscrição do Azure ao clicar o **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém a sua subscrição. Este inquilino não é o inquilino do Azure AD B2C.
3. Escolher **criar um recurso** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **Application Insights**.
4. Clique em **Criar**.
5. Introduza um **nome** para o recurso.
6. Para **tipo de aplicação**, selecione **aplicação web ASP.NET**.
7. Para **grupo de recursos**, selecione um grupo existente ou introduza um nome para um novo grupo.
8. Clique em **Criar**.
4. Depois de criar o recurso do Application Insights, abri-lo, expanda **Essentials**e copie a chave de instrumentação.

![Descrição geral do Application Insights e a chave de instrumentação](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Adicionar novas definições de ClaimType

Abra o *TrustFrameworkExtensions.xml* de ficheiros do pacote de iniciante e adicione os seguintes elementos para o [BuildingBlocks](buildingblocks.md) elemento:

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>Adicionar novos perfis técnicos

Perfis técnicos podem ser considerados as funções na identidade experiência Framework do Azure AD B2C. Esta tabela define os perfis de técnicos que são utilizados para abrir uma sessão e publicar eventos.

| Perfil técnico | Tarefa |
| ----------------- | -----|
| AzureInsights-Common | Cria um conjunto comum de parâmetros a serem incluídos em todos os perfis de técnicos de AzureInsights. | 
| AzureInsights-SignInRequest | Cria um evento de início de sessão com um conjunto de declarações, quando recebido um pedido de início de sessão. | 
| AzureInsights-UserSignup | Cria um evento de UserSignup quando o utilizador for acionada a opção Inscreva-se numa jornada de início de sessão-inscrição/início de sessão. | 
| AzureInsights-SignInComplete | Regista a conclusão com êxito de uma autenticação quando um token tiver sido enviado para a aplicação da entidade confiadora de terceiros. | 

Adicionar os perfis para o *TrustFrameworkExtensions.xml* ficheiro do pacote de iniciante. Adicionar estes elementos para o **ClaimsProviders** elemento:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Alterar a chave de instrumentação no `ApplicationInsights-Common` perfil técnico para o GUID que fornece o recurso do Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Adicionar os perfis técnicos como etapas da orquestração

Chamar `Azure-Insights-SignInRequest` como orquestração passo 2 para controlar o que foi recebido um pedido início de sessão-em/inscrever-se:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente *antes de* a `SendClaims` orquestração passo, adicione um novo passo que chama `Azure-Insights-UserSignup`. Ele é acionado quando o usuário seleciona o botão inscrever-se numa jornada de início de sessão-inscrição/início de sessão.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente após a `SendClaims` passo de orquestração, chamada `Azure-Insights-SignInComplete`. Este passo mostra uma jornada concluída com êxito.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Renumber, depois de adicionar novos passos de orquestração, os passos sequencialmente sem ignorar qualquer inteiros de 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Carregar o ficheiro, executar a política e ver eventos

Guardar e carregue o *TrustFrameworkExtensions.xml* ficheiro. Em seguida, chamar a política de terceiros entidade confiadora da aplicação ou utilize **executar agora** no portal do Azure. Em segundos, os seus eventos estão disponíveis no Application Insights.

1. Abra o **Application Insights** recursos no seu inquilino do Azure Active Directory.
2. Selecione **utilização** > **eventos**.
3. Definir **durante** ao **última hora** e **por** para **3 minutos**.  Poderá ter de selecionar **atualizar** ver os resultados.

![Application Insights-eventos de utilização Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Passos Seguintes

Adicione tipos de afirmação e eventos para o seu percurso do utilizador para se ajustar às suas necessidades. Pode usar [resolvedores de afirmação](claim-resolver-overview.md) ou qualquer cadeia de tipo de afirmação, adicione as afirmações, adicionando um **de solicitação de entrada** elemento para o evento do Application Insights ou para o perfil técnico AzureInsights comuns. 

- **ClaimTypeReferenceId** é a referência para um tipo de afirmação.
- **PartnerClaimType** é o nome da propriedade que é apresentada no Azure Insights. Utilize a sintaxe de `{property:NAME}`, onde `NAME` é propriedade a ser adicionada ao evento. 
- **DefaultValue** utilizar qualquer valor de cadeia ou o Resolvedor de afirmação. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

