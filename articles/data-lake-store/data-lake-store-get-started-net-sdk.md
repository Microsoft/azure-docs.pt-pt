---
title: '.NET SDK: Conta de gestão de operações em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft'
description: Utilizar o SDK .NET de geração 1 armazenamento do Azure Data Lake para efetuar operações de gestão de contas no Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8da40aa04381542c8c750c8d7e33c9a29879371d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900871"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operações de gestão de conta na geração 1 de armazenamento do Azure Data Lake com o .NET SDK
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, aprenderá a efetuar operações de gestão de conta na geração 1 de armazenamento do Azure Data Lake com o .NET SDK. Operações de gestão de contas incluem criar uma conta de geração 1 de armazenamento do Data Lake, listar as contas numa subscrição do Azure, eliminar as contas, etc.

Para obter instruções sobre como efetuar operações de gestão de dados na geração 1 de armazenamento do Data Lake com o .NET SDK, consulte [operações de sistema de ficheiros no Data Lake Storage Gen1 com o .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **O Visual Studio 2013 ou superior**. As instruções abaixo utilizam o Visual Studio 2019.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>Criar uma aplicação .NET
1. No Visual Studio, selecione o **arquivo** menu, **New**e, em seguida **projeto**.
2. Escolher **aplicação de consola (.NET Framework)** e, em seguida, selecione **próxima**.
3. Na **nome do projeto**, introduza `CreateADLApplication`e, em seguida, selecione **criar**.

4. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
   2. No separador **Gestor de Pacotes NuGet**, certifique-se de que a **Origem do pacote** está definida como **nuget.org** e que a caixa de verificação **Incluir pré-lançamento** está selecionada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - Este tutorial utiliza a v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial utiliza a v2.2.12.

        ![Adicionar uma origem NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta do Azure Data Lake")
   4. Feche o **Gestor de Pacotes NuGet**.
5. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

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

6. Declare as variáveis e forneça os valores para os marcadores de posição. Além disso, certifique-se de que o caminho local e o nome de ficheiro fornecidos existem no computador.

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

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para executar operações, como a autenticação, carregamento de ficheiros, etc.

## <a name="authentication"></a>Autenticação

* Para a autenticação de utilizador final para a sua aplicação, veja [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Para a autenticação de serviço a serviço para a sua aplicação, veja [autenticação de serviço para serviço com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Criar o objeto de cliente
O fragmento seguinte cria o objeto de cliente de conta de geração 1 de armazenamento do Data Lake, que é utilizado para emitir pedidos de gestão de conta para o serviço, tais como criar a conta, eliminar a conta, entre outras.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta de geração 1 de armazenamento do Data Lake
O fragmento seguinte cria uma conta de geração 1 de armazenamento do Data Lake na subscrição do Azure que forneceu ao criar o objeto de cliente de conta de geração 1 de armazenamento do Data Lake.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Listar todas as contas de geração 1 do Data Lake armazenamento numa subscrição
Adicione o método seguinte à definição da classe. O fragmento seguinte lista todas as contas de geração 1 do Data Lake armazenamento numa determinada subscrição do Azure.

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

## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminar uma conta de geração 1 de armazenamento do Data Lake
O fragmento seguinte elimina a conta de geração 1 do Data Lake armazenamento que criou anteriormente.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Consulte também
* [Operações do sistema de ficheiros na geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Referência do SDK de .NET do Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passos Seguintes
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
