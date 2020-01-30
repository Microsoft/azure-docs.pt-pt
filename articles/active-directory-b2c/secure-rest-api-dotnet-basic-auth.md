---
title: Proteger um serviço RESTful usando a autenticação básica HTTP
titleSuffix: Azure AD B2C
description: Proteja suas trocas de declarações da API REST personalizadas em seu Azure AD B2C usando a autenticação básica HTTP.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 986ad1845fa2699bb3462dceb3865300a035cb71
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848541"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Proteger seus serviços RESTful usando a autenticação básica HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Em um [artigo de Azure ad B2C relacionado](rest-api-claims-exchange-dotnet.md), você cria um serviço RESTFUL (API Web) que se integra aos percursos do usuário Azure Active Directory B2C (Azure ad B2C) sem autenticação.

Neste artigo, você adiciona a autenticação básica HTTP ao serviço RESTful para que somente os usuários verificados, incluindo B2C, possam acessar sua API. Com a autenticação básica HTTP, você define as credenciais do usuário (ID do aplicativo e segredo do aplicativo) em sua política personalizada.

Para obter mais informações, consulte [autenticação básica na API Web do ASP.net](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas no artigo [integrar as trocas de declarações da API REST no seu Azure ad B2C jornada do usuário](rest-api-claims-exchange-dotnet.md) .

## <a name="step-1-add-authentication-support"></a>Etapa 1: adicionar suporte de autenticação

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Etapa 1,1: adicionar configurações de aplicativo ao arquivo Web. config do seu projeto

1. Abra o projeto do Visual Studio que você criou anteriormente.

2. Adicione as seguintes configurações de aplicativo ao arquivo Web. config no elemento `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Crie uma senha e, em seguida, defina o valor de `WebApp:ClientSecret`.

    Para gerar uma senha complexa, execute o seguinte código do PowerShell. Você pode usar qualquer valor arbitrário.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Etapa 1,2: instalar bibliotecas do OWIN

Para começar, adicione os pacotes NuGet do middleware OWIN ao projeto usando o console do Gerenciador de pacotes do Visual Studio:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Etapa 1,3: adicionar uma classe de middleware de autenticação

Adicione a classe `ClientAuthMiddleware.cs` sob a pasta *App_Start.* Para tal:

1. Clique na pasta *App_Start,* selecione **Adicionar**, e, em seguida, selecione **Class**.

   ![Adicione ClientAuthMiddleware.cs classe na pasta App_Start](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Na caixa **nome** , digite **ClientAuthMiddleware.cs**.

   ![Criando uma nova C# classe na caixa de diálogo Adicionar novo item no Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

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

### <a name="step-14-add-an-owin-startup-class"></a>Etapa 1,4: adicionar uma classe de inicialização OWIN

Adicione uma classe de inicialização OWIN chamada `Startup.cs` à API. Para tal:
1. Clique com o botão direito do mouse no projeto, selecione **adicionar** > **novo item**e, em seguida, procure **OWIN**.

   ![Criando a classe de inicialização OWIN na caixa de diálogo Adicionar novo item no Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Abra o arquivo *Startup.cs* e substitua o conteúdo do arquivo pelo código a seguir:

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

### <a name="step-15-protect-the-identity-api-class"></a>Etapa 1,5: proteger a classe de API de identidade

Controladores abertos\IdentityController.cs e adicione a etiqueta `[Authorize]` à classe controladora. Essa marca restringe o acesso ao controlador para usuários que atendem ao requisito de autorização.

![Adicionar a marcação autorizar ao controlador](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Etapa 2: publicar no Azure

Para publicar seu projeto, em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **contoso. AADB2C. API** e selecione **publicar**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Etapa 3: adicionar a ID do aplicativo de serviços RESTful e o segredo do aplicativo ao Azure AD B2C

Depois que o serviço RESTful estiver protegido pela ID do cliente (nome de usuário) e segredo, você deverá armazenar as credenciais em seu locatário Azure AD B2C. Sua política personalizada fornece as credenciais ao invocar seus serviços RESTful.

### <a name="step-31-add-a-restful-services-client-id"></a>Etapa 3,1: adicionar uma ID de cliente dos serviços RESTful

1. No locatário do Azure AD B2C, selecione **configurações do B2C** > **estrutura de experiência de identidade**.


2. Selecione **chaves de política** para exibir as chaves que estão disponíveis em seu locatário.

3. Selecione **Adicionar**.

4. Para **Opções**, selecione **manual**.

5. Para **nome**, digite **B2cRestClientId**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.

6. Na caixa **segredo** , insira a ID do aplicativo que você definiu anteriormente.

7. Para **uso de chave**, selecione **assinatura**.

8. Selecione **Criar**.

9. Confirme que criou a chave `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-a-restful-services-client-secret"></a>Etapa 3,2: adicionar um segredo do cliente dos serviços RESTful

1. No locatário do Azure AD B2C, selecione **configurações do B2C** > **estrutura de experiência de identidade**.

2. Selecione **chaves de política** para exibir as chaves disponíveis em seu locatário.

3. Selecione **Adicionar**.

4. Para **Opções**, selecione **manual**.

5. Para **nome**, digite **B2cRestClientSecret**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.

6. Na caixa **segredo** , insira o segredo do aplicativo que você definiu anteriormente.

7. Para **uso de chave**, selecione **assinatura**.

8. Selecione **Criar**.

9. Confirme que criou a chave `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Etapa 4: alterar o perfil técnico para dar suporte à autenticação básica em sua política de extensão

1. No diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions. xml).

2. Procure o nó `<TechnicalProfile>` que inclui `Id="REST-API-SignUp"`.

3. Localize o elemento `<Metadata>`.

4. Altere a *AuthenticationType* para *básica*, da seguinte maneira:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Imediatamente após o elemento `<Metadata>` de fechamento, adicione o seguinte trecho de código XML:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Depois de adicionar o trecho, seu perfil técnico deve se parecer com o seguinte código XML:

    ![Adicionar elementos XML de autenticação básica ao TechnicalProfile](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Etapa 5: carregar a política para seu locatário

1. Na [portal do Azure](https://portal.azure.com), selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o locatário Azure ad B2C.

1. Na portal do Azure, procure e selecione **Azure ad B2C**.

1. Selecione **Identity Experience Framework**.

1. Abra **todas as políticas**.

1. Selecione **carregar política**.

1. Marque a caixa de seleção **substituir a política se ela existir** .

1. Carregue o arquivo *TrustFrameworkExtensions. xml* e verifique se ele passou na validação.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Etapa 6: testar a política personalizada usando executar agora

1. Abra **Azure ad B2C configurações**e, em seguida, selecione **estrutura de experiência de identidade**.

    >[!NOTE]
    >Executar agora exige que pelo menos um aplicativo seja preregistrado no locatário. Para saber como registrar aplicativos, consulte o artigo Azure AD B2C [introdução](tutorial-create-tenant.md) ou o artigo [registro de aplicativo](tutorial-register-applications.md) .

2. Abra **B2C_1A_signup_signin,** a política personalizada do partido que você carregou, e depois selecione **Run agora**.

3. Teste o processo digitando **teste** na caixa **nome especificado** .
    Azure AD B2C exibe uma mensagem de erro na parte superior da janela.

    ![Testando a validação de entrada de nome fornecida em sua API de identidade](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-test.png)

4. Na caixa **nome determinado** , digite um nome (diferente de "teste").
    Azure AD B2C inscreve o usuário e, em seguida, envia um número de fidelidade ao seu aplicativo. Observe o número neste exemplo:

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>Adicional Baixar o código e os arquivos de política completos

* Depois de concluir as instruções introdução [às políticas personalizadas](custom-policy-get-started.md) , recomendamos que você crie seu cenário usando seus próprios arquivos de política personalizados. Para sua referência, fornecemos [arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Você pode baixar o código completo da [solução de exemplo do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Passos seguintes

* [Usar certificados de cliente para proteger sua API RESTful](secure-rest-api-dotnet-certificate-auth.md)
