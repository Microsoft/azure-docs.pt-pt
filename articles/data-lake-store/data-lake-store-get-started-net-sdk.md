---
title: Gerir uma conta Azure Data Lake Storage Gen1 com .NET
description: Saiba como utilizar as operações de gestão de conta .NET SDK para Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c4ed092604e9511023a7025a69dc7a78652ea56
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430484"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operações de gestão de conta na Azure Data Lake Storage Gen1 usando .NET SDK
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, você aprende a executar operações de gestão de conta na Azure Data Lake Storage Gen1 usando .NET SDK. As operações de gestão de conta incluem a criação de uma conta Desatado de Armazenamento de Dados Gen1, a listagem das contas numa subscrição do Azure, a eliminação das contas, etc.

Para obter instruções sobre como executar operações de gestão de dados na Data Lake Storage Gen1 utilizando .NET SDK, consulte [as operações do Sistema de Ficheiros na Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013 ou superior**. As instruções abaixo utilizam o Visual Studio 2019.

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>Criar uma aplicação .NET
1. No Estúdio Visual, selecione o menu **'Ficheiro',** **Novo** e, em seguida, **Project**.
2. Escolha **a App consola (.NET Framework)** e, em seguida, selecione **Seguinte**.
3. No **nome do Projeto,** insira `CreateADLApplication` e, em seguida, selecione **Criar**.

4. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
   2. No separador **Gestor de Pacotes NuGet**, certifique-se de que a **Origem do pacote** está definida como **nuget.org** e que a caixa de verificação **Incluir pré-lançamento** está selecionada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - Este tutorial utiliza a v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial utiliza a v2.2.12.

        ![Adicionar uma fonte nuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta Azure Data Lake")
   4. Feche o **Gestor de Pacotes NuGet**.
5. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

6. Declare as variáveis e forneça os valores para os marcadores de posição. Além disso, certifique-se de que o caminho local e o nome de ficheiro fornecidos existem no computador.

    ```csharp
    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
                
            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;
            private static string _subId;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                _location = "East US 2";
                _subId = "<SUBSCRIPTION-ID>";                    
            }
        }
    }
    ```

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para executar operações, como a autenticação, carregamento de ficheiros, etc.

## <a name="authentication"></a>Autenticação

* Para autenticação do utilizador final para a sua aplicação, consulte [a autenticação do utilizador final com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Para autenticação de serviço-a-serviço para a sua aplicação, consulte [a autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Criar o objeto de cliente
O seguinte snippet cria o objeto cliente de conta Data Lake Storage Gen1, que é usado para emitir pedidos de gestão de conta para o serviço, tais como criar conta, apagar conta, etc.

```csharp
// Create client objects and set the subscription ID
_adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
```
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
O seguinte snippet cria uma conta Desapoia de Armazenamento de Dados Gen1 na subscrição Azure que forneceu ao criar o objeto cliente da conta Desemares de Armazenamento de Dados Gen1.

```csharp
// Create Data Lake Storage Gen1 account
var adlsParameters = new DataLakeStoreAccount(location: _location);
_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
```

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Listar todas as contas da Data Lake Storage Gen1 dentro de uma subscrição
Adicione o método seguinte à definição da classe. O seguinte snippet lista todas as contas da Data Lake Storage Gen1 dentro de uma determinada subscrição do Azure.

```csharp
// List all Data Lake Storage Gen1 accounts within the subscription
public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
{
    var response = _adlsClient.Account.List(_adlsAccountName);
    var accounts = new List<DataLakeStoreAccountBasic>(response);

    while (response.NextPageLink != null)
    {
        response = _adlsClient.Account.ListNext(response.NextPageLink);
        accounts.AddRange(response);
    }

    return accounts;
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Excluir uma conta Gen1 de armazenamento de data lake
O seguinte corte elimina a conta Desemarse De Armazenamento de Dados Gen1 que criou anteriormente.

```csharp
// Delete Data Lake Storage Gen1 account
_adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
```

## <a name="see-also"></a>Ver também
* [Operações de sistema de ficheiros na Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK Referência](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Passos seguintes
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)