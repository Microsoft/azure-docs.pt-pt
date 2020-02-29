---
title: Integrar as permutas de reclamações da Rest API numa viagem de utilizador
titleSuffix: Azure AD B2C
description: Integre as trocas de reclamações rest API na sua viagem de utilizador Azure AD B2C como validação da entrada do utilizador.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5976b6ef747b27a5a04c755d47ae4383fc4b2447
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187360"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrar as trocas de reclamações da Rest API na sua viagem de utilizador Azure AD B2C como validação da entrada do utilizador

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Com o Quadro de Experiência de Identidade, que está subjacente ao Diretório Ativo Azure B2C (Azure AD B2C), pode integrar-se com uma API RESTful numa viagem de utilizador. Neste passeio, você vai aprender como Azure AD B2C interage com serviços restful .NET Framework RESTful (Web API).

## <a name="introduction"></a>Introdução

Ao utilizar o Azure AD B2C, pode adicionar a sua própria lógica de negócio a uma viagem de utilizador, chamando o seu próprio serviço RESTful. O Quadro de Experiência de Identidade envia dados para o serviço RESTful numa recolha de *créditos* de entrada e recebe dados da RESTful numa recolha de *sinistros de saída.* Com integração de serviçoreres RESTful, pode:

* **Validar os dados de entrada**do utilizador : Esta ação impede que os dados mal formados persistam em AD Azure. Se o valor do utilizador não for válido, o seu serviço RESTful devolve uma mensagem de erro que instrui o utilizador a fornecer uma entrada. Por exemplo, pode verificar se o endereço de e-mail fornecido pelo utilizador existe na base de dados do seu cliente.
* **Sobrepor as reclamações**de entrada : Por exemplo, se um utilizador introduzir o primeiro nome em todas as letras minúsculas ou maiúsculas, pode formatar o nome apenas com a primeira letra capitalizada.
* **Enriqueça os dados dos utilizadores integrando ainda mais as aplicações**de linha de negócio corporativas : O seu serviço RESTful pode receber o endereço de e-mail do utilizador, consultar a base de dados do cliente e devolver o número de fidelização do utilizador ao Azure AD B2C. As reclamações de devolução podem ser armazenadas na conta AD Azure do utilizador, avaliadas nos *próximos Passos de Orquestração,* ou incluídas no sinal de acesso.
* **Executar a lógica de negócio personalizada:** Pode enviar notificações push, atualizar bases de dados corporativas, executar um processo de migração de utilizadores, gerir permissões, auditar bases de dados e realizar outras ações.

Pode projetar a integração com os serviços RESTful das seguintes formas:

* **Perfil técnico de validação**: A chamada para o serviço RESTful ocorre dentro do perfil técnico de validação do perfil técnico especificado. O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador avançar. Com o perfil técnico de validação, pode:
  * Envie reclamações de entrada.
  * Valide as reclamações de entrada e lance mensagens de erro personalizadas.
  * Envie de volta as reclamações de saída.

* **Troca de reclamações**: Este design é semelhante ao perfil técnico de validação, mas acontece dentro de um passo de orquestração. Esta definição limita-se a:
  * Envie reclamações de entrada.
  * Envie de volta as reclamações de saída.

## <a name="restful-walkthrough"></a>Passagem restful

Neste walkthrough, desenvolve uma API web .NET Framework que valida a entrada do utilizador e fornece um número de fidelização do utilizador. Por exemplo, a sua aplicação pode conceder acesso a *benefícios platinados* com base no número de fidelização.

Descrição geral:

* Desenvolver o serviço RESTful (.NET Framework web API)
* Utilize o serviço RESTful na viagem do utilizador
* Envie reclamações de entrada e leia-as no seu código
* Validar o primeiro nome do utilizador
* Enviar de volta um número de lealdade
* Adicione o número de fidelização a um Token Web JSON (JWT)

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos no Getting started com artigo de [políticas personalizadas.](custom-policy-get-started.md)

## <a name="step-1-create-an-aspnet-web-api"></a>Passo 1: Criar uma ASP.NET Web API

1. No Estúdio Visual, crie um projeto selecionando **File** > **New** > **Project**.
1. Na janela **New Project,** selecione  **C# Visual** > **Web** > ASP.NET Web **Application (.NET Framework)** .
1. Na caixa **Nome,** digite um nome para a aplicação (por exemplo, *Contoso.AADB2C.API*), e, em seguida, selecione **OK**.

    ![Criação de um novo projeto do Estúdio Visual no Estúdio Visual](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-create-project.png)

1. Na janela **New ASP.NET Web Application,** selecione um modelo de aplicação **Web API** ou **Azure API.**

    ![Selecionando um modelo web API no Estúdio Visual](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. Certifique-se de que a autenticação está definida para **não autenticação**.
1. Selecione **OK** para criar o projeto.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Passo 2: Preparar o ponto final da API REST

### <a name="step-21-add-data-models"></a>Passo 2.1: Adicionar modelos de dados

Os modelos representam os dados de reclamações de entrada e de saída no seu serviço RESTful. O seu código lê os dados de entrada desserializando o C# modelo de reclamações de entrada de uma corda JSON para um objeto (o seu modelo). A ASP.NET Web API desserializa automaticamente o modelo de reclamações de saída para a JSON e, em seguida, escreve os dados serializados para o corpo da mensagem de resposta HTTP.

Crie um modelo que represente as alegações de entrada fazendo o seguinte:

1. Se o Solution Explorer ainda não estiver aberto, selecione **'Ver** > **Solution Explorer**.
1. No Explorador de Soluções, clique com o botão direito do rato na pasta **Modelos**, selecione **Adicionar** e, em seguida, selecione **Classe**.

    ![Adicione o item do menu Classe selecionado no Estúdio Visual](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-model.png)

1. Nomeie a classe `InputClaimsModel`, e adicione as seguintes propriedades à classe `InputClaimsModel`:

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

1. Crie um novo modelo, `OutputClaimsModel`, e adicione as seguintes propriedades à classe `OutputClaimsModel`:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. Crie mais um modelo, `B2CResponseContent`, que utiliza para lançar mensagens de erro de validação de entrada. Adicione as seguintes propriedades à classe `B2CResponseContent`, forneça as referências em falta e, em seguida, guarde o ficheiro:

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

### <a name="step-22-add-a-controller"></a>Passo 2.2: Adicione um controlador

Na Web API, um _controlador_ é um objeto que lida com pedidos HTTP. O controlador devolve as alegações de saída ou, se o primeiro nome não for válido, lança uma mensagem de erro Conflict HTTP.

1. No Explorador de Soluções, clique com o botão direito do rato na pasta **Controladores**, selecione **Adicionar** e, em seguida, selecione **Controlador**.

    ![Adicionar um novo controlador no Estúdio Visual](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. Na janela **Adicionar Andaime,** selecione **Web API Controller - Empty**, e, em seguida, selecione **Adicionar**.

    ![Selecionar Web API 2 Controller - Empty in Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. Na janela **Add Controller,** nomeie o controlador de **identidade**do controlador, e, em seguida, selecione **Adicionar**.

    ![Inserindo o nome do controlador no Estúdio Visual](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    O andaime cria um ficheiro chamado *IdentityController.cs* na pasta *controladores.*

1. Se o ficheiro *IdentityController.cs* ainda não estiver aberto, clique duas vezes nele e, em seguida, substitua o código no ficheiro pelo seguinte código:

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

## <a name="step-3-publish-the-project-to-azure"></a>Passo 3: Publicar o projeto ao Azure

1. No Solution Explorer, clique à direita no projeto **Contoso.AADB2C.API** e, em seguida, **selecione Publicar**.

    ![Publicar no Microsoft Azure App Service com Estúdio Visual](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. Na janela **Publicar,** selecione O Serviço de **Aplicações Microsoft Azure**, e, em seguida, selecione **Publicar**.

    ![Criar novo serviço de aplicações microsoft Azure com estúdio visual](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    A janela **Create App Service** abre. Nele, cria todos os recursos Azure necessários para executar a aplicação web ASP.NET em Azure.

    > [!TIP]
    > Para mais informações sobre como publicar, consulte [Criar uma ASP.NET aplicação web no Azure.](../app-service/app-service-web-get-started-dotnet-framework.md)

1. Na caixa **Web App Name,** escreva um nome de aplicativo único (caracteres válidos são a-z, 0-9 e hífenes (-). O URL da aplicação web é http://<app_name>.azurewebsites.NET, onde *app_name* é o nome da sua aplicação web. Também pode aceitar o nome gerado automaticamente, que já é exclusivo.

    ![Configurar as propriedades do Serviço de Aplicações](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Para começar a criar recursos Azure, selecione **Criar**.
    Depois de ter sido criada a ASP.NET aplicação web, o assistente publica-a no Azure e, em seguida, inicia a aplicação no navegador predefinido.

1. Copie o URL da aplicação web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Passo 4: Adicione a nova alegação `loyaltyNumber` ao esquema do seu ficheiro TrustFrameworkExtensions.xml

A reivindicação `loyaltyNumber` ainda não está definida no nosso esquema. Adicione uma definição dentro do elemento `<BuildingBlocks>`, que pode encontrar no início do ficheiro *TrustFrameworkExtensions.xml.*

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

## <a name="step-5-add-a-claims-provider"></a>Passo 5: Adicionar um fornecedor de sinistros

Cada prestador de sinistros deve ter um ou mais perfis técnicos, que determinam os pontos finais e os protocolos necessários para comunicar com o prestador de sinistros.

Um fornecedor de sinistros pode ter vários perfis técnicos por várias razões. Por exemplo, vários perfis técnicos podem ser definidos porque o fornecedor de sinistros suporta múltiplos protocolos, pontos finais podem ter capacidades variadas, ou lançamentos podem conter alegações que têm uma variedade de níveis de garantia. Pode ser aceitável lançar reclamações sensíveis numa viagem de utilizador, mas não noutra.

O seguinte snippet XML contém um nó de fornecedor de sinistros com dois perfis técnicos:

* **Perfil Técnico Id="REST-API-SignUp"** : Define o seu serviço RESTful.
  * `Proprietary` é descrito como o protocolo para um fornecedor restful.
  * `InputClaims` define as reclamações que serão enviadas do Azure AD B2C para o serviço REST.

    Neste exemplo, o conteúdo da reclamação `givenName` envia para o serviço REST como `firstName`, o conteúdo da reclamação `surname` envia para o serviço REST como `lastName`, e `email` envia como está. O elemento `OutputClaims` define as alegações que são recuperadas do serviço RESTful de volta ao Azure AD B2C.

* **Perfil Técnico Id="LocalAccountSignUpWithLogonEmail"** : Adiciona um perfil técnico de validação a um perfil técnico existente (definido na política de base). Durante a jornada de inscrição, o perfil técnico de validação invoca o perfil técnico anterior. Se o serviço RESTful devolver um erro HTTP 409 (um erro de conflito), a mensagem de erro é apresentada ao utilizador.

Localize o nó `<ClaimsProviders>` e adicione o seguinte corte XML sob o nó `<ClaimsProviders>`:

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

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificar alterações que deve fazer quando se muda para um ambiente de produção. Para aprender a proteger as suas APIs RESTful para produção, consulte [APIs RESTful Seguros com APIs](secure-rest-api-dotnet-basic-auth.md) resstful básicos e [SECURE RESTful com auth](secure-rest-api-dotnet-certificate-auth.md)certificado .

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Passo 6: Adicione a `loyaltyNumber` reclamação ao seu ficheiro político do partido que confia para que a reclamação seja enviada para a sua candidatura

Editar o ficheiro *SignUpOrSignIn.xml* (RP) e modificar o elemento TechnicalProfile Id="PolicyProfile" para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Depois de adicionar a nova reivindicação, o código do partido depende do seguinte:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Passo 7: Faça upload da apólice para o seu inquilino

1. No [portal Azure,](https://portal.azure.com)Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

1. No portal Azure, procure e selecione **Azure AD B2C**.

1. Selecione Quadro de **Experiência de Identidade**.

1. Abrir **todas as políticas.**

1. Selecione **Política de Upload**.

1. Selecione a **sobreescrita da apólice se existir** uma caixa de verificação.

1. Faça upload do ficheiro TrustFrameworkExtensions.xml e certifique-se de que passa a validação.

1. Repita o passo anterior com o ficheiro SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Passo 8: Teste a política personalizada usando run now

1. Selecione **Definições AD B2C Azure,** e depois vá para o Quadro de Experiência de **Identidade**.

    > [!NOTE]
    > **A corrida agora** requer pelo menos um pedido para ser pré-registrado no inquilino. Para saber registar as candidaturas, consulte o artigo [de registo](tutorial-create-tenant.md) de Candidaturas Do Azure AD B2C Get ou o artigo de inscrição de [aplicação.](tutorial-register-applications.md)

2. Abra **B2C_1A_signup_signin,** a política personalizada do partido que você carregou, e depois selecione **Run agora**.

    ![A página de política personalizada B2C_1A_signup_signin no portal Azure](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-run.png)

3. Teste o processo digitando **teste** na caixa **Nome Dado.**
    Azure AD B2C exibe uma mensagem de erro na parte superior da janela.

    ![Testar a validação da entrada do Nome Dado na página de inscrição](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-test.png)

4. Na caixa **Nome Dado,** digite um nome (com a não ser "Teste").
    O Azure AD B2C inscreve o utilizador e envia um número de fidelização para a sua aplicação. Repare o número neste JWT.

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Descarregue os ficheiros e código sintetizadores

* Depois de completar o [Get iniciado com políticas personalizadas,](custom-policy-get-started.md) recomendamos que construa o seu cenário utilizando os seus próprios ficheiros de política personalizadas. Para sua referência, fornecemos ficheiros de [política de amostras](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).

* Pode descarregar o código completo da [solução Sample Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Passos seguintes

A sua próxima tarefa é garantir a sua API RESTful utilizando a autenticação básica ou de certificado de cliente. Para aprender a proteger as suas APIs, consulte os seguintes artigos:

* [Proteja a sua API RESTful com autenticação básica (nome de utilizador e senha)](secure-rest-api-dotnet-basic-auth.md)
* [Proteja a sua API RESTful com certificados de cliente](secure-rest-api-dotnet-certificate-auth.md)

Para obter informações sobre todos os elementos disponíveis num perfil técnico RESTful, consulte [Referência: Perfil técnico RESTful](restful-technical-profile.md).
