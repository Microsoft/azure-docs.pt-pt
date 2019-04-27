---
title: REST API de afirmações trocas como passo de uma orquestração no Azure Active Directory B2C | Documentos da Microsoft
description: Um tópico no Azure Active Directory B2C políticas personalizadas que se integram com uma API.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 55740b74aef5ce3d2def5ad22cfe3ededa1204d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316889"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Descrição Passo a Passo: Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como um passo de orquestração

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Framework de experiência de identidade (IEF) que dão suporte do Azure Active Directory B2C (Azure AD B2C) permite que o desenvolvedor de identidade integrar uma interação com uma API RESTful no percurso do utilizador.  

No final destas instruções, poderá criar um percurso do utilizador do Azure AD B2C que interage com os serviços RESTful.

O IEF envia os dados em afirmações e recebe dados de volta nas afirmações. A troca de afirmações de REST API:

- Pode ser desenvolvido como um passo de orquestração.
- Pode acionar uma ação externa. Por exemplo, ele pode registar um evento num banco de dados externo.
- Pode ser utilizado para obter um valor e, em seguida, armazene-o numa base de dados do utilizador.

Pode utilizar as afirmações recebidas mais tarde para alterar o fluxo de execução.

Também é possível projetar a interação como um perfil de validação. Para obter mais informações, consulte [passo a passo: Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como validação na entrada do usuário](active-directory-b2c-rest-api-validation-custom.md).

O cenário é que quando um utilizador executa uma edição de perfil, queremos:

1. Procure o utilizador num sistema externo.
2. Obtenha a cidade em que o que o utilizador está registado.
3. Retorne esse atributo para a aplicação como uma afirmação.

## <a name="prerequisites"></a>Pré-requisitos

- Um inquilino de B2C do Azure AD configurado para concluir uma conta local sessão-inscrição/início de sessão, conforme descrito em [introdução ao](active-directory-b2c-get-started-custom.md).
- Um ponto final da REST API para interagir com. Estas instruções utilizam um webhook da aplicação de função do Azure simples como exemplo.
- *Recomendado*: Concluir o [exchange passo a passo como uma etapa de validação de afirmações de REST API](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Passo 1: Preparar a função de REST API

> [!NOTE]
> A configuração de funções de REST API está fora do escopo deste artigo. [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece um Kit de ferramentas excelente para criar serviços RESTful na cloud.

Configuramos uma função do Azure que recebe uma declaração chamada `email`e, em seguida, devolve a afirmação `city` com o valor atribuído de `Redmond`. O exemplo de função do Azure está ativada [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

O `userMessage` afirmação que devolve a função do Azure é opcional neste contexto, e o IEF irá ignorá-lo. Pode potencialmente usá-lo como uma mensagem transmitido para a aplicação e apresentadas ao usuário mais tarde.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Uma aplicação de função do Azure torna mais fácil obter o URL de função, que inclui o identificador da função específica. Neste caso, o URL é: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Pode usá-lo para fins de teste.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Passo 2: Configurar a troca de afirmações de RESTful API como um perfil técnico em seu arquivo TrustFrameworExtensions.xml

Um perfil técnico é a configuração completa do exchange pretendido com o serviço RESTful. Abra o ficheiro de TrustFrameworkExtensions.xml e adicione o seguinte fragmento XML dentro do `<ClaimsProvider>` elemento.

> [!NOTE]
> O seguinte XML, o fornecedor de RESTful `Version=1.0.0.0` é descrita como o protocolo. Considerá-lo como a função que irá interagir com o serviço externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
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

O `<InputClaims>` elemento define as afirmações que serão enviadas o IEF para o serviço REST. Neste exemplo, o conteúdo da declaração `givenName` será enviado para o serviço REST como a afirmação `email`.  

O `<OutputClaims>` elemento define as afirmações que o IEF espera do serviço REST. Independentemente do número de declarações recebidas, o IEF irá utilizar apenas os identificados aqui. Neste exemplo, uma afirmação recebidos como `city` será mapeada para um IEF declaração chamada `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Passo 3: Adicionar nova afirmação `city` ao esquema de seu arquivo TrustFrameworkExtensions.xml

A afirmação `city` ainda não foi definido em qualquer lugar no nosso esquema. Por isso, adicione uma definição de dentro do elemento `<BuildingBlocks>`. Pode encontrar este elemento no início do ficheiro TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Passo 4: Incluir a troca de afirmações de serviço do REST, como uma orquestração passo no seu percurso de utilizador de edição de perfil no TrustFrameworkExtensions.xml

Adicione um passo para o perfil editar o percurso do utilizador, depois do utilizador ser autenticado (passos de orquestração de 1 a 4 o seguinte XML) e o utilizador forneceu as informações de perfil atualizado (etapa 5).

> [!NOTE]
> Há muitos casos de utilização em que a chamada à REST API pode ser utilizada como um passo de orquestração. Como um passo de orquestração, ele pode ser usado como uma atualização para um sistema externo depois de um utilizador foi concluída com êxito uma tarefa, como o registo de iniciantes ou como uma atualização de perfil para manter as informações sincronizadas. Neste caso, é utilizado para aumentar as informações fornecidas para a aplicação depois de editar o perfil.

Copiar o perfil de código XML de percurso do utilizador do ficheiro TrustFrameworkBase.xml ao seu ficheiro TrustFrameworkExtensions.xml dentro de editar o `<UserJourneys>` elemento. Em seguida, faça a modificação no passo 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Se a ordem não corresponde à sua versão, certifique-se de que inserir o código como o passo antes do `ClaimsExchange` tipo `SendClaims`.

O XML final para o percurso do utilizador deve ter este aspeto:

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

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Passo 5: Adicionar a afirmação `city` para sua entidade confiadora política de ficheiros para que a afirmação sejam enviada para a sua aplicação

Edite o ficheiro do ProfileEdit.xml da entidade confiadora (RP) de terceiros e modificar os `<TechnicalProfile Id="PolicyProfile">` elemento para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="city" />`.

Depois de adicionar a nova afirmação, o perfil técnico fica assim:

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

## <a name="step-6-upload-your-changes-and-test"></a>Passo 6: Carregar as suas alterações e testar

Substitua as versões existentes da política.

1.  (Opcional:) Guarde a versão existente (baixando) do seu arquivo de extensões antes de continuar. Para manter a complexidade inicial baixa, recomendamos que não carregar várias versões do arquivo de extensões.
2.  (Opcional:) Mudar o nome a nova versão do ID de política para o ficheiro de edição de política, alterando `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Carregue o ficheiro de extensões.
4.  Carregue o ficheiro RP de edição de política.
5.  Uso **executar agora** para testar a política. Reveja o token que o IEF retorna ao aplicativo.

Se tudo está configurado corretamente, o token irá incluir a nova afirmação `city`, com o valor `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Passos Seguintes

[Utilize uma API REST como uma etapa de validação](active-directory-b2c-rest-api-validation-custom.md)

[Modificar a edição de perfil para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
