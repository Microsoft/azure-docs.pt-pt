---
title: Escrever o código de autenticação da aplicação
titleSuffix: Azure Digital Twins
description: Ver como escrever código de autenticação numa aplicação ao cliente
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: 0438632a36fe14d35210cb5acb8d3a50d0f038b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767828"
---
# <a name="write-client-app-authentication-code"></a>Escreva código de autenticação de aplicativos de cliente

Depois de [configurar uma instância e autenticação Azure Digital Twins,](how-to-set-up-instance-portal.md)pode criar uma aplicação ao cliente que utilizará para interagir com o caso. Depois de configurar um projeto de cliente inicial, este artigo mostra-lhe **como escrever código nessa aplicação do cliente para autenticá-lo** contra a instância Azure Digital Twins.

Há duas abordagens para o código de amostra neste artigo. Podes usar o que é certo para ti, dependendo da tua linguagem de eleição:
* A primeira secção do código de amostra utiliza o Azure Digital Twins .NET (C#) SDK. O SDK faz parte do Azure SDK para .NET, e está localizado aqui: [*Biblioteca de clientes Azure IoT Digital Twin para .NET*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Existem também SDKs suportados para [Java](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0-beta.1/jar ) e [JavaScript](https://www.npmjs.com/package/@azure/digital-twins/v/1.0.0-preview.1), que podem ser usados de forma semelhante.
* A segunda secção do código de amostra destina-se a utilizadores que não utilizem um SDK fornecido e, em vez disso, utilizem SDKs gerados por AutoR em outras línguas. Para obter mais informações sobre esta estratégia, consulte [*Como-a-fazer: Crie SDKs personalizados para Azure Digital Twins com AutoRest*](how-to-create-custom-sdks.md).

Também pode ler mais sobre as APIs e SDKs para Azure Digital Twins em [*Como-a-: Use as APIs e SDKs das Gémeas Digitais Azure*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

Em primeiro lugar, complete os passos de configuração em [*Como-a: Configurar uma instância e autenticação*](how-to-set-up-instance-portal.md). Isto irá garantir que tem uma instância Azure Digital Twins, o seu utilizador tem permissões de acesso, e criou permissões para aplicações do cliente. Depois de toda esta configuração, está pronto para escrever o código de aplicação do cliente.

Para prosseguir, você precisará de um projeto de aplicação de cliente no qual você escreve o seu código. Se ainda não tiver um projeto de aplicação de clientes configurado, crie um projeto básico no seu idioma de eleição para usar com este tutorial.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Autenticação e criação de clientes: .NET (C#) SDK

Esta secção mostra um exemplo em C# para a utilização do .NET SDK fornecido.

Em primeiro lugar, inclua os seguintes pacotes no seu projeto para utilizar as ferramentas de autenticação .NET SDK e para este modo de como:
* `Azure.DigitalTwins.Core`
* `Azure.Identity`

Dependendo das suas ferramentas de eleição, pode incluir os pacotes utilizando o gestor de pacotes Visual Studio ou a `dotnet` ferramenta da linha de comando. 

Também necessitará das seguintes declarações:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
Para autenticar com o .NET SDK, utilize um dos métodos de obtenção de credenciais definidos na biblioteca [Azure.Identity.](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) Aqui estão dois que são comumente usados (mesmo juntos na mesma aplicação):

* [InteractiveBrowserCredential destina-se](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) a aplicações interativas, e pode ser usado para criar um cliente SDK autenticado
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funciona muito bem em casos em que você precisa de identidades geridas (MSI), e é um bom candidato para trabalhar com as Funções Azure

### <a name="interactivebrowsercredential-method"></a>Método InteractiveBrowserCredential
O método [InteractiveBrowserCredential destina-se](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) a aplicações interativas e irá criar um navegador web para autenticação.

Para utilizar as credenciais de navegador interativas para criar um cliente SDK autenticado, adicione este código:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Embora possa colocar o ID do cliente, o ID do inquilino e o URL de instância diretamente no código, como mostrado acima, é uma boa ideia que o seu código obtenha estes valores a partir de um ficheiro de configuração ou variável ambiental.

### <a name="managedidentitycredential-method"></a>Método ManageIdentityCredential
 O método [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funciona muito bem nos casos em que precisa de [identidades geridas (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)— por exemplo, ao trabalhar com as Funções Azure.
Numa função Azure, pode utilizar as credenciais de identidade geridas como esta:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Ver [*Como fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md) para um exemplo mais completo que explica algumas das escolhas importantes de configuração no contexto das funções.

Além disso, para utilizar a autenticação em função, lembre-se de:
* [Ativar a identidade gerida](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Utilize [variáveis ambientais](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) conforme apropriado
* Atribua permissões à aplicação de funções que lhe permitem aceder às APIs das Gémeas Digitais. Para obter mais informações sobre os processos de Funções Azure, consulte [*Como fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Autenticação com um SDK gerado por AutoResc

Se não estiver a utilizar um dos SDKs fornecidos (.NET, Java, JavaScript), poderá optar por construir uma biblioteca SDK num idioma à sua escolha, como descrito em [*Como-a-: Criar SDKs personalizados para Gémeos Digitais Azure com AutoRest*](how-to-create-custom-sdks.md).

Esta secção explica como autenticar nesse caso.

### <a name="prerequisites"></a>Pré-requisitos

Em primeiro lugar, deverá completar os passos para criar um SDK personalizado com AutoRest, utilizando os passos em [*Como-a-: Criar SDKs personalizados para Gémeos Digitais Azure com AutoRest*](how-to-create-custom-sdks.md).

Este exemplo utiliza um SDK typescript gerado com AutoRest. Como resultado, também requer:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Amostra de código de autenticação mínima

Para autenticar uma aplicação com os serviços Azure, pode utilizar o seguinte código mínimo dentro da sua aplicação cliente.

Você precisará do seu *ID de Aplicação (cliente)* e *Diretório (inquilino) ID* de anteriormente, bem como o URL da sua instância Azure Digital Twins.

> [!TIP]
> O URL da instância Azure Digital Twins é feito adicionando *https://* ao início do *nome de anfitrião*da sua instância Azure Digital Twins . Para ver o *nome do anfitrião,* juntamente com todas as propriedades do seu caso, pode `az dt show --dt-name <your-Azure-Digital-Twins-instance>` correr. Pode utilizar o `az account show --query tenantId` comando para ver o seu *ID do Diretório (inquilino).* 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Note mais uma vez que quando o código acima coloca o ID do cliente, iD do inquilino e URL de exemplo diretamente no código para a simplicidade, é uma boa ideia ter o seu código obter estes valores a partir de um ficheiro de configuração ou variável ambiental em vez disso.

A MSAL tem muitas mais opções que pode usar, para implementar coisas como caching e outros fluxos de autenticação. Para obter mais informações sobre este documento, consulte [*a visão geral da Biblioteca de Autenticação da Microsoft (MSAL)*](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre como funciona a segurança em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)

Ou, agora que a autenticação está configurada, passe a criar modelos no seu caso:
* [*Como fazer: Gerir modelos personalizados*](how-to-manage-model.md)