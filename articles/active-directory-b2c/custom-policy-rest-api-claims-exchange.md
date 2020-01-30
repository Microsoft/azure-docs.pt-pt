---
title: REST API reclama trocas - Azure Ative Directory B2C
description: Adicione as trocas de reclamações rest API a políticas personalizadas no Diretório Ativo B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1caf0b3f6396cc008e77bb96e686fe78f462850b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849100"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Adicionar REST API reclama trocas de pedidos a políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode adicionar interação com uma API RESTful às suas [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C). Este artigo mostra-lhe como criar uma viagem de utilizador Azure AD AD B2C que interage com serviços RESTful.

A interação inclui uma troca de reclamações de informações entre as alegações da API REST e o Azure AD B2C. As trocas de sinistros têm as seguintes características:

- Pode ser projetado como um passo de orquestração.
- Pode desencadear uma ação externa. Por exemplo, pode registar um evento numa base de dados externa.
- Pode ser usado para obter um valor e depois armazená-lo na base de dados do utilizador.
- Pode mudar o fluxo de execução.

O cenário que está representado neste artigo inclui as seguintes ações:

1. Procure o utilizador num sistema externo.
2. Pegue a cidade onde o utilizador está registado.
3. Devolver o atributo ao pedido como reclamação.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em introdução [às políticas personalizadas](custom-policy-get-started.md).
- Um ponto final da API REST para interagir. Este artigo usa uma função Azure simples como exemplo. Para criar a função Azure, consulte [Criar a sua primeira função no portal Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Preparar a API

Nesta secção, prepara-se a função Azure para receber um valor para `email`, e depois devolve o valor para `city` que pode ser usado pelo Azure AD B2C como reivindicação.

Altere o ficheiro run.csx para a função Azure que criou para utilizar o seguinte código:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Configure a troca de sinistros

Um perfil técnico fornece a configuração para a troca de reclamações.

Abra o ficheiro *TrustFrameworkExtensions.xml* e adicione o seguinte elemento **ClaimsProvider** XML dentro do elemento **ClaimsProviders.**

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

O elemento **InputClaims** define as reclamações que são enviadas para o serviço REST. Neste exemplo, o valor da reclamação `givenName` é enviado para o serviço REST à medida que a reclamação `email`. O elemento **OutputClaims** define as reclamações que são esperadas do serviço REST.

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que você deve fazer ao mudar para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, consulte [proteger APIs RESTful com autenticação básica](secure-rest-api-dotnet-basic-auth.md) e [proteger APIs RESTful com autenticação de certificado](secure-rest-api-dotnet-certificate-auth.md).

## <a name="add-the-claim-definition"></a>Adicione a definição de reclamação

Adicione uma definição para `city` dentro do elemento **BuildingBlocks.** Pode encontrar este elemento no início do ficheiro TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Adicione um passo de orquestração

Há muitos casos de uso em que a chamada REST API pode ser usada como um passo de orquestração. Como passo de orquestração, pode ser usado como uma atualização para um sistema externo depois de um utilizador ter concluído com sucesso uma tarefa como registo pela primeira vez, ou como uma atualização de perfil para manter a informação sincronizada. Neste caso, é usado para aumentar a informação fornecida à aplicação após a edição do perfil.

Adicione um passo para a viagem de utilizador de edição de perfil. Depois de o utilizador ser autenticado (passos de orquestração 1-4 no XML seguinte), e o utilizador forneceu as informações atualizadas do perfil (passo 5). Copie o código XML da viagem do utilizador do ficheiro *TrustFrameworkBase.xml* para o ficheiro *TrustFrameworkExtensions.xml* dentro do elemento **UserJourneys.** Em seguida, faça a modificação como passo 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

O XML final para a viagem do utilizador deve parecer este exemplo:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Adicione a reclamação

Editar o ficheiro *ProfileEdit.xml* e adicionar `<OutputClaim ClaimTypeReferenceId="city" />` ao elemento **OutputClaims.**

Depois de adicionar a nova reclamação, o perfil técnico parece este exemplo:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Faça upload das suas alterações e teste

1. (Opcional:) Guarde a versão existente (transferindo) os ficheiros antes de proceder.
2. Faça upload das *Extensões TrustFramework.xml* e *ProfileEdit.xml* e selecione para substituir o ficheiro existente.
3. Selecione **B2C_1A_ProfileEdit**.
4. Para **Selecionar aplicativo** na página Visão geral da política personalizada, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente. Verifique se a **URL de resposta** está `https://jwt.ms`.
4. Selecione **Executar agora**. Inscreva-se com as credenciais da sua conta e clique em **Continuar**.

Se tudo estiver corretamente configurado, o símbolo inclui a nova reclamação `city`, com o valor `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Passos seguintes

Também pode projetar a interação como um perfil de validação. Para mais informações, consulte Walkthrough: Integrar as trocas de reclamações da [Rest API na sua viagem de utilizador Azure AD B2C como validação na entrada](custom-policy-rest-api-claims-validation.md)do utilizador .

[Modificar a edição de perfil para recolher informações adicionais dos seus utilizadores](custom-policy-custom-attributes.md)

[Referência: Perfil técnico RESTful](restful-technical-profile.md)

Para aprender como proteger as suas APIs, consulte os seguintes artigos:

* [Proteger sua API RESTful com autenticação básica (nome de usuário e senha)](secure-rest-api-dotnet-basic-auth.md)
* [Proteger sua API RESTful com certificados de cliente](secure-rest-api-dotnet-certificate-auth.md)
