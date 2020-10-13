---
title: Escrever o código de autenticação da aplicação
titleSuffix: Azure Digital Twins
description: Ver como escrever código de autenticação numa aplicação ao cliente
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f2cef34413f46608e8bc35a009a29212af5ddf20
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893599"
---
# <a name="write-client-app-authentication-code"></a>Escreva código de autenticação de aplicativos de cliente

Depois de [configurar uma instância e autenticação Azure Digital Twins,](how-to-set-up-instance-portal.md)pode criar uma aplicação ao cliente que utilizará para interagir com o caso. Uma vez configurado um projeto de cliente inicial, terá de **escrever código nessa aplicação do cliente para** o autenticar contra a instância Azure Digital Twins.

A Azure Digital Twins realiza a autenticação utilizando [tokens de segurança Azure AD com base em AAUTH 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). Para autenticar o seu SDK, terá de obter um token ao portador com as permissões certas para a Azure Digital Twins e passá-lo juntamente com as suas chamadas API. 

Este artigo descreve como obter credenciais usando a biblioteca do `Azure.Identity` cliente. Enquanto este artigo mostra exemplos de código em C#, como o que escreveria para o [.NET (C#) SDK,](https://www.nuget.org/packages/Azure.DigitalTwins.Core)pode utilizar uma versão `Azure.Identity` de independentemente do que está a usar (para mais informações sobre os SDKs disponíveis para as Gémeas Digitais Azure, ver [*Como:: Use as APIs e SDKs de Gémeos Digitais Azure).*](how-to-use-apis-sdks.md)

## <a name="prerequisites"></a>Pré-requisitos

Em primeiro lugar, complete os passos de configuração em [*Como-a: Configurar uma instância e autenticação*](how-to-set-up-instance-portal.md). Isto irá garantir que tem uma instância Azure Digital Twins, o seu utilizador tem permissões de acesso, e criou permissões para aplicações do cliente. Depois de toda esta configuração, está pronto para escrever o código de aplicação do cliente.

Para prosseguir, você precisará de um projeto de aplicação de cliente no qual você escreve o seu código. Se ainda não tiver um projeto de aplicação de clientes configurado, crie um projeto básico no seu idioma de eleição para usar com este tutorial.

## <a name="common-authentication-methods-with-azureidentity"></a>Métodos comuns de autenticação com Azure.Identidade

`Azure.Identity` é uma biblioteca de clientes que fornece vários métodos de obtenção de credenciais que você pode usar para obter um token portador e autenticar com o seu SDK. Embora este artigo dê exemplos em C#, você pode ver `Azure.Identity` para várias línguas, incluindo...
* [.NET (C#)](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

Três métodos comuns de obtenção de credenciais `Azure.Identity` são:
* [O DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) fornece um fluxo de autenticação predefinido `TokenCredential` para aplicações que serão implantadas no Azure, e é **a escolha recomendada para o desenvolvimento local.** Pode também ser habilitado a experimentar os outros dois métodos recomendados neste artigo; envolve `ManagedIdentityCredential` e pode aceder com uma `InteractiveBrowserCredential` variável de configuração.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funciona muito bem nos casos em que precisa de [identidades geridas (MSI)](../active-directory/managed-identities-azure-resources/overview.md)e é um bom candidato para trabalhar com as Funções Azure e implantar para os serviços Azure.
* [InteractiveBrowserCredential destina-se](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) a aplicações interativas, e pode ser usado para criar um cliente SDK autenticado

O exemplo a seguir mostra como utilizar cada um destes com o .NET (C#) SDK.

## <a name="authentication-examples-net-c-sdk"></a>Exemplos de autenticação: .NET (C#) SDK

Esta secção mostra um exemplo em C# para utilizar o .NET SDK fornecido para escrever código de autenticação.

Em primeiro lugar, inclua o pacote SDK `Azure.DigitalTwins.Core` e o pacote no seu `Azure.Identity` projeto. Dependendo das suas ferramentas de eleição, pode incluir os pacotes utilizando o gestor de pacotes Visual Studio ou a `dotnet` ferramenta da linha de comando. 

Também terá de adicionar as seguintes declarações ao seu código de projeto:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Em seguida, adicione código para obter credenciais usando um dos métodos em `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Método DefaultAzureCredential

[O DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) fornece um fluxo de autenticação predefinido `TokenCredential` para aplicações que serão implantadas no Azure, e é **a escolha recomendada para o desenvolvimento local.**

Para utilizar as credenciais Azure predefinidas, você precisará do URL da instância Azure Digital Twins[(instruções para encontrar).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Aqui está uma amostra de código para adicionar um `DefaultAzureCredential` ao seu projeto:

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

### <a name="managedidentitycredential-method"></a>Método ManageIdentityCredential

O método [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funciona muito bem nos casos em que precisa de [identidades geridas (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)— por exemplo, ao trabalhar com as Funções Azure.

Isto significa que pode utilizar `ManagedIdentityCredential` no mesmo projeto que `DefaultAzureCredential` `InteractiveBrowserCredential` ou, para autenticar uma parte diferente do projeto.

Para utilizar as credenciais Azure predefinidas, você precisará do URL da instância Azure Digital Twins[(instruções para encontrar).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Numa função Azure, pode utilizar as credenciais de identidade geridas como esta:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>Método InteractiveBrowserCredential

O método [InteractiveBrowserCredential destina-se](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) a aplicações interativas e irá criar um navegador web para autenticação. Pode usá-lo em vez de `DefaultAzureCredential` nos casos em que necessita de autenticação interativa.

Para utilizar as credenciais de navegador interativas, você precisará de um **registo de aplicação** que tenha permissões para as APIs de Gémeos Digitais Azure. Para obter etapas sobre como configurar este registo de aplicações, consulte as permissões de [*acesso configurar para*](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) a secção de aplicações do cliente de *Como-a-: Configurar uma instância e autenticação.* Assim que o registo da aplicação estiver configurado, você precisará...
* ID de inscrição da aplicação *(cliente)*
* ID do Diretório de Registo de aplicações *(inquilino)*
* URL da instância Azure Digital Twins[(instruções para encontrar)](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Aqui está um exemplo do código para criar um cliente SDK autenticado usando `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

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

#### <a name="other-notes-about-authenticating-azure-functions"></a>Outras notas sobre a autenticação de Funções Azure

Ver [*Como fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md) para um exemplo mais completo que explica algumas das escolhas importantes de configuração no contexto das funções.

Além disso, para utilizar a autenticação em função, lembre-se de:
* [Ativar a identidade gerida](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Utilize [variáveis ambientais](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) conforme apropriado
* Atribua permissões à aplicação de funções que lhe permitem aceder às APIs das Gémeas Digitais. Para obter mais informações sobre os processos de Funções Azure, consulte [*Como fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Outros métodos de credencial

Se os cenários de autenticação acima referidos não cobrirem as necessidades da sua aplicação, poderá explorar outros tipos de autenticação oferecidas na plataforma de identidade da [**Microsoft.**](../active-directory/develop/v2-overview.md#getting-started) A documentação desta plataforma abrange cenários de autenticação adicional, organizados por tipo de aplicação.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre como funciona a segurança em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)

Ou, agora que a autenticação está configurada, passe a criar modelos no seu caso:
* [*Como fazer: Gerir modelos personalizados*](how-to-manage-model.md)
