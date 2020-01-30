---
title: Rest API reclama trocas como validação
titleSuffix: Azure AD B2C
description: Um passeio para criar uma viagem de utilizador Azure AD B2C que interage com serviços RESTful.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 209cddcc8648f7d396f048057bb4367e2ae9f66c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846617"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Walkthrough: Integrar rest API reclama trocas de créditos na sua viagem de utilizador Azure AD B2C como validação na entrada do utilizador

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Quadro de Experiência de Identidade (IEF) que está subjacente ao Diretório Ativo Azure B2C (Azure AD B2C) permite ao desenvolvedor de identidade integrar uma interação com uma API RESTful numa viagem de utilizador.

No final desta passagem, poderá criar uma viagem de utilizador Azure AD AD B2C que interage com serviços RESTful.

O IEF envia dados em sinistros e recebe dados em sinistros. A interação com a API:

- Pode ser projetado como uma troca de reclamações REST API ou como um perfil de validação, que acontece dentro de um passo de orquestração.
- Normalmente valida a entrada do utilizador. Se o valor do utilizador for rejeitado, o utilizador poderá tentar novamente introduzir um valor válido com a oportunidade de devolver uma mensagem de erro.

Você também pode projetar a interação como um passo de orquestração. Para mais informações, consulte [Walkthrough: Integrar rest API reclama trocas de reclamações na sua viagem de utilizador Azure AD B2C como passo de orquestração](custom-policy-rest-api-claims-exchange.md).

Para o exemplo do perfil de validação, utilizaremos a viagem de utilizador de edição de perfil no ficheiro de arranque ProfileEdit.xml.

Podemos verificar se o nome fornecido pelo utilizador na edição de perfil não faz parte de uma lista de exclusão.

## <a name="prerequisites"></a>Pré-requisitos

- Um inquilino Azure AD B2C configurado para completar um sinal de inscrição/inscrição de conta local, como descrito em [Getting started](custom-policy-get-started.md).
- Um ponto final da API REST para interagir. Para este walkthrough, configuramos um site de demonstração chamado [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) com um serviço REST API.

## <a name="step-1-prepare-the-rest-api-function"></a>Passo 1: Preparar a função REST API

> [!NOTE]
> A configuração das funções REST API está fora do âmbito deste artigo. [A Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece um excelente kit de ferramentas para criar serviços RESTful na nuvem.

Criámos uma função Azure que recebe uma reivindicação que espera como `playerTag`. A função valida se esta alegação existe. Pode aceder ao código de função Completo Azure no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

O IEF espera que o `userMessage` afirme que a função Azure regressa. Esta alegação será apresentada como uma corda ao utilizador se a validação falhar, como quando um estado de conflito 409 é devolvido no exemplo anterior.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Passo 2: Configure a troca de reclamações restful API como um perfil técnico no seu ficheiro TrustFrameworkExtensions.xml

Um perfil técnico é a configuração completa da troca desejada com o serviço RESTful. Abra o ficheiro TrustFrameworkExtensions.xml e adicione o seguinte corte XML dentro do elemento `<ClaimsProviders>`.

> [!NOTE]
> No xml seguinte, o fornecedor RESTful `Version=1.0.0.0` é descrito como o protocolo. Considere-o como a função que irá interagir com o serviço externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

O elemento `InputClaims` define as reclamações que serão enviadas do IEF para o serviço REST. Neste exemplo, o conteúdo da reclamação `givenName` será enviado para o serviço REST como `playerTag`. Neste exemplo, o IEF não espera reclamações de volta. Em vez disso, aguarda uma resposta do serviço REST e atua com base nos códigos de estado que recebe.

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que você deve fazer ao mudar para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, consulte [proteger APIs RESTful com autenticação básica](secure-rest-api-dotnet-basic-auth.md) e [proteger APIs RESTful com autenticação de certificado](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Passo 3: Incluir a troca de reclamações de serviço RESTful no perfil técnico autoafirmado onde pretende validar a entrada do utilizador

O uso mais comum do passo de validação está na interação com um utilizador. Todas as interações em que se espera que o utilizador forneça entrada são *perfis técnicos autoafirmados*. Para este exemplo, adicionaremos a validação ao perfil técnico Auto-Afirmado-ProfileUpdate. Este é o perfil técnico que o ficheiro político do partido que confia (RP) `Profile Edit` utiliza.

Para adicionar a troca de reclamações ao perfil técnico autoafirmado:

1. Abra o ficheiro TrustFrameworkBase.xml e procure `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Reveja a configuração deste perfil técnico. Observe como a troca com o utilizador é definida como alegações que serão solicitadas ao utilizador (pedidos de entrada) e alegações que serão esperadas de volta do fornecedor autoafirmado (alegações de saída).
3. Procure `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`, e note que este perfil é invocado como passo de orquestração 5 de `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Passo 4: Carregar e testar o ficheiro de política de rP de edição de perfil

1. Faça upload da nova versão do ficheiro TrustFrameworkExtensions.xml.
2. Use **executar agora** para testar o ficheiro de política de RP de edição de perfil.
3. Teste a validação fornecendo um dos nomes existentes (por exemplo, mcvinny) no campo **Nome Dado.** Se tudo estiver configurado corretamente, deve receber uma mensagem que notifique ao utilizador que a etiqueta do leitor já está utilizada.

## <a name="next-steps"></a>Passos seguintes

[Modificar a edição de perfil e o registo do utilizador para recolher informações adicionais dos seus utilizadores](custom-policy-custom-attributes.md)

[Walkthrough: Integrar rest API reclama trocas na sua jornada de utilizador Azure AD B2C como passo de orquestração](custom-policy-rest-api-claims-exchange.md)

[Referência: Perfil técnico RESTful](restful-technical-profile.md)

Para saber como proteger suas APIs, consulte os seguintes artigos:

* [Proteger sua API RESTful com autenticação básica (nome de usuário e senha)](secure-rest-api-dotnet-basic-auth.md)
* [Proteger sua API RESTful com certificados de cliente](secure-rest-api-dotnet-certificate-auth.md)
