---
title: Criar Conta De Visão Usando .NET SDK
description: Crie uma Conta Azure Purview utilizando .NET SDK.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: b3dc7bf8ac7650a7219c15a09a31d4dcf84a40bf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587804"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Quickstart: Criar uma conta de visão usando .NET SDK

Este quickstart descreve como usar .NET SDK para criar uma conta Azure Purview 

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* O seu próprio [inquilino do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* A sua conta deve ter permissão para criar recursos na subscrição

* Se tiver **a Azure Policy** a bloquear todas as aplicações da criação de conta de **Armazenamento** e espaço **de nomes EventHub,** tem de abrir exceções de política utilizando a etiqueta, que pode ser inserida durante o processo de criação de uma conta Purview. A razão principal é que para cada Conta Desembaraço criada, precisa de criar um Grupo de Recursos gerido e dentro deste grupo de recursos, uma conta de Armazenamento e um espaço de nomes EventHub. Para mais informações consulte o [Portal do Catálogo](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

A passagem por este artigo utiliza o Visual Studio 2019. Os procedimentos para Visual Studio 2013, 2015 ou 2017 diferem ligeiramente.

### <a name="azure-net-sdk"></a>SDK do Azure para .NET

Transfira e instale o [SDK do Azure para .NET](https://azure.microsoft.com/downloads/) no seu computador.

## <a name="create-an-application-in-azure-active-directory"></a>Criar uma Aplicação no Azure Active Directory

A partir das secções em *Como: Utilizar o portal para criar uma aplicação AD AD AZure e um principal de serviço que possa aceder aos recursos,* siga as instruções para fazer estas tarefas:

1. Na [Criação de uma aplicação Azure Ative Directory,](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)crie uma aplicação que represente a aplicação .NET que está a criar neste tutorial. Para o URL de início de sessão, pode fornecer um URL fictício conforme mostrado no artigo (`https://contoso.org/exampleapp`).
2. Em [Obter valores para iniciar sessão,](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)obtenha o ID da **aplicação** e **iD do inquilino,** e observe estes valores que você usa mais tarde neste tutorial. 
3. Em [Certificados e segredos](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options), obtenha a **chave de autenticação**, e note este valor que você usa mais tarde neste tutorial.
4. Na [Atribuição da aplicação a uma função,](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)atribua a aplicação à função **Contribuinte** ao nível de subscrição para que a aplicação possa criar fábricas de dados na subscrição.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Em seguida, crie uma aplicação de consola C# .NET no Visual Studio:

1. Lançamento **Visual Studio**.
2. Na janela Iniciar, **selecione Criar uma nova** app de consola de projeto  >  **(.NET Framework)**. É necessária a versão 4.5.2 ou superior do .NET.
3. Em **nome do Projeto,** insira **o PurviewQuickStart**.
4. Selecione **Create** (Criar) para criar o projeto.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

1. Selecione **Tools** (Ferramentas)  > **NuGet Package Manager** (Gestor de Pacotes NuGet)  > **Package Manager Console** (Consola do Gestor de Pacotes).
2. No painel **de consolas Do Gestor de Pacotes,** executar os seguintes comandos para instalar pacotes. Para obter mais informações, consulte o [pacote Microsoft.Azure.Management.Management.Purview NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Criar um cliente de Purview

1. Abra **Program.cs** e inclua as seguintes instruções para adicionar referências aos espaços de nomes.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adicione o código seguinte ao método **Principal** que define as variáveis. Substitua os espaços reservados pelos seus próprios valores. Para obter uma lista das regiões Azure em que o Purview está atualmente disponível, pesque a **Azure Purview** e selecione as regiões que lhe interessam na seguinte página: [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Adicione o seguinte código ao método **Principal** que cria uma instância de **classe PurviewManagementClient.** Utilize este objeto para criar uma Conta Purview.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Criar uma conta de visão

Adicione o seguinte código ao método **principal** que cria uma **Conta Purview**. 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>Executar o código

Construa e inicie a aplicação e verifique a execução.

A consola imprime o progresso da criação da Conta Purview.

### <a name="sample-output"></a>Saída de exemplo

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificar a saída

Aceda à página de **contas do 'Purview'** no [portal Azure](https://portal.azure.com) e verifique a conta criada através do código acima. 

## <a name="delete-purview-account"></a>Eliminar conta de Purga

Para eliminar programaticamente uma Conta Desemis, adicione as seguintes linhas de código ao programa: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Verifique se o nome da conta de Purview está disponível

Para verificar a disponibilidade de uma conta de competência, utilize o seguinte código: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

O código acima com impressão 'Verdadeiro' se o nome estiver disponível e 'Falso' se o nome não estiver disponível.


## <a name="next-steps"></a>Passos seguintes

O código deste tutorial cria uma conta de competência, elimina uma conta de competência e verifica a disponibilidade do nome da conta. Agora pode baixar o .NET SDK e conhecer outras ações do fornecedor de recursos que pode realizar para uma conta Purview.

Avance para o próximo artigo para saber como permitir que os utilizadores acedam à sua Conta Azure Purview. 

> [!div class="nextstepaction"]
> [Adicione utilizadores à sua Conta Azure Purview](catalog-permissions.md)
