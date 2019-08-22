---
title: Trocas de declarações da API REST como validação no Azure Active Directory B2C
description: Uma explicação sobre como criar uma jornada de usuário Azure AD B2C que interage com os serviços RESTful.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 45fad1fab419c448febb3f3b760996fba278e154
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644978"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Descrição Passo a Passo: Integre as trocas de declarações da API REST no percurso do usuário Azure AD B2C como validação na entrada do usuário

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A IEF (estrutura de experiência de identidade) que se baseia Azure Active Directory B2C (Azure AD B2C) permite que o desenvolvedor de identidade integre uma interação com uma API RESTful em um percurso do usuário.

No final deste passo a passo, você poderá criar uma jornada de usuário Azure AD B2C que interage com os serviços RESTful.

O IEF envia dados em declarações e recebe dados de volta em declarações. A interação com a API:

- Pode ser projetado como uma troca de declarações da API REST ou como um perfil de validação, que ocorre dentro de uma etapa de orquestração.
- Normalmente valida a entrada do usuário. Se o valor do usuário for rejeitado, o usuário poderá tentar novamente inserir um valor válido com a oportunidade de retornar uma mensagem de erro.

Você também pode criar a interação como uma etapa de orquestração. Para obter mais informações, [consulte Walkthrough: Integre as trocas de declarações da API REST na sua jornada do usuário Azure AD B2C como uma](active-directory-b2c-rest-api-step-custom.md)etapa de orquestração.

Para o exemplo de perfil de validação, usaremos o percurso do usuário de edição de perfil no arquivo do pacote inicial ProfileEdit. xml.

Podemos verificar se o nome fornecido pelo usuário na edição de perfil não faz parte de uma lista de exclusão.

## <a name="prerequisites"></a>Pré-requisitos

- Um locatário Azure AD B2C configurado para concluir uma inscrição/entrada de conta local, conforme descrito em [introdução](active-directory-b2c-get-started-custom.md).
- Um ponto de extremidade de API REST com o qual interagir. Para este passo a passo, configuramos um site de demonstração chamado [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) com um serviço de API REST.

## <a name="step-1-prepare-the-rest-api-function"></a>Passo 1: Preparar a função da API REST

> [!NOTE]
> A configuração das funções da API REST está fora do escopo deste artigo. O [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece um kit de ferramentas excelente para criar serviços RESTful na nuvem.

Criamos uma função do Azure que recebe uma declaração esperada como `playerTag`. A função valida se essa declaração existe. Você pode acessar o código completo de função do Azure no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

O IEF espera a `userMessage` declaração que a função do Azure retorna. Essa declaração será apresentada como uma cadeia de caracteres para o usuário se a validação falhar, como quando um status de conflito 409 é retornado no exemplo anterior.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Passo 2: Configurar a troca de declarações da API RESTful como um perfil técnico no arquivo TrustFrameworkExtensions. xml

Um perfil técnico é a configuração completa do Exchange desejado com o serviço RESTful. Abra o arquivo TrustFrameworkExtensions. xml e adicione o trecho de código XML a `<ClaimsProviders>` seguir dentro do elemento.

> [!NOTE]
> No XML a seguir, o provedor `Version=1.0.0.0` RESTful é descrito como o protocolo. Considere-o como a função que irá interagir com o serviço externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

O `InputClaims` elemento define as declarações que serão enviadas do IEF para o serviço REST. Neste exemplo, o conteúdo da Declaração `givenName` será enviado para o serviço REST como. `playerTag` Neste exemplo, o IEF não espera declarações de volta. Em vez disso, ele aguarda uma resposta do serviço REST e age com base nos códigos de status que recebe.

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que você deve fazer ao mudar para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, consulte [proteger APIs RESTful com autenticação básica](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) e [proteger APIs RESTful com autenticação de certificado](active-directory-b2c-custom-rest-api-netfw-secure-cert.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Passo 3: Incluir a troca de declarações do serviço RESTful em um perfil técnico autodeclarado no qual você deseja validar a entrada do usuário

O uso mais comum da etapa de validação está na interação com um usuário. Todas as interações em que o usuário deve fornecer entrada são *perfis técnicos*autodeclarados. Para este exemplo, adicionaremos a validação ao perfil técnico autodeclarado-ProfileUpdate. Esse é o perfil técnico usado pelo arquivo `Profile Edit` de política RP (terceira parte confiável).

Para adicionar a troca de declarações ao perfil técnico autodeclarado:

1. Abra o arquivo TrustFrameworkBase. xml e procure `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Examine a configuração deste perfil técnico. Observe como a troca com o usuário é definida como declarações que serão solicitadas pelo usuário (declarações de entrada) e pelas declarações que serão esperadas de volta do provedor autodeclarado (declarações de saída).
3. Pesquise e observe que esse perfil é invocado como a etapa 5 da orquestração `<UserJourney Id="ProfileEdit">`de. `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Passo 4: Carregar e testar o arquivo de política do RP de edição de perfil

1. Carregue a nova versão do arquivo TrustFrameworkExtensions. xml.
2. Use **executar agora** para testar o arquivo de política do RP de edição de perfil.
3. Teste a validação fornecendo um dos nomes existentes (por exemplo, mcvinny) no campo **nome fornecido** . Se tudo estiver configurado corretamente, você deverá receber uma mensagem notificando o usuário que a marca do jogador já está sendo usada.

## <a name="next-steps"></a>Passos seguintes

[Modifique a edição de perfil e o registro de usuário para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Mostrou Integre as trocas de declarações da API REST na sua jornada do usuário Azure AD B2C como uma etapa de orquestração](active-directory-b2c-rest-api-step-custom.md)

[Referência Perfil técnico RESTful](restful-technical-profile.md)

Para saber como proteger suas APIs, consulte os seguintes artigos:

* [Proteger sua API RESTful com autenticação básica (nome de usuário e senha)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Proteger sua API RESTful com certificados de cliente](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
