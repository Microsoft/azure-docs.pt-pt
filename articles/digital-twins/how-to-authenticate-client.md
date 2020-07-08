---
title: Autenticar uma aplicação cliente
titleSuffix: Azure Digital Twins
description: Veja como autenticar uma aplicação do cliente contra o serviço Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390827"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Autenticar uma aplicação de cliente com Azure Digital Twins

Depois de [criar uma instância Azure Digital Twins,](how-to-set-up-instance.md)pode criar uma aplicação de cliente que irá utilizar para interagir com o caso. Depois de configurar um projeto de cliente inicial, este artigo mostra-lhe como autenticar adequadamente essa aplicação do cliente com a instância Azure Digital Twins.

Isto é feito em dois passos:
1. Criar um registo de aplicativos
2. Escreva código de autenticação numa aplicação ao cliente

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Criar um registo de aplicativos

Para autenticar contra a Azure Digital Twins a partir de uma aplicação do cliente, é necessário configurar um **registo de aplicações** no [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md).

Este registo de aplicações é onde configura permissões de acesso às [APIs das Gémeas Digitais Azure.](how-to-use-apis-sdks.md) A aplicação do seu cliente autentica-se contra o registo da aplicação e, como resultado, é-lhe concedidas as permissões de acesso configuradas às APIs.

Para criar um registo de aplicações, você precisa fornecer os IDs de recursos para as APIs de Gémeos Digitais Azure, e as permissões de base para a API. No seu diretório de trabalho, abra um novo arquivo e introduza o seguinte corte JSON para configurar estes detalhes: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Guarde este ficheiro à medida *quemanifest.jsligado*.

> [!NOTE] 
> Existem alguns locais onde uma corda "amigável", legível pelo homem `https://digitaltwins.azure.net` pode ser usada para o ID de recurso Azure Digital Twins em vez do GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Por exemplo, muitos exemplos ao longo desta documentação definem a autenticação com a biblioteca MSAL, e a corda amigável pode ser usada para isso. No entanto, durante este passo de criação do registo da aplicação, é necessário o formulário GUID do ID tal como é mostrado acima. 

Na sua janela Cloud Shell, clique no ícone "Carregar/Descarregar ficheiros" e escolha "Upload".

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Janela Cloud Shell mostrando a seleção da opção Upload":::
Navegue até ao *manifest.jsem* que acabou de criar e bater "Open".

Em seguida, executar o seguinte comando para criar um registo de aplicações (substituindo os espaços reservados, se necessário):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

A saída deste comando é mais ou menos assim.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Novo registo de aplicativos AAD":::

Depois de criar o registo da aplicação, siga [este link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) para navegar para a página geral de registo de aplicações da AAD no portal Azure.

A partir desta visão geral, selecione o registo de aplicações que acabou de criar a partir da lista. Isto abrirá os seus detalhes numa página como esta:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Portal Azure: IDs de autenticação":::

Tome nota do ID de *Aplicação (cliente)* e *Diretório (inquilino)* mostrado na **sua** página. Mais tarde utilizará estes valores para autenticar uma aplicação do cliente contra as APIs das Gémeas Digitais Azure.

> [!NOTE]
> Dependendo do seu cenário, poderá ter de fazer alterações adicionais no registo da aplicação. Aqui estão alguns requisitos comuns que poderá ter de cumprir:
> * Ativar o acesso do cliente público
> * Definir URLs de resposta específico para acesso web e desktop
> * Permitir fluxos implícitos de autenticação OAuth2
> * Se a sua subscrição Azure for criada utilizando uma conta Microsoft como Live, Xbox ou Hotmail, tem de definir o *sinalInAudience* no registo da aplicação para suportar contas pessoais.
> A forma mais fácil de configurar estas configurações é utilizar o [portal Azure](https://portal.azure.com/). Para obter mais informações sobre este processo, consulte [Registar uma aplicação com a plataforma de identidade da Microsoft.](https://docs.microsoft.com/graph/auth-register-app-v2)

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Escreva código de autenticação de aplicativos de cliente: .NET (C#) SDK

Esta secção descreve o código que necessitará de incluir na aplicação do seu cliente para completar o processo de autenticação utilizando o .NET (C#) SDK.
O Azure Digital Twins C# SDK faz parte do Azure SDK para .NET. Está localizado aqui: [Biblioteca de clientes Azure IoT Digital Twin para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver um projeto de aplicação de cliente inicial já configurado, crie um projeto básico .NET para utilizar com este tutorial.

Para utilizar o .NET SDK, terá de incluir os seguintes pacotes no seu projeto:
* `Azure.DigitalTwins.Core``1.0.0-preview.2`(versão)
* `Azure.Identity`

Dependendo das suas ferramentas de eleição, pode fazê-lo com o gestor de pacotes Visual Studio ou com a `dotnet` ferramenta da linha de comando. 

### <a name="authentication-and-client-creation-net"></a>Autenticação e criação de clientes: .NET

Para autenticar com o .NET SDK, utilize um dos métodos de obtenção de credenciais definidos na biblioteca [Azure.Identity.](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet)

Aqui estão dois que são comumente usados: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Este método destina-se a aplicações interativas e irá criar um navegador web para autenticação.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Este método funciona muito bem nos casos em que você precisa de [identidades geridas (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)— por exemplo, ao trabalhar com as Funções Azure. 

Também necessitará das seguintes declarações:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

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

Numa função Azure, pode então utilizar as credenciais de identidade geridas como esta:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Ver [Como fazer: Configurar uma função Azure para o processamento de dados](how-to-create-azure-function.md) para um exemplo mais completo que explica algumas das escolhas importantes de configuração no contexto das funções.

Além disso, para utilizar a autenticação em função, lembre-se de:
* [Ativar a identidade gerida](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Variáveis de ambiente](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Atribua permissões à aplicação de funções que lhe permitem aceder às APIs das Gémeas Digitais. Ver [Como fazer: Configurar uma função Azure para o processamento de dados](how-to-create-azure-function.md) para mais informações.

## <a name="authentication-in-an-autorest-generated-sdk"></a>Autenticação num SDK gerado por AutoResc

Se não estiver a utilizar .NET, poderá optar por construir uma biblioteca SDK num idioma à sua escolha, como descrito em [Como-a-: Criar SDKs personalizados para Gémeos Digitais Azure com AutoRest](how-to-create-custom-sdks.md).

Esta secção explica como autenticar nesse caso.

### <a name="prerequisites"></a>Pré-requisitos

Este exemplo utiliza um SDK typescript gerado com AutoRest. Como resultado, também requer:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Amostra de código de autenticação mínima

Para autenticar uma aplicação .NET com os serviços Azure, pode utilizar o seguinte código mínimo dentro da sua aplicação cliente.

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

A MSAL tem muitas mais opções que pode usar, para implementar coisas como caching e outros fluxos de autenticação. Para obter mais informações sobre este documento, consulte [a visão geral da Biblioteca de Autenticação da Microsoft (MSAL)](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Próximos passos

Leia mais sobre como funciona a segurança em Azure Digital Twins:
* [Conceitos: Segurança para soluções Azure Digital Twins](concepts-security.md)

Ou, agora que a autenticação está configurada, passe a criar modelos no seu caso:
* [Como fazer: Gerir modelos personalizados](how-to-manage-model.md)