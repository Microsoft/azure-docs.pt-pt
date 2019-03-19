---
title: Integrar trocas de afirmação de REST API no seu percurso de utilizador do Azure Active Directory B2C | Documentos da Microsoft
description: Integre trocas de afirmação de REST API no seu percurso do utilizador do Azure AD B2C como validação de entrada do usuário.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: bc2e41fd5da4737ea1efe329b70964535daff54a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105969"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como validação de entrada do usuário

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Com o Framework de experiência de identidade, que dão suporte para Azure Active Directory B2C (Azure AD B2C), pode integrar com uma API RESTful no percurso do utilizador. Nestas instruções, irá aprender como o Azure AD B2C interage com os serviços RESTful do .NET Framework (web API).

## <a name="introduction"></a>Introdução
Ao utilizar o Azure AD B2C, pode adicionar sua própria lógica de negócio para um percurso do utilizador ao chamar o seu próprio serviço RESTful. O Framework de experiência de identidade envia dados para o serviço RESTful numa *afirmações de entrada* coleção e recebe dados de volta de RESTful num *declarações de saída* coleção. Com a integração de serviço RESTful, pode:

* **Validar dados de entrada do usuário**: Esta ação impede que os dados mal formados persistência com o Azure AD. Se o valor do usuário não é válido, o seu serviço RESTful devolve uma mensagem de erro que indica ao utilizador para fornecer uma entrada. Por exemplo, pode verificar que o endereço de e-mail fornecido pelo utilizador existe na base de dados do seu cliente.
* **Substituir afirmações de entrada**: Por exemplo, se um utilizador introduz o nome em letras minúsculas ou todas as letras maiúsculas, pode formatar o nome com a primeira letra em maiúsculas.
* **Enriquecer os dados de utilizador com a integração mais com aplicações de linha de negócio empresariais**: O serviço RESTful pode receber o endereço de e-mail do utilizador, consultar base de dados do cliente e retornar o número de fidelização do utilizador para o Azure AD B2C. O retorno de afirmações podem ser armazenados em que a conta de utilizador do Azure AD, avaliada nos próximos *etapas da orquestração*, ou incluídas no token de acesso.
* **Executar a lógica de negócio personalizada**: Pode enviar notificações push, Atualizar bancos de dados corporativos, executar um processo de migração de utilizador, gerir as permissões, bases de dados de auditoria e efetuar outras ações.

Pode projetar a integração com os serviços RESTful das seguintes formas:

* **Perfil de técnica de validação**: A chamada para o serviço RESTful ocorre no prazo do perfil técnico de validação do perfil técnico especificado. O perfil técnico de validação valida os dados fornecidos pelo usuário antes do percurso do utilizador avança. Com o perfil técnico de validação, pode:
   * Envie afirmações de entrada.
   * Validar afirmações de entrada e gerar mensagens de erro personalizadas.
   * Envie afirmações de saída anterior.

* **Afirmações do exchange**: Esse design é semelhante para o perfil técnico de validação, mas acontece dentro de um passo de orquestração. Esta definição está limitada a:
   * Envie afirmações de entrada.
   * Envie afirmações de saída anterior.

## <a name="restful-walkthrough"></a>Passo a passo rESTful
Este passo a passo, vai desenvolver uma API que valida a entrada do usuário e oferece uma série de fidelidade de usuário de web do .NET Framework. Por exemplo, seu aplicativo pode conceder acesso a *benefícios platinum* com base no número de fidelização.

Descrição geral:
* Desenvolva o serviço RESTful (web API do .NET Framework).
* Utilize o serviço RESTful no percurso do utilizador.
* Enviar afirmações de entrada e lê-los em seu código.
* Valide o nome próprio do utilizador.
* Envie de volta um número de fidelização.
* Adicione o número de fidelização para um JSON Web Token (JWT).

## <a name="prerequisites"></a>Pré-requisitos
Conclua os passos a [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

## <a name="step-1-create-an-aspnet-web-api"></a>Passo 1: Criar uma API web ASP.NET

1. No Visual Studio, crie um projeto, selecionando **arquivo** > **New** > **projeto**.

2. Na **novo projeto** janela, selecione **Visual c#** > **Web** > **aplicação Web ASP.NET (.NET Framework)**.

3. Na **Name** , escreva um nome para a aplicação (por exemplo, *Contoso.AADB2C.API*) e, em seguida, selecione **OK**.

    ![Criar novo projeto do visual studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. Na **nova aplicação Web ASP.NET** janela, selecione um **Web API** ou **aplicação de API do Azure** modelo.

    ![Selecione o modelo de API web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Certifique-se de que a autenticação está definida como **sem autenticação**.

6. Selecione **OK** para criar o projeto.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Passo 2: Preparar o ponto final de REST API

### <a name="step-21-add-data-models"></a>Passo 2.1: Adicionar modelos de dados
Os modelos representam as afirmações de entrada e saída de dados no serviço RESTful afirmações. Seu código lê os dados de entrada por anular a serialização o modelo de afirmações de entrada de uma cadeia de caracteres do JSON para um objeto do c# (modelo). A API web ASP.NET automaticamente desserializa o modelo de afirmações de saída para JSON e, em seguida, escreve os dados serializados para o corpo da mensagem de resposta HTTP.

Crie um modelo que representa as declarações de entrada, fazendo o seguinte:

1. Se o Explorador de soluções ainda não estiver aberto, selecione **View** > **Explorador de soluções**.
2. No Explorador de Soluções, clique com o botão direito do rato na pasta **Modelos**, selecione **Adicionar** e, em seguida, selecione **Classe**.

    ![Adicionar o modelo](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Nome da classe `InputClaimsModel`e, em seguida, adicione as seguintes propriedades para o `InputClaimsModel` classe:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Criar um novo modelo `OutputClaimsModel`e, em seguida, adicione as seguintes propriedades para o `OutputClaimsModel` classe:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Criar um modelo mais, `B2CResponseContent`, que utilizar para gerar mensagens de erro de validação de entrada. Adicione as propriedades seguintes para o `B2CResponseContent` de classe, forneça as referências em falta e, em seguida, guarde o ficheiro:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Passo 2.2: Adicionar um controlador
Na API, web uma _controlador_ é um objeto que processa os pedidos HTTP. O controlador devolve as afirmações de saída ou, se o nome não é válido, emite uma mensagem de erro de HTTP de conflito.

1. No Explorador de Soluções, clique com o botão direito do rato na pasta **Controladores**, selecione **Adicionar** e, em seguida, selecione **Controlador**.

    ![Adicionar novo controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. Na **Adicionar estrutura** janela, selecione **Web API Controller - vazia**e, em seguida, selecione **Add**.

    ![Vazio do selecione Web API 2 Controller-](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. Na **Add Controller** janela, o nome do controlador **IdentityController**e, em seguida, selecione **Add**.

    ![Escreva o nome do controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    O scaffolding cria um ficheiro denominado *IdentityController.cs* no *controladores* pasta.

4. Se o *IdentityController.cs* ficheiro já não está aberto, clique duas vezes nele e, em seguida, substitua o código no ficheiro com o código a seguir:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Passo 3: Publicar o projeto no Azure
1. No Solution Explorer, clique com botão direito a **Contoso.AADB2C.API** projeto e, em seguida, selecione **Publish**.

    ![Publicar no serviço de aplicações do Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. Na **Publish** janela, selecione **serviço de aplicações do Microsoft Azure**e, em seguida, selecione **publicar**.

    ![Criar novo serviço de aplicações do Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    O **criar serviço de aplicações** é aberta a janela. Aqui, cria todos os recursos do Azure necessários para executar a sua aplicação web ASP.NET no Azure.

    > [!NOTE]
    >Para obter mais informações sobre como publicar, consulte [criar uma aplicação web ASP.NET no Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. Na **nome da aplicação Web** , escreva um nome de aplicação exclusivo (carateres válidos são a-z, 0-9 e hífenes (-). O URL da aplicação web é http://<APP_NAME>.azurewebsites.NET, onde *app_name* é o nome da sua aplicação web. Também pode aceitar o nome gerado automaticamente, que já é exclusivo.

    ![Forneça as propriedades do serviço de aplicações](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Para começar a criar recursos do Azure, selecione **criar**.  
    Depois de ter sido criada a sua aplicação web ASP.NET, o assistente publica-o para o Azure e, em seguida, inicia a aplicação no browser predefinido.

6. Copie o URL da aplicação web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Passo 4: Adicionar a nova `loyaltyNumber` de afirmação para o esquema do seu ficheiro TrustFrameworkExtensions.xml
O `loyaltyNumber` afirmação ainda não foi definida no nosso esquema. Adicionar uma definição de dentro do `<BuildingBlocks>` elemento, que pode ser encontrado no início do *TrustFrameworkExtensions.xml* ficheiro.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Passo 5: Adicionar um fornecedor de afirmações
Cada fornecedor de afirmações tem de ter um ou mais perfis técnicos, que determinam os pontos de extremidade e protocolos necessários para comunicar com o fornecedor de afirmações.

Um fornecedor de afirmações pode ter vários perfis técnicos por vários motivos. Por exemplo, vários perfis técnicos podem ser definidos porque o fornecedor de afirmações suporta vários protocolos, pontos de extremidade podem ter recursos variados ou versões podem conter declarações que têm uma variedade de níveis de garantia. Pode ser aceitável para libertar afirmações confidenciais no percurso do utilizador de um, mas não em outro.

O seguinte fragmento XML contém um nó de fornecedor de afirmações com dois perfis técnicos:

* **TechnicalProfile Id="REST-API-SignUp"**: Define o seu serviço RESTful.
  * `Proprietary` é descrita como o protocolo de um fornecedor com base na RESTful.
  * `InputClaims` Define as afirmações que serão enviadas do Azure AD B2C para o serviço REST.

    Neste exemplo, o conteúdo da declaração `givenName` envia para o serviço REST como `firstName`, o conteúdo da afirmação `surname` envia para o serviço REST como `lastName`, e `email` envia como está. O `OutputClaims` elemento define as afirmações que são obtidas a partir do serviço RESTful volta para o Azure AD B2C.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"**: Adiciona um perfil de técnicas de validação para um perfil técnico existente (definido na política base). Durante a viagem de inscrição, o perfil técnico de validação invoca o perfil técnico anterior. Se o serviço RESTful retornar um erro HTTP 409 (um erro de conflito), a mensagem de erro é apresentada ao utilizador.

Localize a `<ClaimsProviders>` nó e, em seguida, adicione o seguinte fragmento XML sob o `<ClaimsProviders>` nó:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Passo 6: Adicionar o `loyaltyNumber` ao seu ficheiro de política de terceiros entidade confiadora de afirmação para que a afirmação sejam enviada para a sua aplicação
Editar sua *SignUpOrSignIn.xml* entidade confiadora (RP) de ficheiros e modificar o TechnicalProfile Id = "PolicyProfile" elemento para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Depois de adicionar a nova afirmação, o código de terceiros entidade confiadora fica assim:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Passo 7: Carregar a política para o seu inquilino

1. Na [portal do Azure](https://portal.azure.com), mude para o [contexto do inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, abra **do Azure AD B2C**.

2. Selecione **arquitetura de experiências de identidade**.

3. Open **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política, se existir** caixa de verificação.

6. Carregue o ficheiro de TrustFrameworkExtensions.xml e certifique-se de que ele passa a validação.

7. Repita o passo anterior com o arquivo SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Passo 8: Testar a política personalizada com executar agora
1. Selecione **definições do Azure AD B2C**e, em seguida, aceda à **Framework de experiência de identidade**.

    > [!NOTE]
    > **Executar agora** requer, pelo menos, um aplicativo para ser foi pré-registado no inquilino. Para saber como registar aplicações, veja o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Open **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) da entidade confiadora que carregou e, em seguida, selecione **executar agora**.

    ![A janela de B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testar o processo, escrevendo **teste** no **nome fornecido** caixa.  
    O Azure AD B2C apresenta uma mensagem de erro na parte superior da janela.

    ![Testar a política](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. Na **determinado nome** , escreva um nome (diferente de "teste").  
    O Azure AD B2C, o utilizador se inscreve e, em seguida, envia um loyaltyNumber à sua aplicação. Tenha em atenção o número neste JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Transferir os ficheiros de política concluída e o código
* Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) passo a passo, é recomendável que criar seu cenário com seus próprios arquivos de política personalizada. Para referência, nós fornecemos [arquivos de diretiva de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Pode baixar o código completo da [solução do Visual Studio de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Passos Seguintes
* [Proteja a sua API RESTful com autenticação básica (nome de utilizador e palavra-passe)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Proteja a sua API RESTful com certificados de cliente](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
