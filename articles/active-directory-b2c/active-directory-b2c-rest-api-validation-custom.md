---
title: REST API trocas de afirmações como validação no Azure Active Directory B2C | Documentos da Microsoft
description: Um tópico em políticas personalizadas do Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0779e4a93230a90b8eee76f1898154c1a5b82661
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508733"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Descrição Passo a Passo: Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como validação na entrada do usuário

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Framework de experiência de identidade (IEF) que dão suporte do Azure Active Directory B2C (Azure AD B2C) permite que o desenvolvedor de identidade integrar uma interação com uma API RESTful no percurso do utilizador.  

No final destas instruções, poderá criar um percurso do utilizador do Azure AD B2C que interage com os serviços RESTful.

O IEF envia os dados em afirmações e recebe dados de volta nas afirmações. A interação com a API:

- Pode ser criado como uma troca de afirmações de REST API ou como um perfil de validação, o que acontece dentro de um passo de orquestração.
- Normalmente, valida a entrada do usuário. Se o valor do usuário for rejeitado, o utilizador pode tentar novamente para introduzir um valor válido com a oportunidade de retornar uma mensagem de erro.

Também é possível projetar a interação como um passo de orquestração. Para obter mais informações, consulte [passo a passo: Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como um passo de orquestração](active-directory-b2c-rest-api-step-custom.md).

No exemplo de perfil de validação, utilizaremos o percurso de utilizador de edição de perfil no ficheiro de pacote de iniciação ProfileEdit.xml.

Podemos verificar de que o nome fornecido pelo usuário na edição de perfil não faz parte de uma lista de exclusão.

## <a name="prerequisites"></a>Pré-requisitos

- Um inquilino de B2C do Azure AD configurado para concluir uma conta local sessão-inscrição/início de sessão, conforme descrito em [introdução ao](active-directory-b2c-get-started-custom.md).
- Um ponto final da REST API para interagir com. Nestas instruções, configuramos um site de demonstração chamado [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) com um serviço de REST API.

## <a name="step-1-prepare-the-rest-api-function"></a>Passo 1: Preparar a função de REST API

> [!NOTE]
> A configuração de funções de REST API está fora do escopo deste artigo. [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece um Kit de ferramentas excelente para criar serviços RESTful na cloud.

Nós acabamos de criar uma função do Azure que recebe uma afirmação que ele espera como `playerTag`. A função valida se esta afirmação existe. Pode acessar o código de função do Azure completa na [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

O IEF espera que o `userMessage` afirmação que devolve a função do Azure. Essa declaração será apresentada como uma cadeia de caracteres para o usuário se a validação falhar, por exemplo, quando é devolvido um Estado de 409 conflito no exemplo anterior.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Passo 2: Configurar a troca de afirmações de RESTful API como um perfil técnico em seu arquivo TrustFrameworkExtensions.xml

Um perfil técnico é a configuração completa do exchange pretendido com o serviço RESTful. Abra o ficheiro de TrustFrameworkExtensions.xml e adicione o seguinte fragmento XML dentro do `<ClaimsProviders>` elemento.

> [!NOTE]
> O seguinte XML, o fornecedor de RESTful `Version=1.0.0.0` é descrita como o protocolo. Considerá-lo como a função que irá interagir com o serviço externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
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

O `InputClaims` elemento define as afirmações que serão enviadas o IEF para o serviço REST. Neste exemplo, o conteúdo da declaração `givenName` será enviado para o serviço REST como `playerTag`. Neste exemplo, o IEF não espera que as afirmações de volta. Em vez disso, ele aguarda uma resposta do serviço do REST e age com base nos códigos de estado que receber.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Passo 3: Incluir a troca de afirmações de serviço RESTful no perfil técnico declaração própria em que pretende validar o intervenção do utilizador

O uso mais comum da etapa de validação é na interação com um utilizador. Todas as interações em que o utilizador é esperado para fornecer entradas são *autodeclarativas perfis técnicos*. Neste exemplo, vamos adicionar a validação para o perfil técnico Self Asserted ProfileUpdate. Esta é a técnica de perfil que o ficheiro de política da entidade confiadora (RP) de terceiros `Profile Edit` utiliza.

Para adicionar a troca de afirmações para o perfil técnico de declaração própria:

1. Abra o ficheiro de TrustFrameworkBase.xml e procure `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Reveja a configuração deste perfil técnico. Observe como o exchange com o utilizador é definido como afirmações que serão pedidas do utilizador (afirmações de entrada) e de afirmações que se espera volta do fornecedor de declaração própria (afirmações de saída).
3. Procure `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`e tenha em atenção que este perfil é invocado como passo de orquestração 5 de `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Passo 4: Carregar e testar o ficheiro de política RP de edição de perfil

1. Carregar a nova versão do ficheiro TrustFrameworkExtensions.xml.
2. Uso **executar agora** para testar o ficheiro de política RP de edição de perfil.
3. Testar a validação, fornecendo um dos nomes existentes (por exemplo, mcvinny) a **determinado nome** campo. Se tudo está configurado corretamente, deverá receber uma mensagem que notifica o utilizador que a marca de player já está a ser utilizada.

## <a name="next-steps"></a>Passos Seguintes

[Modificar o registo de utilizador e Editar perfil para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Passo a passo: Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como um passo de orquestração](active-directory-b2c-rest-api-step-custom.md)
