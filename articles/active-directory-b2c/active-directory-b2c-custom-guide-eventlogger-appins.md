---
title: Acompanhar o comportamento do usuário com Application Insights
titleSuffix: Azure AD B2C
description: Saiba como habilitar logs de eventos em Application Insights de Azure AD B2C percursos do usuário usando políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8376deecb5e184c01b41495b868b57bd8fd745d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367965"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Acompanhar o comportamento do usuário em Azure Active Directory B2C usando Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Ao usar o Azure Active Directory B2C (Azure AD B2C) junto com o Aplicativo Azure insights, você pode obter logs de eventos detalhados e personalizados para os percursos do usuário. Neste artigo, vai aprender a:

* Obter informações sobre o comportamento do usuário.
* Solucionar problemas de suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Criar notificações de Application Insights.

## <a name="how-it-works"></a>Como funciona

A estrutura de experiência de identidade no Azure AD B2C inclui o provedor `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Ele envia dados de eventos diretamente para Application Insights usando a chave de instrumentação fornecida para Azure AD B2C.

Um perfil técnico usa esse provedor para definir um evento de Azure AD B2C. O perfil especifica o nome do evento, as declarações que são registradas e a chave de instrumentação. Para postar um evento, o perfil técnico é adicionado como um `orchestration step` em uma jornada do usuário personalizado.

Application Insights pode unificar os eventos usando uma ID de correlação para registrar uma sessão de usuário. Application Insights torna o evento e a sessão disponíveis em segundos e apresenta muitas ferramentas analíticas, de exportação e de visualização.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas](active-directory-b2c-get-started-custom.md). Este artigo pressupõe que você esteja usando o pacote de início de política personalizada. Mas o pacote inicial não é necessário.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando você estiver usando Application Insights com Azure AD B2C, tudo o que você precisa fazer é criar um recurso e obter a chave de instrumentação.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém sua assinatura do Azure selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém sua assinatura. Este locatário não é seu locatário Azure AD B2C.
3. Escolha **criar um recurso** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Application insights**.
4. Clique em **Criar**.
5. Insira um **nome** para o recurso.
6. Para **tipo de aplicativo**, selecione **ASP.NET aplicativo Web**.
7. Para **grupo de recursos**, selecione um grupo existente ou insira um nome para um novo grupo.
8. Clique em **Criar**.
4. Depois de criar o recurso Application Insights, abra-o, expanda **Essentials**e copie a chave de instrumentação.

![Visão geral de Application Insights e chave de instrumentação](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Adicionar novas definições de ClaimType

Abra o arquivo *TrustFrameworkExtensions. xml* do pacote inicial e adicione os seguintes elementos ao elemento [BuildingBlocks](buildingblocks.md) :

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

Os perfis técnicos podem ser considerados funções na estrutura de experiência de identidade do Azure AD B2C. Esta tabela define os perfis técnicos que são usados para abrir uma sessão e postar eventos.

| Perfil técnico | Tarefa |
| ----------------- | -----|
| AzureInsights-Common | Cria um conjunto comum de parâmetros a serem incluídos em todos os perfis técnicos do umconjunto. |
| AzureInsights-SignInRequest | Cria um evento de entrada com um conjunto de declarações quando uma solicitação de entrada é recebida. |
| AzureInsights-UserSignup | Cria um evento usersignup quando o usuário aciona a opção de inscrição em uma jornada de inscrição/entrada. |
| AzureInsights-SignInComplete | Registra a conclusão com êxito de uma autenticação quando um token é enviado para o aplicativo de terceira parte confiável. |

Adicione os perfis ao arquivo *TrustFrameworkExtensions. xml* do pacote inicial. Adicione esses elementos ao elemento **ClaimsProviders** :

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
> Altere a chave de instrumentação no perfil técnico de `AzureInsights-Common` para o GUID que o recurso de Application Insights fornece.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Adicionar os perfis técnicos como etapas de orquestração

Chame `Azure-Insights-SignInRequest` como a etapa 2 de orquestração para acompanhar que uma solicitação de entrada/inscrição foi recebida:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente *antes* da etapa `SendClaims` orquestração, adicione uma nova etapa que chama `Azure-Insights-UserSignup`. Ele é disparado quando o usuário seleciona o botão de inscrição em uma jornada de inscrição/entrada.

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

Imediatamente após a etapa de orquestração de `SendClaims`, chame `Azure-Insights-SignInComplete`. Esta etapa mostra uma jornada concluída com êxito.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Depois de adicionar as novas etapas de orquestração, renumere as etapas sequencialmente sem ignorar nenhum inteiro de 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Carregar o arquivo, executar a política e exibir eventos

Salve e carregue o arquivo *TrustFrameworkExtensions. xml* . Em seguida, chame a política de terceira parte confiável do seu aplicativo ou use **executar agora** no portal do Azure. Em segundos, os eventos estão disponíveis no Application Insights.

1. Abra o recurso de **Application insights** no seu locatário Azure Active Directory.
2. Selecione os **eventos**de > de **uso** .
3. Defina **durante** para a **última hora** **e de** até **3 minutos**.  Talvez seja necessário selecionar **Atualizar** para exibir os resultados.

![Application Insights uso-eventos Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Passos seguintes

Adicione tipos de declaração e eventos à sua jornada de usuário para atender às suas necessidades. Você pode usar [resolvedores de declaração](claim-resolver-overview.md) ou qualquer tipo de declaração de cadeia de caracteres, adicionar as declarações adicionando um elemento de **declaração de entrada** ao evento Application insights ou ao perfil técnico umconjunto-comum.

- **ClaimTypeReferenceId** é a referência a um tipo de declaração.
- **PartnerClaimType** é o nome da propriedade que aparece no Azure insights. Use a sintaxe de `{property:NAME}`, em que `NAME` é a propriedade que está sendo adicionada ao evento.
- **DefaultValue** use qualquer valor de cadeia de caracteres ou o resolvedor de declaração.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

