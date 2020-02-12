---
title: Acompanhe o comportamento do utilizador com insights de aplicação
titleSuffix: Azure AD B2C
description: Saiba como ativar os registos de eventos em Insights de Aplicação a partir de viagens de utilizadores Do Azure AD B2C utilizando políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 002221bc69659a3be6fee950319909c9fc63ea9c
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136314"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Acompanhe o comportamento do utilizador no Diretório Ativo Azure B2C utilizando insights de aplicação

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Quando utilizar o Azure Ative Directory B2C (Azure AD B2C) juntamente com o Azure Application Insights, pode obter registos de eventos detalhados e personalizados para as suas viagens de utilizador. Neste artigo, vai aprender a:

* Obtenha informações sobre o comportamento do utilizador.
* Problemas de suposição das suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Crie notificações a partir de Insights de Aplicação.

## <a name="how-it-works"></a>Como funciona

O Quadro de Experiência de Identidade em Azure AD B2C inclui o fornecedor `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Envia dados do evento diretamente para a Application Insights utilizando a chave de instrumentação fornecida ao Azure AD B2C.

Um perfil técnico utiliza este fornecedor para definir um evento a partir de Azure AD B2C. O perfil especifica o nome do evento, as alegações que são gravadas e a chave de instrumentação. Para publicar um evento, o perfil técnico é adicionado como um `orchestration step` numa viagem personalizada do utilizador.

Os Insights de Aplicação podem unificar os eventos usando um ID de correlação para gravar uma sessão de utilizador. Application Insights disponibiliza o evento e a sessão em segundos e apresenta muitas ferramentas de visualização, exportação e analíticas.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Get started com políticas personalizadas.](custom-policy-get-started.md) Este artigo assume que está a usar o pacote de iniciação de políticas personalizadas. Mas o pacote de arranque não é necessário.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando estiver a usar insights de aplicação com AD B2C Azure, tudo o que precisa de fazer é criar um recurso e obter a chave de instrumentação.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém a sua subscrição Azure selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém a sua subscrição. Este inquilino não é o seu inquilino Azure AD B2C.
3. Escolha **Criar um recurso** no canto superior esquerdo do portal Azure e, em seguida, procurar e selecionar Insights de **Aplicação**.
4. Clique em **Criar**.
5. Introduza um **nome** para o recurso.
6. Para o Tipo de **Aplicação,** selecione **ASP.NET aplicação web**.
7. Para **o Grupo de Recursos**, selecione um grupo existente ou introduza um nome para um novo grupo.
8. Clique em **Criar**.
4. Depois de criar o recurso Application Insights, abra-o, expanda **o Essencial**e copie a chave de instrumentação.

![Visão geral e chave de instrumentação](./media/analytics-with-application-insights/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Adicione novas definições de ClaimType

Abra o ficheiro *TrustFrameworkExtensions.xml* a partir do pacote de arranque e adicione os seguintes elementos ao elemento [BuildingBlocks:](buildingblocks.md)

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

## <a name="add-new-technical-profiles"></a>Adicione novos perfis técnicos

Os perfis técnicos podem ser considerados funções no Quadro de Experiência de Identidade do Azure AD B2C. Esta tabela define os perfis técnicos que são usados para abrir uma sessão e pós-eventos.

| Perfil Técnico | Tarefa |
| ----------------- | -----|
| AzureInsights-Common | Cria um conjunto comum de parâmetros a incluir em todos os perfis técnicos do AzureInsights. |
| AzureInsights-SignInRequest | Cria um evento SignIn com um conjunto de reclamações quando um pedido de inscrição foi recebido. |
| AzureInsights-UserSignup | Cria um evento userSignup quando o utilizador aciona a opção de inscrição numa viagem de inscrição/inscrição. |
| AzureInsights-SignInComplete | Regista a conclusão bem sucedida de uma autenticação quando um símbolo foi enviado para a aplicação da parte que depende. |

Adicione os perfis ao ficheiro *TrustFrameworkExtensions.xml* do pacote de arranque. Adicione estes elementos ao elemento **ClaimsProviders:**

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
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Altere a chave de instrumentação no perfil técnico `AzureInsights-Common` para o GUID que o seu recurso Application Insights fornece.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Adicione os perfis técnicos como passos de orquestração

Ligue `Azure-Insights-SignInRequest` como passo de orquestração 2 para acompanhar que foi recebido um pedido de inscrição/inscrição:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente *antes* do passo de orquestração `SendClaims`, adicione um novo passo que chama `Azure-Insights-UserSignup`. É acionado quando o utilizador seleciona o botão de inscrição numa viagem de inscrição/inscrição.

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

Imediatamente após o passo de orquestração `SendClaims`, chame `Azure-Insights-SignInComplete`. Este passo mostra uma viagem concluída com sucesso.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Depois de adicionar os novos passos de orquestração, renumerar os passos sequencialmente sem saltar qualquer inteiro de 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Faça upload do seu ficheiro, execute a política e veja eventos

Guarde e carregue o ficheiro *TrustFrameworkExtensions.xml.* Em seguida, ligue para a política do partido que confia na sua aplicação ou use **Run Now** no portal Azure. Em segundos, os seus eventos estão disponíveis na Application Insights.

1. Abra o recurso **Application Insights** no seu inquilino azure Ative Directory.
2. Selecione **Eventos** > **de utilização** .
3. Definido **durante** a **última hora** e **até** **3 minutos**.  Pode ser necessário selecionar **Refresh** para visualizar os resultados.

![Informações de aplicação USAGE-Eventos Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="next-steps"></a>Passos seguintes

Adicione tipos de reclamação e eventos à sua viagem de utilizador para se adaptar às suas necessidades. Pode utilizar [resolver a reclamação](claim-resolver-overview.md) ou qualquer tipo de reclamação de cordas, adicionar as reclamações adicionando um elemento **de Reclamação** de Entrada ao evento Desinformação de Aplicação ou ao perfil técnico AzureInsights-Common.

- **ClaimTypeReferenceId** é a referência a um tipo de reclamação.
- **PartnerClaimType** é o nome da propriedade que aparece no Azure Insights. Utilize a sintaxe de `{property:NAME}`, onde `NAME` é imóvel a ser adicionado ao evento.
- **DefaultValue** utilize qualquer valor de cadeia ou a reclamação resolver.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

