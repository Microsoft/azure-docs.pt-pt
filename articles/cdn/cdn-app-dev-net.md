---
title: Começar com a Biblioteca Azure CDN para .NET [ Microsoft Docs
description: Saiba como escrever aplicações .NET para gerir o Azure CDN usando o Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7e3ad3a5928b36c221bb83b1c4012c3c9e14f35d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67594167"
---
# <a name="get-started-with-azure-cdn-development"></a>Introdução à programação do CDN do Azure
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Pode utilizar a [Biblioteca Azure CDN para .NET](/dotnet/api/overview/azure/cdn) automatizar a criação e gestão de perfis e pontos finais da CDN.  Este tutorial atravessa a criação de uma simples aplicação de consola .NET que demonstra várias das operações disponíveis.  Este tutorial não se destina a descrever em detalhe todos os aspetos da Biblioteca CDN Azure.

Precisa do Estúdio Visual 2015 para completar este tutorial.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) está disponível gratuitamente para download.

> [!TIP]
> O [projeto concluído deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) está disponível para download na MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Crie o seu projeto e adicione pacotes Nuget
Agora que criámos um grupo de recursos para os nossos perfis de CDN e damos permissão à nossa aplicação Azure AD para gerir perfis de CDN e pontos finais dentro desse grupo, podemos começar a criar a nossa aplicação.

A partir do Visual Studio 2015, clique em **File**, **New**, **Project...** para abrir o novo diálogo do projeto.  Expandir **visual C#** e, em seguida, selecione **Windows** no painel à esquerda.  Clique na **aplicação da consola** no painel central.  Nomeie o seu projeto e, em seguida, clique **OK**.  

![Novo Projeto](./media/cdn-app-dev-net/cdn-new-project.png)

O nosso projeto vai usar algumas bibliotecas Azure contidas em pacotes Nuget.  Vamos adicioná-los ao projeto.

1. Clique no menu **Ferramentas,** **Nuget Package Manager,** em **seguida, consola de gestor de pacotes**.
   
    ![Gerir pacotes nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. Na consola do Gestor de Pacotes, execute o seguinte comando para instalar a Biblioteca de Autenticação de **Diretório Ativo (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Execute o seguinte para instalar a Biblioteca de **Gestão Azure CDN:**
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Diretivas, constantes, método principal e métodos de ajuda
Vamos escrever a estrutura básica do nosso programa.

1. De volta ao separador `using` Program.cs, substitua as diretivas no topo com as seguintes:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Temos de definir algumas constantes que os nossos métodos irão utilizar.  Na `Program` aula, mas `Main` antes do método, adicione o seguinte.  Certifique-se de que substitui os espaços reservados, incluindo os ** &lt;suportes&gt;angulares,** com os seus próprios valores, conforme necessário.
   
    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";
   
    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Também ao nível da classe, defina estas duas variáveis.  Usaremos isto mais tarde para determinar se o nosso perfil e ponto final já existem.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Substitua `Main` o método da seguinte forma:
   
   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();
   
       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };
   
       ListProfilesAndEndpoints(cdn);
   
       // Create CDN Profile
       CreateCdnProfile(cdn);
   
       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);
   
       Console.WriteLine();
   
       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);
   
       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);
   
       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);
   
       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Alguns dos nossos outros métodos vão levar o utilizador com perguntas "Sim/Não".  Adicione o seguinte método para tornar isto um pouco mais fácil:
   
    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Agora que a estrutura básica do nosso programa está escrita, `Main` devemos criar os métodos chamados pelo método.

## <a name="authentication"></a>Autenticação
Antes de podermos utilizar a Biblioteca de Gestão Azure CDN, precisamos autenticar o nosso diretor de serviço e obter um símbolo de autenticação.  Este método utiliza a ADAL para recuperar o símbolo.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Se estiver a utilizar a `GetAccessToken` autenticação individual do utilizador, o método será ligeiramente diferente.

> [!IMPORTANT]
> Utilize apenas esta amostra de código se optar por ter a autenticação individual do utilizador em vez de um diretor de serviço.
> 
> 

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Certifique-se `<redirect URI>` de que substitui o URI de redirecionamento que inseriu quando registou a aplicação em Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Listar perfis e pontos finais da CDN
Agora estamos prontos para fazer operações de CDN.  A primeira coisa que o nosso método faz é listar todos os perfis e pontos finais do nosso grupo de recursos, e se encontrar uma correspondência para o perfil e nomes finais especificados nas nossas constantes, faz uma nota disso para mais tarde para não tentarmos criar duplicados.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis e pontos finais da CDN
Em seguida, criaremos um perfil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Assim que o perfil for criado, criaremos um ponto final.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> O exemplo acima atribui ao ponto final uma origem `www.contoso.com`chamada *Contoso* com um nome de anfitrião.  Devias mudar isto para apontar o nome de anfitrião da tua própria origem.
> 
> 

## <a name="purge-an-endpoint"></a>Purgue um ponto final
Assumindo que o ponto final foi criado, uma tarefa comum que podemos querer realizar no nosso programa é purgar o conteúdo no nosso ponto final.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> No exemplo acima, `/*` a corda denota que quero expurgar tudo na raiz do caminho do ponto final.  Isto equivale a verificar purga **tudo** no diálogo de "purga" do portal Azure. No `CreateCdnProfile` método, criei o nosso perfil como **CDN Azure a partir do** perfil de Verizon usando o código `Sku = new Sku(SkuName.StandardVerizon)`, por isso este será um sucesso.  No entanto, **o Azure CDN dos** perfis da Akamai não suporta a **Purga All**, por isso, se eu estivesse a usar um perfil Akamai para este tutorial, teria de incluir caminhos específicos para purgar.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar perfis e pontos finais da CDN
Os últimos métodos eliminarão o nosso ponto final e o nosso perfil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Executar o programa
Podemos agora compilar e executar o programa clicando no botão **Iniciar** no Estúdio Visual.

![Programa em execução](./media/cdn-app-dev-net/cdn-program-running-1.png)

Quando o programa chegar à solicitação acima, deverá poder regressar ao seu grupo de recursos no portal Azure e ver se o perfil foi criado.

![Êxito!](./media/cdn-app-dev-net/cdn-success.png)

Podemos então confirmar as instruções para executar o resto do programa.

![Programa de conclusão](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Passos Seguintes
Para ver o projeto concluído a partir desta passagem, [descarregue a amostra](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Para encontrar documentação adicional na Biblioteca de Gestão de CDN azure para .NET, consulte a [referência no MSDN](/dotnet/api/overview/azure/cdn).

Gerencie os seus recursos CDN com [a PowerShell](cdn-manage-powershell.md).

