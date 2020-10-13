---
title: Começa com a Biblioteca Azure CDN para .NET Microsoft Docs
description: Aprenda a escrever aplicações .NET para gerir o Azure CDN utilizando o Visual Studio.
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
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: a812704c42a4da5ddf89fe6c5ba0c9a684047f75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919493"
---
# <a name="get-started-with-the-azure-cdn-library-for-net"></a>Começa com a Biblioteca Azure CDN para .NET
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
>
>

Pode utilizar a [Biblioteca Azure CDN para .NET](/dotnet/api/overview/azure/cdn) para automatizar a criação e gestão de perfis e pontos finais da CDN.  Este tutorial percorre a criação de uma aplicação simples de consola .NET que demonstra várias das operações disponíveis.  Este tutorial não se destina a descrever todos os aspetos da Biblioteca Azure CDN para .NET em detalhe.

Precisa do Visual Studio 2015 para completar este tutorial.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) está livremente disponível para download.

> [!TIP]
> O [projeto concluído deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) está disponível para download na MSDN.
>
>

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Crie o seu projeto e adicione pacotes Nuget
Agora que criámos um grupo de recursos para os nossos perfis CDN e demos a nossa permissão à nossa aplicação AZure AD para gerir perfis e pontos finais da CDN dentro desse grupo, podemos começar a criar a nossa aplicação.

A partir do Visual Studio 2015, clique em **File**, **New**, **Project...** para abrir o novo diálogo do projeto.  Expandir **Visual C#**, em seguida, selecione **Windows** in the pane à esquerda.  Clique na **Aplicação da Consola** no painel central.  Nomeie o seu projeto e, em seguida, clique **em OK**.

![Novo Projeto](./media/cdn-app-dev-net/cdn-new-project.png)

O nosso projeto vai usar algumas bibliotecas Azure contidas em pacotes Nuget.  Vamos adicioná-los ao projeto.

1. Clique no menu **Ferramentas,** **Gestor de Pacotes Nuget**e, em seguida, **Consola de Gestor de Pacotes**.

    ![Gerir pacotes Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. Na Consola Gestor de Pacotes, execute o seguinte comando para instalar a Biblioteca de **Autenticação de Diretório Ativo (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Execute o seguinte para instalar a **Biblioteca de Gestão Azure CDN**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Diretivas, constantes, método principal e métodos de ajuda
Vamos escrever a estrutura básica do nosso programa.

1. De volta ao separador Program.cs, substitua as `using` diretivas no topo pelo seguinte:

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
2. Precisamos definir algumas constantes que os nossos métodos usarão.  Na `Program` classe, mas antes do `Main` método, adicione o seguinte.  Certifique-se de que substitui os espaços reservados, incluindo os ** &lt; suportes angulares, &gt; **com os seus próprios valores, se necessário.

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
3. Também ao nível da classe, defina estas duas variáveis.  Vamos usá-los mais tarde para determinar se o nosso perfil e o nosso ponto final já existem.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Substitua o `Main` método da seguinte forma:

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
5. Alguns dos nossos outros métodos vão incitar o utilizador com perguntas "Sim/Não".  Adicione o seguinte método para facilitar um pouco:

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

Agora que a estrutura básica do nosso programa está escrita, devemos criar os métodos chamados pelo `Main` método.

## <a name="authentication"></a>Autenticação
Antes de podermos utilizar a Biblioteca de Gestão Azure CDN, precisamos de autenticar o nosso principal de serviço e obter um sinal de autenticação.  Este método usa a ADAL para recuperar o símbolo.

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

Se estiver a utilizar a autenticação individual do utilizador, o `GetAccessToken` método será ligeiramente diferente.

> [!IMPORTANT]
> Utilize apenas esta amostra de código se estiver a optar por ter a autenticação individual do utilizador em vez de um principal de serviço.
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

Não se esqueça de substituir `<redirect URI>` pelo URI de redirecionamento que inseriu quando registou a aplicação no Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Listar perfis e pontos finais da CDN
Agora estamos prontos para fazer operações de CDN.  A primeira coisa que o nosso método faz é listar todos os perfis e pontos finais do nosso grupo de recursos, e se encontrar uma correspondência para os nomes de perfil e ponto final especificados nas nossas constantes, faz uma nota disso para mais tarde para não tentarmos criar duplicados.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis e pontos finais de CDN
A seguir, vamos criar um perfil.

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
> O exemplo acima atribui ao ponto final uma origem chamada *Contoso* com um nome de `www.contoso.com` anfitrião.  Devias mudar isto para apontar para o nome de anfitrião da tua própria origem.
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
> No exemplo acima, a corda `/*` denota que quero purgar tudo na raiz do caminho do ponto final.  Isto equivale a verificar o diálogo de purga **all** in the Azure portal "purgar". No `CreateCdnProfile` método, criei o nosso perfil como **UM CDN Azure a partir do** perfil de Verizon usando o código , para que isto seja bem `Sku = new Sku(SkuName.StandardVerizon)` sucedido.  No entanto, **a Azure CDN dos** perfis da Akamai não suporta **o Purpur All**, por isso, se eu estivesse a usar um perfil da Akamai para este tutorial, teria de incluir caminhos específicos para a purga.
>
>

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar perfis e pontos finais da CDN
Os últimos métodos eliminarão o nosso ponto final e perfil.

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
Agora podemos compilar e executar o programa clicando no botão **Iniciar** no Estúdio Visual.

![Programa em execução](./media/cdn-app-dev-net/cdn-program-running-1.png)

Quando o programa chegar ao pedido acima, deverá ser capaz de voltar ao seu grupo de recursos no portal Azure e ver se o perfil foi criado.

![Êxito!](./media/cdn-app-dev-net/cdn-success.png)

Podemos então confirmar as instruções para executar o resto do programa.

![Conclusão do programa](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Passos Seguintes
Para ver o projeto concluído a partir deste walkthrough, [descarregue a amostra.](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)

Para encontrar documentação adicional na Biblioteca de Gestão Azure CDN para .NET, consulte a [referência na MSDN](/dotnet/api/overview/azure/cdn).

Gerencie os seus recursos CDN com [o PowerShell](cdn-manage-powershell.md).
