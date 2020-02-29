---
title: Proteja um serviço RESTful utilizando a autenticação básica HTTP
titleSuffix: Azure AD B2C
description: Proteja as suas trocas personalizadas de reclamações REST API no seu Azure AD B2C utilizando a autenticação básica HTTP.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 505d92e33606dac327778cae179df44efbfdf853
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183861"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Proteja os seus serviços RESTful utilizando a autenticação básica HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Num artigo relacionado com [o Azure AD B2C,](rest-api-claims-exchange-dotnet.md)cria-se um serviço RESTful (Web API) que se integra com viagens de utilizador do Azure Ative Directory B2C (Azure AD B2C) sem autenticação.

Neste artigo, adiciona a autenticação básica HTTP ao seu serviço RESTful para que apenas utilizadores verificados, incluindo o B2C, possam aceder à sua API. Com a autenticação básica HTTP, define as credenciais de utilizador (id de aplicação e segredo de aplicação) na sua política personalizada.

Para mais informações, consulte a [autenticação básica na ASP.NET Web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Pré-requisitos

Complete as etapas no Integrate REST API reclama trocas no seu artigo de viagem de [utilizador Azure AD B2C.](rest-api-claims-exchange-dotnet.md)

## <a name="step-1-add-authentication-support"></a>Passo 1: Adicionar suporte à autenticação

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Passo 1.1: Adicione as definições de aplicação ao ficheiro web.config do seu projeto

1. Abra o projeto Visual Studio que criou anteriormente.

2. Adicione as seguintes definições de aplicação ao ficheiro web.config sob o elemento `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Crie uma palavra-passe e, em seguida, detete o valor `WebApp:ClientSecret`.

    Para gerar uma senha complexa, execute o seguinte código PowerShell. Pode usar qualquer valor arbitrário.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Passo 1.2: Instalar bibliotecas OWIN

Para começar, adicione os pacotes NuGet de middleware oWIN ao projeto utilizando a Consola de Gestor de Pacotes de Estúdio Visual:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Passo 1.3: Adicione uma classe de middleware de autenticação

Adicione a classe `ClientAuthMiddleware.cs` sob a pasta *App_Start.* Para tal:

1. Clique na pasta *App_Start,* selecione **Adicionar**, e, em seguida, selecione **Class**.

   ![Adicione ClientAuthMiddleware.cs classe na pasta App_Start](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Na caixa **nome,** escreva **ClientAuthMiddleware.cs**.

   ![Criar uma C# nova classe no diálogo Add New Item no Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Abra o ficheiro *App_Start\ClientAuthMiddleware.cs* e substitua o conteúdo do ficheiro pelo seguinte código:

    ```csharp

    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;

    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }

            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }

            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);

                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }

                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;

                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }

                return Next(environment);
            }

            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;

                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }

                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }

                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);

                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Passo 1.4: Adicione uma aula de startup OWIN

Adicione uma classe de startup oWIN chamada `Startup.cs` à API. Para tal:
1. Clique no projeto à direita, selecione **Adicionar** > **Novo Item,** e depois procure o **OWIN**.

   ![Criar a classe de startups OWIN em Adicionar novo diálogo de item no Estúdio Visual](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Abra o ficheiro *Startup.cs* e substitua o conteúdo do ficheiro por um código seguinte:

    ```csharp
    using Microsoft.Owin;
    using Owin;

    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Passo 1.5: Proteger a classe API de identidade

Controladores abertos\IdentityController.cs e adicione a etiqueta `[Authorize]` à classe controladora. Esta etiqueta restringe o acesso ao controlador aos utilizadores que cumpram o requisito de autorização.

![Adicione a etiqueta Autorizar ao controlador](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Passo 2: Publicar para o Azure

Para publicar o seu projeto, no Solution Explorer, clique no projeto **Contoso.AADB2C.API** e, em seguida, selecione **Publicar**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Passo 3: Adicione o ID da aplicação de serviços RESTful e o segredo da aplicação ao Azure AD B2C

Depois de o seu serviço RESTful estar protegido pelo ID do cliente (nome de utilizador) e secreto, deve armazenar as credenciais no seu inquilino Azure AD B2C. A sua política personalizada fornece as credenciais quando invoca os seus serviços RESTful.

### <a name="step-31-add-a-restful-services-client-id"></a>Passo 3.1: Adicione um ID do cliente de serviços RESTful

1. No seu inquilino Azure AD B2C, selecione **As configurações B2C** > **Quadro de Experiência de Identidade**.


2. Selecione **Teclas de Política** para ver as chaves disponíveis no seu inquilino.

3. Selecione **Adicionar**.

4. Para **opções,** selecione **Manual**.

5. Para **Nome**, tipo **B2cRestClientId**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.

6. Na caixa **Secreta,** introduza o ID da aplicação que definiu anteriormente.

7. Para **a utilização da chave,** selecione **Signature**.

8. Selecione **Criar**.

9. Confirme que criou a chave `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-a-restful-services-client-secret"></a>Passo 3.2: Adicione um segredo de cliente de serviços RESTful

1. No seu inquilino Azure AD B2C, selecione **As configurações B2C** > **Quadro de Experiência de Identidade**.

2. Selecione **Teclas de Política** para ver as chaves disponíveis no seu inquilino.

3. Selecione **Adicionar**.

4. Para **opções,** selecione **Manual**.

5. Para **Nome**, tipo **B2cRestClientSecret**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.

6. Na caixa **Secreta,** insira o segredo da aplicação que definiu anteriormente.

7. Para **a utilização da chave,** selecione **Signature**.

8. Selecione **Criar**.

9. Confirme que criou a chave `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Passo 4: Alterar o perfil técnico para apoiar a autenticação básica na sua política de extensão

1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).

2. Procure o nó `<TechnicalProfile>` que inclua `Id="REST-API-SignUp"`.

3. Localize o elemento `<Metadata>`.

4. Mude o Tipo de *Autenticação* para *Básico,* da seguinte forma:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Imediatamente após o elemento `<Metadata>` de fecho, adicione o seguinte corte XML:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Depois de adicionar o corte, o seu perfil técnico deve parecer o seguinte código XML:

    ![Adicione elementos xML de autenticação básica ao Perfil Técnico](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passo 5: Faça upload da apólice para o seu inquilino

1. No [portal Azure,](https://portal.azure.com)selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

1. No portal Azure, procure e selecione **Azure AD B2C**.

1. Selecione Quadro de **Experiência de Identidade**.

1. Abrir **todas as políticas.**

1. Selecione **Política de Upload**.

1. Selecione a **sobreescrita da apólice se existir** uma caixa de verificação.

1. Faça upload do ficheiro *TrustFrameworkExtensions.xml* e, em seguida, certifique-se de que passa a validação.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passo 6: Testar a política personalizada usando run now

1. Abra **as definições ad B2C do Azure,** e, em seguida, selecione Quadro de **Experiência de Identidade**.

    >[!NOTE]
    >Run Now requer pelo menos um pedido para ser pré-registrado no inquilino. Para saber registar as candidaturas, consulte o artigo [de registo](tutorial-create-tenant.md) de Candidaturas Do Azure AD B2C Get ou o artigo de inscrição de [aplicação.](tutorial-register-applications.md)

2. Abra **B2C_1A_signup_signin,** a política personalizada do partido que você carregou, e depois selecione **Run agora**.

3. Teste o processo digitando **teste** na caixa **Nome Dado.**
    Azure AD B2C exibe uma mensagem de erro na parte superior da janela.

    ![Testar a validação da entrada do Nome Dado na sua Identidade API](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-test.png)

4. Na caixa **Nome Dado,** digite um nome (com a não ser "Teste").
    O Azure AD B2C inscreve o utilizador e envia um número de fidelização para a sua aplicação. Note o número neste exemplo:

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Descarregue os ficheiros e código sintetizadores

* Depois de completar o [Get iniciado com políticas personalizadas,](custom-policy-get-started.md) recomendamos que construa o seu cenário utilizando os seus próprios ficheiros de política personalizadas. Para sua referência, fornecemos ficheiros de [política de amostras](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Pode descarregar o código completo da [solução Sample Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Passos seguintes

* [Utilize certificados de cliente para garantir a sua API RESTful](secure-rest-api-dotnet-certificate-auth.md)
