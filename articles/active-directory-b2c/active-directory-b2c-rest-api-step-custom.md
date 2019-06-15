---
title: REST API de afirmações trocas - Azure Active Directory B2C | Documentos da Microsoft
description: Adicione trocas de afirmações de REST API para as políticas personalizadas no Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bc0cea765816bfac066b05aca65f668fbce0c8ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508758"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Adicionar trocas de afirmações de REST API para as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode adicionar a interação com uma API RESTful para sua [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C. Este artigo mostra-lhe como criar um percurso do utilizador do Azure AD B2C que interage com os serviços RESTful.

A interação inclui uma troca de afirmações de informações entre as declarações da REST API e do Azure AD B2C. Trocas de afirmações têm as seguintes características:

- Pode ser desenvolvido como um passo de orquestração.
- Pode acionar uma ação externa. Por exemplo, ele pode registar um evento num banco de dados externo.
- Pode ser utilizado para obter um valor e, em seguida, armazene-o numa base de dados do utilizador.
- Pode alterar o fluxo de execução. 

O cenário que é representado neste artigo inclui as seguintes ações:

1. Procure o utilizador num sistema externo.
2. Obtenha a cidade em que o que o utilizador está registado.
3. Retorne esse atributo para a aplicação como uma afirmação.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua os passos na [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
- Um ponto final da REST API para interagir com. Este utiliza artigo um Azure simple funcione como um exemplo. Para criar a função do Azure, veja [criar a primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Preparar a API

Nesta secção, deve preparar-se a função do Azure para receber um valor para `email`e, em seguida, retornar o valor para `city` que podem ser utilizados pelo Azure AD B2C como uma afirmação.

Altere ficheiro Run. csx para a função do Azure que criou para utilizar o seguinte código: 

```
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

## <a name="configure-the-claims-exchange"></a>Configurar a troca de afirmações

Um perfil técnico fornece a configuração para a troca de afirmações. 

Abra o *TrustFrameworkExtensions.xml* de ficheiros e adicionar os seguintes elementos XML dentro do **ClaimsProvider** elemento.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
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

O **InputClaims** elemento define as afirmações que são enviadas para o serviço REST. Neste exemplo, o valor da afirmação `givenName` é enviado para o serviço REST como a afirmação `email`. O **OutputClaims** elemento define as afirmações que são esperadas do serviço REST.

## <a name="add-the-claim-definition"></a>Adicione a definição de afirmação

Adicione uma definição para `city` dentro do **BuildingBlocks** elemento. Pode encontrar este elemento no início do ficheiro TrustFrameworkExtensions.xml.

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

## <a name="add-an-orchestration-step"></a>Adicionar um passo de orquestração

Há muitos casos de utilização em que a chamada à REST API pode ser utilizada como um passo de orquestração. Como um passo de orquestração, ele pode ser usado como uma atualização para um sistema externo depois de um utilizador foi concluída com êxito uma tarefa, como o registo de iniciantes ou como uma atualização de perfil para manter as informações sincronizadas. Neste caso, é utilizado para aumentar as informações fornecidas para a aplicação depois de editar o perfil.

Adicione um passo para o percurso de utilizador de edição de perfil. Depois do utilizador é autenticado (passos de orquestração de 1 a 4 o seguinte XML) e o utilizador forneceu as informações de perfil atualizado (etapa 5). Copiar o perfil de editar o código XML de percurso de utilizador do *TrustFrameworkBase.xml* do ficheiro para sua *TrustFrameworkExtensions.xml* dentro do ficheiro a **UserJourneys** elemento. Em seguida, faça a modificação como passo 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

O XML final para o percurso do utilizador deve ter um aspeto semelhante a este exemplo:

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
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Adicionar a afirmação

Editar a *ProfileEdit.xml* do ficheiro e adicione `<OutputClaim ClaimTypeReferenceId="city" />` para o **OutputClaims** elemento.

Depois de adicionar a nova afirmação, o perfil técnico terá um aspeto semelhante a este exemplo:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Carregar as suas alterações e testar

1. (Opcional:) Guarde a versão existente (baixando) dos ficheiros antes de continuar.
2. Carregar o *TrustFrameworkExtensions.xml* e *ProfileEdit.xml* e selecione para substituir o ficheiro existente.
3. Selecione **B2C_1A_ProfileEdit**.
4. Para **selecione aplicativo** na página de descrição geral da política personalizada, selecione a aplicação web com o nome *webapp1* que registou anteriormente. Certifique-se de que o **URL de resposta** é `https://jwt.ms`.
4. Selecione **executar agora**. Inicie sessão com as credenciais da conta e clique em **continuar**.

Se tudo está configurado corretamente, o token inclui a nova afirmação `city`, com o valor `Redmond`.

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

- Também é possível projetar a interação como um perfil de validação. Para obter mais informações, consulte [passo a passo: Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como validação na entrada do usuário](active-directory-b2c-rest-api-validation-custom.md).
- [Modificar a edição de perfil para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
