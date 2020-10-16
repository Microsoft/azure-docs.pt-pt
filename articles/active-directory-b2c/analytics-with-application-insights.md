---
title: Acompanhe o comportamento do utilizador com insights de aplicação
titleSuffix: Azure AD B2C
description: Saiba como ativar os registos de eventos em Application Insights a partir de viagens de utilizador Azure AD B2C utilizando políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ea7324419d86fa5b5c23a2f0aa5f8c057495d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385982"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Acompanhe o comportamento do utilizador no Azure Ative Directory B2C usando Insights de Aplicação

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure Ative Directory B2C (Azure AD B2C) suporta o envio de dados de eventos diretamente para [a Application Insights](../azure-monitor/app/app-insights-overview.md) utilizando a chave de instrumentação fornecida ao Azure AD B2C.  Com um perfil técnico do Application Insights, pode obter registos de eventos detalhados e personalizados para as viagens do utilizador para:

* Obtenha informações sobre o comportamento do utilizador.
* Resolva as suas próprias políticas de desenvolvimento ou produção.
* Medir o desempenho.
* Criar notificações a partir de Insights de Aplicação.

## <a name="how-it-works"></a>Como funciona

O perfil técnico [do Application Insights](application-insights-technical-profile.md) define um evento do Azure AD B2C. O perfil especifica o nome do evento, as alegações que são registadas e a chave de instrumentação. Para publicar um evento, o perfil técnico é então adicionado como um passo de orquestração numa [jornada de utilizador](userjourneys.md).

O Application Insights pode unificar os eventos utilizando um ID de correlação para gravar uma sessão de utilizador. A Application Insights disponibiliza o evento e a sessão em segundos e apresenta muitas ferramentas de visualização, exportação e analítica.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Começar com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar s inscrição com contas locais.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando estiver a utilizar o Application Insights com Azure AD B2C, tudo o que precisa de fazer é criar um recurso e obter a chave de instrumentação. Para obter informações, consulte [Criar um recurso De Insights de Aplicação](../azure-monitor/app/create-new-resource.md)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém a subscrição do Azure selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém a sua subscrição. Este inquilino não é o seu inquilino Azure AD B2C.
3. Escolha **Criar um recurso** no canto superior esquerdo do portal Azure e, em seguida, procurar e selecionar Insights de **Aplicação**.
4. Clique em **Criar**.
5. Insira um **Nome** para o recurso.
6. Para **o Tipo de Aplicação**, selecione ASP.NET **aplicação web**.
7. Para **o Grupo de Recursos,** selecione um grupo existente ou introduza um nome para um novo grupo.
8. Clique em **Criar**.
4. Depois de criar o recurso Application Insights, abra-o, expanda o **Essencial**e copie a tecla de instrumentação.

![Visão geral e chave de instrumentação de insights de aplicação](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definir reclamações

Uma reclamação fornece um armazenamento temporário de dados durante uma execução política Azure AD B2C. O [esquema de reclamações](claimsschema.md) é o lugar onde declara as suas reivindicações.

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione as seguintes reclamações ao elemento **ClaimsSchema.** 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Adicionar novos perfis técnicos

Os perfis técnicos podem ser considerados funções no Quadro de Experiência de Identidade do Azure AD B2C. Esta tabela define os perfis técnicos que são usados para abrir uma sessão e pós eventos.

| Perfil Técnico | Tarefa |
| ----------------- | -----|
| AppInsights-Common | O conjunto comum de parâmetros a incluir em todos os perfis técnicos do Azure Insights. |
| AppInsights-SignInRequest | Grava um `SignInRequest` evento com um conjunto de reclamações quando um pedido de inscrição foi recebido. |
| AppInsights-UserSignUp | Grava um `UserSignUp` evento quando o utilizador ativa a opção de inscrição numa viagem de inscrição/inscrição. |
| AppInsights-SignInComplete | Grava um `SignInComplete` evento com sucesso na conclusão de uma autenticação, quando um token foi enviado para a aplicação da parte de gestão. |

Adicione os perfis ao * ficheiroTrustFrameworkExtensions.xml* do pacote inicial. Adicione estes elementos ao elemento **ClaimsProviders:**

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Altere a chave de instrumentação no `AppInsights-Common` perfil técnico para o GUID que o seu recurso Application Insights fornece.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Adicione os perfis técnicos como etapas de orquestração

Chamada `AppInsights-SignInRequest` como passo de orquestração 2 para acompanhar que foi recebido um pedido de inscrição/inscrição:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente *antes* do `SendClaims` passo de orquestração, adicione um novo passo que ligue `AppInsights-UserSignup` . É acionado quando o utilizador seleciona o botão de inscrição numa viagem de inscrição/inscrição.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente após o `SendClaims` passo de orquestração, `AppInsights-SignInComplete` ligue. Este passo mostra uma viagem concluída com sucesso.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Depois de adicionar os novos passos de orquestração, renumerar os passos sequencialmente sem saltar nenhum número inteiro de 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Faça upload do seu ficheiro, execute a política e veja eventos

Guarde e carre fique * no *TrustFrameworkExtensions.xmlficheiro. Em seguida, ligue para a política do partido em gestão da sua aplicação ou use **Run Now** no portal Azure. Em segundos, os seus eventos estão disponíveis em Application Insights.

1. Abra o recurso **Application Insights** no seu inquilino Azure Ative Directory.
2. Selecione **Eventos de Utilização**  >  **Events**.
3. Definir **durante** a **última hora** e **por** **3 minutos**.  Poderá ser necessário selecionar **Refresh** para visualizar resultados.

![Insights de Aplicação USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>[Opcional] Recolher mais dados

Adicione tipos de reclamações e eventos à sua viagem de utilizador para se adaptar às suas necessidades. Pode utilizar [os resolvers de reclamação](claim-resolver-overview.md) ou qualquer tipo de reclamação de cordas, adicionar as reclamações adicionando um elemento **de Reclamação** de Entrada ao evento Application Insights ou ao perfil técnico AppInsights-Common.

- **ClaimTypeReferenceId** é a referência a um tipo de reclamação.
- **PartnerClaimType** é o nome da propriedade que aparece no Azure Insights. Utilize a sintaxe de `{property:NAME}` , onde a propriedade está sendo adicionada ao `NAME` evento.
- **DefaultValue** use qualquer valor de cadeia ou o resolver de reclamação.

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o perfil técnico [da Application Insights](application-insights-technical-profile.md) na referência IEF. 
