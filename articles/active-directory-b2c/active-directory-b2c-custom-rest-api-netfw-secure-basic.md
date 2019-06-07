---
title: Proteger os seus serviços RESTful com autenticação básica HTTP no Azure Active Directory B2C | Documentos da Microsoft
description: Proteja as suas trocas de afirmações de REST API personalizadas no seu Azure AD B2C ao utilizar a autenticação básica HTTP.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e07a3fee2cc6c588dee608a68415b554e6fc77f3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507595"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Proteger os serviços RESTful com autenticação básica HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Num [relacionado do Azure AD B2C artigo](active-directory-b2c-custom-rest-api-netfw.md), vai criar um serviço RESTful (web API) que se integra no Azure Active Directory B2C (Azure AD B2C) utilizador Jornadas sem autenticação.

Neste artigo, adicionar autenticação básica HTTP ao seu serviço RESTful, de forma a que só verificar os utilizadores, incluindo B2C, pode aceder a sua API. Com a autenticação básica HTTP, defina as credenciais de utilizador (ID da aplicação e segredo da aplicação) na sua política personalizada.

Para obter mais informações, consulte [a autenticação básica na ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos a [trocas no seu percurso do utilizador do Azure AD B2C de afirmações de API do REST de integrar](active-directory-b2c-custom-rest-api-netfw.md) artigo.

## <a name="step-1-add-authentication-support"></a>Passo 1: Adicionar suporte de autenticação

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Passo 1.1: Adicionar as definições da aplicação ao arquivo Web. config de seu projeto

1. Abra o projeto do Visual Studio que criou anteriormente.

2. Adicione as seguintes definições de aplicação para o ficheiro Web. config sob o `appSettings` elemento:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Criar uma palavra-passe e, em seguida, defina o `WebApp:ClientSecret` valor.

    Para gerar uma palavra-passe complexa, execute o seguinte código do PowerShell. Pode utilizar qualquer valor arbitrário.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Passo 1.2: Instalar OWIN bibliotecas

Para começar, adicione os pacotes de NuGet de middleware do OWIN para o projeto, utilizando a consola do Gestor de pacotes do Visual Studio:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Passo 1.3: Adicionar uma classe de middleware de autenticação

Adicionar a `ClientAuthMiddleware.cs` classe sob a *App_Start* pasta. Para tal:

1. Com o botão direito a *App_Start* pasta, selecione **Add**e, em seguida, selecione **classe**.

   ![Adicionar classe de ClientAuthMiddleware.cs na pasta App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Na **Name** , escreva **ClientAuthMiddleware.cs**.

   ![Criar a nova classe c#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Abra o *App_Start\ClientAuthMiddleware.cs* de ficheiro e substitua o ficheiro de conteúdo com o código a seguir:

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

### <a name="step-14-add-an-owin-startup-class"></a>Passo 1.4: Adicionar uma classe de startup da OWIN

Adicionar uma classe de startup da OWIN com o nome `Startup.cs` para a API. Para tal:
1. Com o botão direito no projeto, selecione **Add** > **Novo Item**e, em seguida, procure **OWIN**.

   ![Adicionar uma classe de startup da OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Abra o *Startup.cs* de ficheiro e substitua o ficheiro de conteúdo com o código a seguir:

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

### <a name="step-15-protect-the-identity-api-class"></a>Passo 1.5: Proteger a classe de API de identidade

Abra Controllers\IdentityController.cs e adicione o `[Authorize]` etiqueta para a classe de controlador. Esta etiqueta restringe o acesso para o controlador para os utilizadores que cumpre o requisito de autorização.

![Adicionar a marca de autorizar o controlador](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Passo 2: Publicar no Azure

Para publicar o seu projeto, no Explorador de soluções, clique com botão direito a **Contoso.AADB2C.API** projeto e, em seguida, selecione **Publish**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Passo 3: Adicione o segredo de ID e a aplicação da aplicação de serviços RESTful para o Azure AD B2C

Depois do seu serviço RESTful está protegido pelo ID de cliente (nome de utilizador) e o segredo, tem de armazenar as credenciais no seu inquilino do Azure AD B2C. A diretiva personalizada fornece as credenciais quando invoca os serviços RESTful.

### <a name="step-31-add-a-restful-services-client-id"></a>Passo 3.1: Adicionar um ID de cliente de serviços RESTful

1. No seu inquilino do Azure AD B2C, selecione **definições do B2C** > **Framework de experiência de identidade**.


2. Selecione **chaves de política** para exibir as chaves que estão disponíveis no seu inquilino.

3. Selecione **Adicionar**.

4. Para **opções**, selecione **Manual**.

5. Para **Name**, tipo **B2cRestClientId**.  
    O prefixo *B2C_1A_* podem ser adicionados automaticamente.

6. Na **segredo** , introduza o ID da aplicação que definiu anteriormente.

7. Para **utilização de chave**, selecione **assinatura**.

8. Selecione **Criar**.

9. Confirme que criou o `B2C_1A_B2cRestClientId` chave.

### <a name="step-32-add-a-restful-services-client-secret"></a>Passo 3.2: Adicionar um segredo de cliente de serviços RESTful

1. No seu inquilino do Azure AD B2C, selecione **definições do B2C** > **Framework de experiência de identidade**.

2. Selecione **chaves de política** para exibir as chaves disponíveis no seu inquilino.

3. Selecione **Adicionar**.

4. Para **opções**, selecione **Manual**.

5. Para **Name**, tipo **B2cRestClientSecret**.  
    O prefixo *B2C_1A_* podem ser adicionados automaticamente.

6. Na **segredo** , introduza o segredo de aplicação que definiu anteriormente.

7. Para **utilização de chave**, selecione **assinatura**.

8. Selecione **Criar**.

9. Confirme que criou o `B2C_1A_B2cRestClientSecret` chave.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Passo 4: Alterar o perfil técnico para suportar a autenticação básica na sua política de extensão

1. No diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).

2. Procure o `<TechnicalProfile>` nó que inclui `Id="REST-API-SignUp"`.

3. Localize o `<Metadata>` elemento.

4. Alteração da *AuthenticationType* para *básica*, da seguinte forma:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Imediatamente após o fecho `<Metadata>` elemento, adicione o seguinte fragmento XML:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Depois de adicionar o fragmento, o perfil técnico deve ter um aspeto semelhante ao seguinte código XML:
    
    ![Adicionar elementos XML de autenticação básica](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passo 5: Carregar a política para o seu inquilino

1. Na [portal do Azure](https://portal.azure.com), mude para o [contexto do inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, abra **do Azure AD B2C**.

2. Selecione **arquitetura de experiências de identidade**.

3. Open **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política, se existir** caixa de verificação.

6. Carregar o *TrustFrameworkExtensions.xml* de ficheiros e, em seguida, certifique-se de que ele passa a validação.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passo 6: Testar a política personalizada com executar agora

1. Open **definições do Azure AD B2C**e, em seguida, selecione **Framework de experiência de identidade**.

    >[!NOTE]
    >Executar agora requer, pelo menos, um aplicativo para ser foi pré-registado no inquilino. Para saber como registar aplicações, veja o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Open **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) da entidade confiadora que carregou e, em seguida, selecione **executar agora**.

3. Testar o processo, escrevendo **teste** no **nome fornecido** caixa.  
    O Azure AD B2C apresenta uma mensagem de erro na parte superior da janela.

    ![Testar a API de identidade](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Na **determinado nome** , escreva um nome (diferente de "teste").  
    O Azure AD B2C, o utilizador se inscreve e, em seguida, envia um número de fidelidade à sua aplicação. Tenha em atenção o número neste exemplo:

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

* Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) passo a passo, é recomendável que criar seu cenário com seus próprios arquivos de política personalizada. Para referência, nós fornecemos [arquivos de diretiva de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Pode baixar o código completo da [solução do Visual Studio de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar certificados de cliente para proteger a sua API RESTful](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)