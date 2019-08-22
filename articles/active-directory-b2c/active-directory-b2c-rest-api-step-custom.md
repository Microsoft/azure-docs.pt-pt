---
title: Trocas de declarações da API REST-Azure Active Directory B2C
description: Adicione trocas de declarações da API REST a políticas personalizadas no Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42129870c6ab2bb5e58bdf9aaa323a3d64b479f8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644911"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Adicionar trocas de declarações da API REST a políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Você pode adicionar interação com uma API RESTful às suas [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C. Este artigo mostra como criar uma jornada de usuário Azure AD B2C que interage com os serviços RESTful.

A interação inclui uma troca de declarações de informações entre as declarações da API REST e a Azure AD B2C. As trocas de declarações têm as seguintes características:

- Pode ser projetado como uma etapa de orquestração.
- Pode disparar uma ação externa. Por exemplo, ele pode registrar um evento em um banco de dados externo.
- Pode ser usado para buscar um valor e, em seguida, armazená-lo no banco de dados do usuário.
- Pode alterar o fluxo de execução.

O cenário representado neste artigo inclui as seguintes ações:

1. Pesquisar o usuário em um sistema externo.
2. Obtenha a cidade onde o usuário está registrado.
3. Retorne esse atributo para o aplicativo como uma declaração.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em introdução [às políticas personalizadas](active-directory-b2c-get-started-custom.md).
- Um ponto de extremidade de API REST com o qual interagir. Este artigo usa uma função simples do Azure como um exemplo. Para criar a função do Azure, consulte [criar sua primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Preparar a API

Nesta seção, você prepara a função do Azure para receber um valor de `email`e, em seguida, retorna o `city` valor para que pode ser usado pelo Azure ad B2C como uma declaração.

Altere o arquivo run. CSX para a função do Azure que você criou para usar o seguinte código:

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

## <a name="configure-the-claims-exchange"></a>Configurar a troca de declarações

Um perfil técnico fornece a configuração para a troca de declarações.

Abra o arquivo *TrustFrameworkExtensions. xml* e adicione o seguinte elemento XML claimprovider dentro do elemento **ClaimsProviders** .

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

O elemento **InputClaims** define as declarações que são enviadas para o serviço REST. Neste exemplo, o valor da Declaração `givenName` é enviado para o serviço REST como a declaração. `email` O elemento **OutputClaims** define as declarações que são esperadas do serviço REST.

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que você deve fazer ao mudar para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, consulte [proteger APIs RESTful com autenticação básica](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) e [proteger APIs RESTful com autenticação de certificado](active-directory-b2c-custom-rest-api-netfw-secure-cert.md).

## <a name="add-the-claim-definition"></a>Adicionar a definição de declaração

Adicione uma definição para `city` dentro do elemento **BuildingBlocks** . Você pode encontrar esse elemento no início do arquivo TrustFrameworkExtensions. xml.

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

## <a name="add-an-orchestration-step"></a>Adicionar uma etapa de orquestração

Há muitos casos de uso em que a chamada à API REST pode ser usada como uma etapa de orquestração. Como uma etapa de orquestração, ela pode ser usada como uma atualização para um sistema externo depois que um usuário tiver concluído com êxito uma tarefa como o registro inicial, ou como uma atualização de perfil para manter as informações sincronizadas. Nesse caso, ele é usado para aumentar as informações fornecidas ao aplicativo após a edição do perfil.

Adicione uma etapa à jornada do usuário de edição de perfil. Depois que o usuário é autenticado (etapas de orquestração 1-4 no XML a seguir), e o usuário forneceu as informações de perfil atualizadas (etapa 5). Copie o código XML de jornada do usuário de edição de perfil do arquivo *TrustFrameworkBase. xml* para o arquivo *TrustFrameworkExtensions. xml* dentro do elemento userjornadas. Em seguida, faça a modificação como etapa 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

O XML final da jornada do usuário deve ser semelhante a este exemplo:

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

## <a name="add-the-claim"></a>Adicionar a declaração

Edite o arquivo *ProfileEdit. xml* e `<OutputClaim ClaimTypeReferenceId="city" />` adicione ao elemento **OutputClaims** .

Depois de adicionar a nova declaração, o perfil técnico é semelhante a este exemplo:

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

## <a name="upload-your-changes-and-test"></a>Carregue suas alterações e teste

1. Adicional Salve a versão existente (baixando) dos arquivos antes de continuar.
2. Carregue o *TrustFrameworkExtensions. xml* e o *ProfileEdit. xml* e selecione para substituir o arquivo existente.
3. Selecione **B2C_1A_ProfileEdit**.
4. Para **Selecionar aplicativo** na página Visão geral da política personalizada, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente. Verifique se a **URL de resposta** é `https://jwt.ms`.
4. Selecione **executar agora**. Entre com suas credenciais de conta e clique em **continuar**.

Se tudo estiver configurado corretamente, o token incluirá a nova declaração `city`, com o valor `Redmond`.

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

## <a name="next-steps"></a>Passos Seguintes

Você também pode criar a interação como um perfil de validação. Para obter mais informações, [consulte Walkthrough: Integre as trocas de declarações da API REST em sua jornada do usuário Azure AD B2C como validação](active-directory-b2c-rest-api-validation-custom.md)na entrada do usuário.

[Modificar a edição de perfil para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Referência Perfil técnico RESTful](restful-technical-profile.md)

Para saber como proteger suas APIs, consulte os seguintes artigos:

* [Proteger sua API RESTful com autenticação básica (nome de usuário e senha)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Proteger sua API RESTful com certificados de cliente](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
