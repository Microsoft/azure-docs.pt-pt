---
title: Integrar as trocas de declarações da API REST em uma jornada do usuário
titleSuffix: Azure AD B2C
description: Integre as trocas de declarações da API REST no percurso do usuário Azure AD B2C como validação da entrada do usuário.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 780d575bd7f035673510d5b1e62cff4dfd6ede16
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848762"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integre as trocas de declarações da API REST no percurso do usuário Azure AD B2C como validação da entrada do usuário

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Com a estrutura de experiência de identidade, que se baseia Azure Active Directory B2C (Azure AD B2C), você pode integrar com uma API RESTful em um percurso do usuário. Neste tutorial, você aprenderá como Azure AD B2C interage com .NET Framework serviços RESTful (API Web).

## <a name="introduction"></a>Introdução

Usando Azure AD B2C, você pode adicionar sua própria lógica de negócios a uma jornada do usuário chamando seu próprio serviço RESTful. A estrutura de experiência de identidade envia dados para o serviço RESTful em uma coleção de *declarações de entrada* e recebe dados de volta do RESTful em uma coleção de *declarações de saída* . Com a integração do serviço RESTful, você pode:

* **Validar dados de entrada do usuário**: essa ação impede que dados malformados persistam no Azure AD. Se o valor do usuário não for válido, seu serviço RESTful retornará uma mensagem de erro que instrui o usuário a fornecer uma entrada. Por exemplo, você pode verificar se o endereço de email fornecido pelo usuário existe no banco de dados do cliente.
* **Substituir declarações de entrada**: por exemplo, se um usuário inserir o primeiro nome em letras minúsculas ou maiúsculas, você poderá formatar o nome somente com a primeira letra em maiúscula.
* **Enriquecer os dados do usuário com a integração adicional com aplicativos corporativos de linha de negócios**: seu serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário para Azure ad B2C. As declarações de retorno podem ser armazenadas na conta do Azure AD do usuário, avaliadas nas próximas *etapas de orquestração*ou incluídas no token de acesso.
* **Executar lógica de negócios personalizada**: você pode enviar notificações por push, atualizar bancos de dados corporativos, executar um processo de migração de usuário, gerenciar permissões, auditar bancos de dados e executar outras ações.

Você pode criar a integração com os serviços RESTful das seguintes maneiras:

* **Perfil técnico de validação**: a chamada para o serviço RESTful ocorre dentro do perfil técnico de validação do perfil técnico especificado. O perfil técnico de validação valida os dados fornecidos pelo usuário antes que o percurso do usuário avance. Com o perfil técnico de validação, você pode:
  * Enviar declarações de entrada.
  * Valide as declarações de entrada e gere mensagens de erro personalizadas.
  * Enviar declarações de saída.

* **Troca de declarações**: esse design é semelhante ao perfil técnico de validação, mas acontece dentro de uma etapa de orquestração. Essa definição é limitada a:
  * Enviar declarações de entrada.
  * Enviar declarações de saída.

## <a name="restful-walkthrough"></a>Instruções RESTful

Neste tutorial, você desenvolve uma API Web .NET Framework que valida a entrada do usuário e fornece um número de fidelidade do usuário. Por exemplo, seu aplicativo pode conceder acesso a *benefícios Platinum* com base no número de fidelidade.

Descrição geral:

* Desenvolver o serviço RESTful (API Web do .NET Framework)
* Usar o serviço RESTful no percurso do usuário
* Enviar declarações de entrada e lê-las em seu código
* Validar o nome do usuário
* Enviar de volta um número de fidelidade
* Adicionar o número de fidelidade a um token Web JSON (JWT)

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas no artigo [introdução às políticas personalizadas](custom-policy-get-started.md) .

## <a name="step-1-create-an-aspnet-web-api"></a>Etapa 1: criar uma API Web do ASP.NET

1. No Visual Studio, crie um projeto selecionando **arquivo** > **novo** > **projeto**.
1. Na janela **novo projeto** , selecione **Visual C#**  > **Web** > **aplicativo Web ASP.net (.NET Framework)** .
1. Na caixa **nome** , digite um nome para o aplicativo (por exemplo, *contoso. AADB2C. API*) e, em seguida, selecione **OK**.

    ![Criando um novo projeto do Visual Studio no Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-create-project.png)

1. Na janela **novo aplicativo web ASP.net** , selecione uma **API da Web** ou um modelo de **aplicativo de API do Azure** .

    ![Selecionando um modelo de API Web no Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. Verifique se a autenticação está definida como **sem autenticação**.
1. Selecione **OK** para criar o projeto.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Etapa 2: preparar o ponto de extremidade da API REST

### <a name="step-21-add-data-models"></a>Etapa 2,1: adicionar modelos de dados

Os modelos representam as declarações de entrada e os dados de declarações de saída em seu serviço RESTful. Seu código lê os dados de entrada desserializando o modelo de declarações de entrada de uma cadeia de C# caracteres JSON para um objeto (seu modelo). A API Web ASP.NET desserializa automaticamente o modelo de declarações de saída de volta para JSON e, em seguida, grava os dados serializados no corpo da mensagem de resposta HTTP.

Crie um modelo que representa as declarações de entrada fazendo o seguinte:

1. Se Gerenciador de Soluções ainda não estiver aberto, selecione **exibir** > **Gerenciador de soluções**.
1. No Explorador de Soluções, clique com o botão direito do rato na pasta **Modelos**, selecione **Adicionar** e, em seguida, selecione **Classe**.

    ![Adicionar item de menu de classe selecionado no Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-model.png)

1. Nomeie a classe `InputClaimsModel`e, em seguida, adicione as seguintes propriedades à classe `InputClaimsModel`:

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

1. Crie um novo modelo, `OutputClaimsModel`e, em seguida, adicione as seguintes propriedades à classe `OutputClaimsModel`:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. Crie mais um modelo, `B2CResponseContent`, que você usa para gerar mensagens de erro de validação de entrada. Adicione as propriedades a seguir à classe `B2CResponseContent`, forneça as referências ausentes e salve o arquivo:

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

### <a name="step-22-add-a-controller"></a>Etapa 2,2: adicionar um controlador

Na API Web, um _controlador_ é um objeto que MANIPULA solicitações HTTP. O controlador retorna declarações de saída ou, se o nome não for válido, gera uma mensagem de erro HTTP de conflito.

1. No Explorador de Soluções, clique com o botão direito do rato na pasta **Controladores**, selecione **Adicionar** e, em seguida, selecione **Controlador**.

    ![Adicionando um novo controlador no Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. Na janela **Adicionar Scaffold** , selecione **controlador da API Web-vazio**e, em seguida, selecione **Adicionar**.

    ![Selecionando o controlador da API Web 2-vazio no Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. Na janela **Adicionar controlador** , nomeie o controlador **IdentityController**e, em seguida, selecione **Adicionar**.

    ![Inserindo o nome do controlador no Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    O scaffolding cria um arquivo chamado *IdentityController.cs* na pasta *controladores* .

1. Se o arquivo *IdentityController.cs* ainda não estiver aberto, clique duas vezes nele e substitua o código no arquivo pelo código a seguir:

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

## <a name="step-3-publish-the-project-to-azure"></a>Etapa 3: publicar o projeto no Azure

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **contoso. AADB2C. API** e selecione **publicar**.

    ![Publicar no serviço de aplicativo Microsoft Azure com o Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. Na janela **publicar** , selecione **Microsoft Azure serviço de aplicativo**e, em seguida, selecione **publicar**.

    ![Criar novo Microsoft Azure serviço de aplicativo com o Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    A janela **Criar serviço de aplicativo** é aberta. Nele, você cria todos os recursos do Azure necessários para executar o aplicativo Web ASP.NET no Azure.

    > [!TIP]
    > Para obter mais informações sobre como publicar, consulte [criar um aplicativo web ASP.net no Azure](../app-service/app-service-web-get-started-dotnet-framework.md).

1. Na caixa **nome do aplicativo Web** , digite um nome de aplicativo exclusivo (os caracteres válidos são a-z, 0-9 e hifens (-). O URL da aplicação web é http://<app_name>.azurewebsites.NET, onde *app_name* é o nome da sua aplicação web. Também pode aceitar o nome gerado automaticamente, que já é exclusivo.

    ![Configurando as propriedades do serviço de aplicativo](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Para começar a criar recursos do Azure, selecione **criar**.
    Depois que o aplicativo Web ASP.NET tiver sido criado, o assistente o publicará no Azure e iniciará o aplicativo no navegador padrão.

1. Copie a URL do aplicativo Web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Etapa 4: adicionar a nova declaração de `loyaltyNumber` ao esquema do seu arquivo TrustFrameworkExtensions. xml

A declaração de `loyaltyNumber` ainda não está definida em nosso esquema. Adicione uma definição dentro do elemento `<BuildingBlocks>`, que pode ser encontrado no início do arquivo *TrustFrameworkExtensions. xml* .

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

## <a name="step-5-add-a-claims-provider"></a>Etapa 5: adicionar um provedor de declarações

Cada provedor de declarações deve ter um ou mais perfis técnicos, que determinam os pontos de extremidade e protocolos necessários para se comunicar com o provedor de declarações.

Um provedor de declarações pode ter vários perfis técnicos por vários motivos. Por exemplo, vários perfis técnicos podem ser definidos porque o provedor de declarações dá suporte a vários protocolos, os pontos de extremidade podem ter recursos variados ou as versões podem conter declarações que têm uma variedade de níveis de garantia. Pode ser aceitável lançar declarações confidenciais em uma jornada do usuário, mas não em outra.

O trecho de código XML a seguir contém um nó de provedor de declarações com dois perfis técnicos:

* **TechnicalProfile ID = "Rest-API-Signup"** : define seu serviço RESTful.
  * `Proprietary` é descrito como o protocolo para um provedor baseado em RESTful.
  * `InputClaims` define as declarações que serão enviadas do Azure AD B2C para o serviço REST.

    Neste exemplo, o conteúdo da declaração `givenName` envia para o serviço REST como `firstName`, o conteúdo da declaração `surname` envia ao serviço REST como `lastName`e `email` envia como está. O elemento `OutputClaims` define as declarações que são recuperadas do serviço RESTful de volta para Azure AD B2C.

* **TechnicalProfile ID = "LocalAccountSignUpWithLogonEmail"** : Adiciona um perfil técnico de validação a um perfil técnico existente (definido na política de base). Durante a jornada de inscrição, o perfil técnico de validação invoca o perfil técnico anterior. Se o serviço RESTful retornar um erro HTTP 409 (um erro de conflito), a mensagem de erro será exibida para o usuário.

Localize o nó `<ClaimsProviders>` e, em seguida, adicione o seguinte trecho XML sob o nó `<ClaimsProviders>`:

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
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
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

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que você deve fazer ao mudar para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, consulte [proteger APIs RESTful com autenticação básica](secure-rest-api-dotnet-basic-auth.md) e [proteger APIs RESTful com autenticação de certificado](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Etapa 6: adicionar a declaração de `loyaltyNumber` ao seu arquivo de política de terceira parte confiável para que a declaração seja enviada ao seu aplicativo

Edite o arquivo RP (terceira parte confiável) *SignUpOrSignIn. xml* e modifique o elemento TechnicalProfile ID = "PolicyProfile" para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Depois que você adicionar a nova declaração, o código de terceira parte confiável terá esta aparência:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Etapa 7: carregar a política para seu locatário

1. Na [portal do Azure](https://portal.azure.com), selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o locatário Azure ad B2C.

1. Na portal do Azure, procure e selecione **Azure ad B2C**.

1. Selecione **Identity Experience Framework**.

1. Abra **todas as políticas**.

1. Selecione **carregar política**.

1. Marque a caixa de seleção **substituir a política se ela existir** .

1. Carregue o arquivo TrustFrameworkExtensions. xml e certifique-se de que ele passe na validação.

1. Repita a etapa anterior com o arquivo SignUpOrSignIn. xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Etapa 8: testar a política personalizada usando executar agora

1. Selecione **configurações de Azure ad B2C**e vá para **estrutura de experiência de identidade**.

    > [!NOTE]
    > **Executar agora** exige que pelo menos um aplicativo seja preregistrado no locatário. Para saber como registrar aplicativos, consulte o artigo Azure AD B2C [introdução](tutorial-create-tenant.md) ou o artigo [registro de aplicativo](tutorial-register-applications.md) .

2. Abra **B2C_1A_signup_signin,** a política personalizada do partido que você carregou, e depois selecione **Run agora**.

    ![A página de política personalizada B2C_1A_signup_signin no portal Azure](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-run.png)

3. Teste o processo digitando **teste** na caixa **nome especificado** .
    Azure AD B2C exibe uma mensagem de erro na parte superior da janela.

    ![Testando a validação de entrada de nome fornecida na página de entrada de inscrição](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-test.png)

4. Na caixa **nome determinado** , digite um nome (diferente de "teste").
    Azure AD B2C inscreve o usuário e, em seguida, envia um loyaltyNumber para seu aplicativo. Observe o número neste JWT.

```JSON
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>Adicional Baixar o código e os arquivos de política completos

* Depois de concluir as instruções introdução [às políticas personalizadas](custom-policy-get-started.md) , recomendamos que você crie seu cenário usando seus próprios arquivos de política personalizados. Para sua referência, fornecemos [arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).

* Você pode baixar o código completo da [solução de exemplo do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Passos seguintes

Sua próxima tarefa é proteger sua API RESTful usando a autenticação básica ou de certificado do cliente. Para saber como proteger suas APIs, consulte os seguintes artigos:

* [Proteger sua API RESTful com autenticação básica (nome de usuário e senha)](secure-rest-api-dotnet-basic-auth.md)
* [Proteger sua API RESTful com certificados de cliente](secure-rest-api-dotnet-certificate-auth.md)

Para obter informações sobre todos os elementos disponíveis em um perfil técnico RESTful, consulte [referência: perfil técnico RESTful](restful-technical-profile.md).
