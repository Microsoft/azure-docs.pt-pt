---
title: '.NET SDK: Operações de gestão de conta no Azure Data Lake Storage Gen1 [ Microsoft Docs'
description: Use Azure Data Lake Storage Gen1 .NET SDK para realizar operações de gestão de conta em Data Lake Storage Gen1
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "65900871"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operações de gestão de conta no Azure Data Lake Storage Gen1 utilizando .NET SDK
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, aprende-se a realizar operações de gestão de conta no Azure Data Lake Storage Gen1 utilizando o .NET SDK. As operações de gestão de conta incluem a criação de uma conta Data Lake Storage Gen1, a listagem das contas numa subscrição do Azure, a exclusão das contas, etc.

Para obter instruções sobre como realizar operações de gestão de dados no Data Lake Storage Gen1 utilizando .NET SDK, consulte as operações do [Sistema de Ficheiros no Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Estúdio Visual 2013 ou superior.** As instruções abaixo utilizam o Visual Studio 2019.

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>Criar uma aplicação .NET
1. No Estúdio Visual, selecione o menu **File,** **Novo,** e, em seguida, **Projeto**.
2. Escolha **a App consola (.QUADRO NET)** e, em seguida, selecione **Next**.
3. No nome do `CreateADLApplication` **Projeto,** introduza, e, em seguida, selecione **Criar**.

4. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
   2. No separador **Gestor de Pacotes NuGet**, certifique-se de que a **Origem do pacote** está definida como **nuget.org** e que a caixa de verificação **Incluir pré-lançamento** está selecionada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - Este tutorial utiliza a v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial utiliza a v2.2.12.

        ![Adicione uma fonte NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Crie uma nova conta azure Data Lake")
   4. Feche o **NuGet Package Manager**.
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

* Para autenticação de utilizador final para a sua aplicação, consulte a [autenticação do utilizador final com data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Para autenticação de serviço a serviço para a sua aplicação, consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Criar o objeto de cliente
O seguinte corte cria o objeto cliente da conta Data Lake Storage Gen1, que é usado para emitir pedidos de gestão de conta para o serviço, tais como criar conta, excluir conta, etc.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
O seguinte snippet cria uma conta Data Lake Storage Gen1 na subscrição Azure que forneceu enquanto cria o objeto cliente de cliente da conta Data Lake Storage Gen1.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Listar todas as contas da Gen1 de Armazenamento de Data Lake dentro de uma subscrição
Adicione o método seguinte à definição da classe. O seguinte snippet lista todas as contas data Lake Storage Gen1 dentro de uma determinada subscrição azure.

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

## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminar uma conta Gen1 de Armazenamento de Data Lake
O seguinte corte elimina a conta Data Lake Storage Gen1 que criou anteriormente.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Consulte também
* [Operações do sistema de ficheiros em Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Referência data Lake Storage Gen1 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passos seguintes
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
