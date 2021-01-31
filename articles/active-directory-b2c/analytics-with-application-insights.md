---
title: Acompanhe o comportamento do utilizador com insights de aplicação
titleSuffix: Azure AD B2C
description: Saiba como ativar os registos de eventos em Application Insights a partir de viagens de utilizador Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218558"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Acompanhe o comportamento do utilizador no Azure Ative Directory B2C usando Insights de Aplicação

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

O Azure Ative Directory B2C (Azure AD B2C) suporta o envio de dados de eventos diretamente para [a Application Insights](../azure-monitor/app/app-insights-overview.md) utilizando a chave de instrumentação fornecida ao Azure AD B2C. Com um perfil técnico do Application Insights, pode obter registos de eventos detalhados e personalizados para as viagens do utilizador para:

* Obtenha informações sobre o comportamento do utilizador.
* Resolva as suas próprias políticas de desenvolvimento ou produção.
* Medir o desempenho.
* Criar notificações a partir de Insights de Aplicação.

## <a name="overview"></a>Descrição Geral

Para ativar registos de eventos personalizados, adicione um perfil técnico de Insights de Aplicação. No perfil técnico, define a chave de instrumentação de Insights de Aplicação, nome do evento e as alegações para registar. Para publicar um evento, o perfil técnico é então adicionado como um passo de orquestração numa [jornada de utilizador](userjourneys.md).

Ao utilizar os Insights de Aplicação, considere o seguinte:

- Há um curto atraso, tipicamente menos de cinco minutos, antes de novos registos disponíveis no Application Insights.
- O Azure AD B2C permite-lhe escolher as reclamações a registar. Não inclua reclamações com dados pessoais.
- Para gravar uma sessão de utilizador, os eventos podem ser unificados usando um ID de correlação. 
- Ligue para o perfil técnico do Application Insights diretamente de uma [viagem](userjourneys.md) de utilizador ou de uma [sub-viagens](subjourneys.md). Não utilize o perfil técnico da Application Insights como perfil técnico de [validação](validation-technical-profile.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

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
4. Depois de criar o recurso Application Insights, abra-o, expanda o **Essencial** e copie a tecla de instrumentação.

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

Os perfis técnicos podem ser considerados funções na política personalizada. Esta tabela define os perfis técnicos que são usados para abrir uma sessão e pós eventos. A solução utiliza a abordagem [de inclusão de perfil técnico.](technicalprofiles.md#include-technical-profile) Quando um perfil técnico inclui outro perfil técnico para alterar definições ou adicionar uma nova funcionalidade.

| Perfil Técnico | Tarefa |
| ----------------- | -----|
| AppInsights-Common | O perfil técnico comum com o conjunto comum de configuração. Incluindo a chave de instrumentação application Insights, a recolha de reclamações para gravar e o modo de desenvolvimento. Os seguintes perfis técnicos incluem o perfil técnico comum, e adicionar mais reclamações, como o nome do evento. |
| AppInsights-SignInRequest | Grava um `SignInRequest` evento com um conjunto de reclamações quando um pedido de inscrição foi recebido. |
| AppInsights-UserSignUp | Grava um `UserSignUp` evento quando o utilizador ativa a opção de inscrição numa viagem de inscrição/inscrição. |
| AppInsights-SignInComplete | Grava um `SignInComplete` evento com sucesso na conclusão de uma autenticação, quando um token foi enviado para a aplicação da parte de gestão. |

Adicione os perfis ao *ficheiroTrustFrameworkExtensions.xml* do pacote inicial. Adicione estes elementos ao elemento **ClaimsProviders:**

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
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
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
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
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

Guarde e carre fique *no* TrustFrameworkExtensions.xmlficheiro. Em seguida, ligue para a política do partido em gestão da sua aplicação ou use **Run Now** no portal Azure. Espere um minuto ou mais, e os seus eventos estarão disponíveis em Application Insights.

1. Abra o recurso **Application Insights** no seu inquilino Azure Ative Directory.
2. Selecione **Utilização** e, em seguida, selecione **Eventos**.
3. Definir **durante** a **última hora** e **por** **3 minutos**.  Poderá ser necessário selecionar **Refresh** para visualizar resultados.

![Insights de Aplicação USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Recolher mais dados

Para se adaptar às suas necessidades de negócio, pode querer registar mais reclamações. Para adicionar uma reclamação, primeiro [defina uma reclamação,](#define-claims)em seguida, adicione a reclamação à coleção de reclamações de entrada. As alegações que adicionar ao perfil técnico *AppInsights-Common,* aparecerão em todos os eventos. As alegações que adicionar a um perfil técnico específico, só aparecerão nesse evento. O elemento de reclamação de entrada contém os seguintes atributos:

- **ClaimTypeReferenceId** - é a referência a um tipo de reclamação. 
- **PartnerClaimType** - é o nome da propriedade que aparece no Azure Insights. Utilize a sintaxe de `{property:NAME}` , onde a propriedade está sendo adicionada ao `NAME` evento.
- **DefaultValue** - Um valor predefinido a ser gravado, como o nome do evento. Uma alegação que é utilizada na viagem do utilizador, como o nome do fornecedor de identidade. Se a reclamação estiver vazia, o valor predefinido será utilizado. Por exemplo, a `identityProvider` alegação é definida pelos perfis técnicos da federação, como o Facebook. Se a reclamação estiver vazia, indica o utilizador a iniciar scontabilidade com uma conta local. Assim, o valor predefinido é definido para *Local.* Também pode registar uma [reclamação](claim-resolver-overview.md) com um valor contextual, como o ID da aplicação ou o endereço IP do utilizador.

### <a name="manipulating-claims"></a>Manipulação de alegações

Pode utilizar [as transformações de reclamações](custom-policy-trust-frameworks.md#manipulating-your-claims) de entrada para modificar as reclamações de entrada ou gerar novas antes de enviar para o Application Insights. No exemplo seguinte, o perfil técnico inclui a transformação de pedidos de entrada *CheckIsAdmin.* 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Adicionar eventos

Para adicionar um evento, crie um novo perfil técnico que inclua o perfil técnico *AppInsights-Common.* Em seguida, adicione o perfil técnico como passo de orquestração à jornada do [utilizador](custom-policy-trust-frameworks.md#orchestration-steps). Utilize [a condição prévia](userjourneys.md#preconditions) para ativar o evento quando desejar. Por exemplo, reporte o evento apenas quando os utilizadores passam por MFA.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Agora que tem um perfil técnico, adicione o evento à viagem do utilizador. Em seguida, renumerar os passos sequencialmente sem saltar nenhum número inteiro de 1 a N.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Ativar o modo de desenvolvimento

Ao utilizar o Application Insights para definir eventos, pode indicar se o modo de desenvolvimento está ativado. O modo de programação controla a forma como os eventos são tamponados. Num ambiente de desenvolvimento com volume mínimo de eventos, permitir que o modo de desenvolvimento resulte em eventos enviados imediatamente para a Application Insights. O valor predefinido é `false`. Não permita o modo de desenvolvimento em ambientes de produção.

Para permitir o modo de desenvolvimento, no perfil técnico *AppInsights-Common,* altere os `DeveloperMode` metadados `true` para: 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Desativar a telemetria

Para desativar os registos de insight da aplicação, no perfil técnico *AppInsights-Common,* altere os `DisableTelemetry` metadados `true` para: 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar dashboards KPI personalizados utilizando insights de aplicação Azure](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end