---
title: Acompanhe o comportamento do utilizador utilizando insights de aplicação
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
ms.openlocfilehash: 92da0b12a3119b048866eef5b18f658916595294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645930"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Acompanhe o comportamento do utilizador em Azure AD B2C utilizando insights de aplicação

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

No Azure Ative Directory B2C (Azure AD B2C), pode enviar dados de eventos diretamente para [a Application Insights](../azure-monitor/app/app-insights-overview.md) utilizando a chave de instrumentação fornecida ao Azure AD B2C. Com um perfil técnico do Application Insights, pode obter registos de eventos detalhados e personalizados para as viagens do utilizador para:

- Obtenha informações sobre o comportamento do utilizador.
- Resolva as suas próprias políticas de desenvolvimento ou produção.
- Medir o desempenho.
- Criar notificações a partir de Insights de Aplicação.

## <a name="overview"></a>Descrição Geral

Para ativar registos de eventos personalizados, adicione um perfil técnico de Insights de Aplicação. No perfil técnico, define-se a chave de instrumentação application Insights, o nome do evento e as alegações para registar. Para publicar um evento, adicione o perfil técnico como um passo de orquestração numa [jornada de utilizador](userjourneys.md).

Quando utilizar insights de aplicação, considere o seguinte:

- Há um curto atraso, normalmente menos de cinco minutos, antes de novos registos estarem disponíveis em Application Insights.
- O Azure AD B2C permite-lhe escolher quais as pretensões de gravar. Não inclua reclamações com dados pessoais.
- Para gravar uma sessão de utilizador, pode utilizar um ID de correlação para unificar eventos.
- Ligue para o perfil técnico do Application Insights diretamente de uma [viagem](userjourneys.md) de utilizador ou de uma [sub-viagem](subjourneys.md). Não utilize um perfil técnico do Application Insights como perfil técnico de [validação](validation-technical-profile.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando utilizar o Application Insights com Azure AD B2C, tudo o que precisa de fazer é criar um recurso e obter a chave de instrumentação. Para obter informações, consulte [Criar um recurso 'Insights' de Aplicação.](../azure-monitor/app/create-new-resource.md)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a utilizar o diretório que tem a sua assinatura Azure. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém a sua subscrição Azure. Este inquilino não é o seu inquilino Azure AD B2C.
1. Escolha **Criar um recurso** no canto superior esquerdo do portal Azure e, em seguida, procurar e selecionar Insights de **Aplicação**.
1. Selecione **Criar**.
1. Para **nome,** insira um nome para o recurso.
1. Para **o Tipo de Aplicação**, selecione ASP.NET **aplicação web**.
1. Para **o Grupo de Recursos,** selecione um grupo existente ou introduza um nome para um novo grupo.
1. Selecione **Criar**.
1. Abra o novo recurso Application Insights, expanda **o Essencial** e copie a chave de instrumentação.

![Screenshot que mostra a chave de instrumentação no separador visão geral de insights de aplicação.](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definir reclamações

Uma reclamação fornece armazenamento temporário de dados durante uma execução política Azure AD B2C. Declara as suas reclamações no [elemento ClaimsSchema](claimsschema.md).

1. Abra o ficheiro de extensões da sua apólice. O ficheiro pode parecer-se com `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** .
1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se não vir o elemento, adicione-o.
1. Encontre o elemento **ClaimsSchema.** Se não vir o elemento, adicione-o.
1. Adicione as seguintes reclamações ao elemento **ClaimsSchema:**

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

Os perfis técnicos podem ser considerados funções na política personalizada. Estas funções utilizam a abordagem [de inclusão de perfil técnico,](technicalprofiles.md#include-technical-profile) onde um perfil técnico inclui outro perfil técnico e altera definições ou adiciona nova funcionalidade. A tabela a seguir define os perfis técnicos que são utilizados para abrir uma sessão e pós-eventos.

| Perfil técnico | Tarefa |
| ----------------- | -----|
| AppInsights-Common | O perfil técnico comum com configuração típica. Inclui a chave de instrumentação Application Insights, uma coleção de reclamações para gravar e o modo de desenvolvimento. Os outros perfis técnicos incluem o perfil técnico comum e adicionam mais reclamações, como o nome do evento. |
| AppInsights-SignInRequest | Grava um evento **SignInRequest** com um conjunto de reclamações quando um pedido de inscrição foi recebido. |
| AppInsights-UserSignUp | Grava um evento **UserSignUp** quando o utilizador ativa a opção de inscrição numa viagem de inscrição ou de inscrição. |
| AppInsights-SignInComplete | Grava um evento **SignInComplete** após autenticação bem sucedida, quando um token foi enviado para a aplicação da parte de gestão. |

Abra o ficheiro *TrustFrameworkExtensions.xml* do pacote de arranque. Adicione os perfis técnicos ao elemento **ClaimsProvider:**

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
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

Adicione novos passos de orquestração que se refiram aos perfis técnicos.

> [!IMPORTANT]
> Depois de adicionar os novos passos de orquestração, renumerar os passos sequencialmente sem saltar nenhum número inteiro de 1 a N.

1. Chame `AppInsights-SignInRequest` como segundo passo de orquestração. Este passo acompanha que foi recebido um pedido de inscrição ou de inscrição.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Antes do `SendClaims` passo de orquestração, adicione um novo passo que chame `AppInsights-UserSignup` . É acionado quando o utilizador seleciona o botão de inscrição numa viagem de inscrição ou de inscrição.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
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

1. Depois do `SendClaims` passo de orquestração, `AppInsights-SignInComplete` ligue. Este passo mostra uma viagem concluída com sucesso.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Faça upload do seu ficheiro, execute a política e veja eventos

Guarde e carre fique *no* TrustFrameworkExtensions.xmlficheiro. Em seguida, ligue para a política do partido em gestão da sua aplicação ou use **Run Now** no portal Azure. Aguarde que os seus eventos estejam disponíveis no Application Insights.

1. Abra o recurso **Application Insights** no seu inquilino Azure Ative Directory.
1. Selecione **Utilização** e, em seguida, selecione **Eventos**.
1. Definir **durante** a **última hora** e **por** **3 minutos**. Talvez precise refrescar a janela para ver os resultados.

![Screenshot que mostra estatísticas de eventos Application Insights.](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Recolher mais dados

Para se adaptar às suas necessidades de negócio, talvez queira registar mais reclamações. Para adicionar uma reclamação, primeiro [defina uma reclamação,](#define-claims)em seguida, adicione a reclamação à coleção de reclamações de entrada. As alegações que adiciona ao perfil técnico **AppInsights-Common** aparecem em todos os eventos. As alegações que adiciona a um perfil técnico específico só aparecem nesse evento. O elemento de reclamação de entrada contém os seguintes atributos:

- **ClaimTypeReferenceId** é a referência a um tipo de reclamação.
- **PartnerClaimType** é o nome da propriedade que aparece no Azure Insights. Utilize a sintaxe `{property:NAME}` de, onde `NAME` está a ser adicionada uma propriedade ao evento.
- **DefaultValue** é um valor predefinido a ser gravado, como um nome de evento. Se uma alegação que é utilizada na viagem do utilizador estiver vazia, o valor predefinido é utilizado. Por exemplo, a `identityProvider` alegação é definida pelos perfis técnicos da federação, como o Facebook. Se a reclamação estiver vazia, indica que o utilizador inscreveu-se numa conta local. Assim, o valor predefinido é definido para **Local.** Também pode registar uma [reclamação](claim-resolver-overview.md) com um valor contextual, como o ID da aplicação ou o endereço IP do utilizador.

### <a name="manipulate-claims"></a>Manipular alegações

Pode utilizar [as transformações de pedidos](custom-policy-trust-frameworks.md#manipulating-your-claims) de entrada para modificar as reclamações de entrada ou gerar novas antes de enviá-las para o Application Insights. No exemplo seguinte, o perfil técnico inclui a transformação de `CheckIsAdmin` pedidos de entrada.

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

Para adicionar um evento, crie um novo perfil técnico que inclua o `AppInsights-Common` perfil técnico. Em seguida, adicione o novo perfil técnico como um passo de orquestração para a jornada do [utilizador](custom-policy-trust-frameworks.md#orchestration-steps). Utilize o elemento [pré-condição](userjourneys.md#preconditions) para ativar o evento quando estiver pronto. Por exemplo, reporte o evento apenas quando os utilizadores passam por autenticação multifactor.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Quando adicionar um evento à jornada do utilizador, lembre-se de renumerar os passos de orquestração sequencialmente.

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

Quando utilizar o Application Insights para definir eventos, pode indicar se o modo de desenvolvimento está ativado. O modo de programação controla a forma como os eventos são tamponados. Num ambiente de desenvolvimento com volume mínimo de eventos, permitir que o modo de desenvolvimento resulte em eventos enviados imediatamente para a Application Insights. O valor predefinido é `false`. Não permita o modo de desenvolvimento em ambientes de produção.

Para ativar o modo de desenvolvimento, altere os `DeveloperMode` metadados para `true` o `AppInsights-Common` perfil técnico:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Desativar a telemetria

Para desativar os registos de Insights de Aplicação, altere os `DisableTelemetry` metadados para `true` o `AppInsights-Common` perfil técnico:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar dashboards KPI personalizados utilizando insights de aplicação Azure](../azure-monitor/app/tutorial-app-dashboards.md).

::: zone-end