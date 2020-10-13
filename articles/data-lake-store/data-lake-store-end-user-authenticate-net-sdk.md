---
title: Autenticação de utilizador final -.NET com Data Lake Storage Gen1 - Azure
description: Saiba como obter a autenticação do utilizador final com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory com .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89004787"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 usando .NET SDK
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilização de REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprende sobre como usar o .NET SDK para fazer a autenticação do utilizador final com Azure Data Lake Storage Gen1. Para autenticação de serviço-a-serviço com data lake storage gen1 usando .NET SDK, consulte [a autenticação de serviço-a-serviço com data lake storage gen1 usando .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013 ou superior**. As instruções abaixo utilizam o Visual Studio 2019.

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Native" do Diretório Ativo Azure**. Deve ter completado os passos na [autenticação do utilizador final com a Data Lake Storage Gen1 utilizando o Azure Ative Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar uma aplicação .NET
1. No Estúdio Visual, selecione o menu **'Ficheiro',** **Novo**e, em seguida, **Project**.
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

5. Abrir **Program.cs**
6. Substitua as declarações de utilização pelas seguintes linhas:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Autenticação de utilizador final
Adicione este corte na sua aplicação de cliente .NET. Substitua os valores de espaço reservado pelos valores obtidos a partir de uma aplicação nativa AD AD (listada como pré-requisito). Este corte permite-lhe autenticar a sua aplicação **interativamente** com a Data Lake Storage Gen1, o que significa que é solicitado a introduzir as suas credenciais Azure.

Para facilitar a utilização, o seguinte corte utiliza valores predefinidos para iD do cliente e redireciona URI que são válidos para qualquer subscrição do Azure. No seguinte corte, você só precisa fornecer o valor para o seu ID inquilino. Você pode recuperar a identificação do inquilino usando as instruções fornecidas na [Obter a identificação do inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    
- Substitua a função Principal() pelo seguinte código:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Algumas coisas para saber sobre o corte anterior:

* O corte anterior utiliza funções de ajudante `GetTokenCache` e `GetCreds_User_Popup` . O código para estas funções de ajudante está disponível [aqui no GitHub.](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache)
* Para ajudá-lo a completar o tutorial mais rapidamente, o snippet usa um ID do cliente de aplicação nativa que está disponível por padrão para todas as subscrições do Azure. Por isso, pode **utilizar este fragmento tal como está na sua aplicação**.
* No entanto, se pretender utilizar o seu próprio domínio do Azure AD e o ID de cliente de aplicação, tem de criar uma aplicação nativa do Azure AD e, depois, utilizar o ID de inquilino do Azure AD, o ID de cliente e o URI de redirecionamento da aplicação que criou. Consulte [Criar um pedido de diretório ativo para autenticação de utilizador final com a Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) para obter instruções.

  
## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a autenticação do utilizador final para autenticar com a Azure Data Lake Storage Gen1 utilizando .NET SDK. Agora pode olhar para os seguintes artigos que falam sobre como usar o .NET SDK para trabalhar com a Azure Data Lake Storage Gen1.

* [Operações de gestão de conta na Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operações de dados em Data Lake Storage Gen1 usando .NET SDK](data-lake-store-data-operations-net-sdk.md)

