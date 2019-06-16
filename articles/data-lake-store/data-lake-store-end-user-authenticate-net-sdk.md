---
title: 'Autenticação de utilizador final: SDK do .NET com a geração 1 de armazenamento do Azure Data Lake com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como conseguir a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory com o .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 215b839c21c2590c08ac2f4250086eaf97914ce1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243717"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com o .NET SDK
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Utilizar o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, saiba como utilizar o SDK de .NET para fazer a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake. Para a autenticação serviço a serviço com a geração 1 de armazenamento do Data Lake com o .NET SDK, veja [autenticação de serviço para serviço com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **O Visual Studio 2013 ou superior**. As instruções abaixo utilizam o Visual Studio 2019.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Nativa" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

5. Abra **Program.cs**
6. Substituir o com instruções com as seguintes linhas:

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
Adicione este trecho de código na sua aplicação de cliente .NET. Substitua os valores de marcador de posição pelos valores obtidos a partir de uma aplicação nativa do Azure AD (listada como pré-requisito). Este fragmento permite-lhe autenticar a sua aplicação **interativamente** com Gen1 de armazenamento do Data Lake, que significa que lhe for pedido para introduzir as suas credenciais do Azure.

Facilidade de utilização, o fragmento seguinte utiliza valores predefinidos para o ID de cliente e URI de redirecionamento que são válidos para qualquer subscrição do Azure. No trecho a seguir, só precisa fornecer o valor para o ID de inquilino. Pode obter o ID de inquilino ao seguir as instruções fornecidas no [obter o ID de inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    
- Substitua a função Main () com o código a seguir:

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

Algumas coisas que deve saber sobre o trecho anterior:

* O trecho anterior utiliza funções de programa auxiliar `GetTokenCache` e `GetCreds_User_Popup`. O código para estas funções de programa auxiliar está disponível [aqui, no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Para ajudar a concluir o tutorial mais depressa, o fragmento utiliza um ID de cliente do aplicativo nativo que está disponível por predefinição para todas as subscrições do Azure. Por isso, pode **utilizar este fragmento tal como está na sua aplicação**.
* No entanto, se pretender utilizar o seu próprio domínio do Azure AD e o ID de cliente de aplicação, tem de criar uma aplicação nativa do Azure AD e, depois, utilizar o ID de inquilino do Azure AD, o ID de cliente e o URI de redirecionamento da aplicação que criou. Ver [criar uma aplicação do Active Directory para autenticação de utilizador final com o Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) para obter instruções.

  
## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar a autenticação de utilizador final para se autenticar com a geração 1 de armazenamento do Azure Data Lake com o .NET SDK. Agora, pode ver os seguintes artigos que falam sobre como utilizar o SDK do .NET para trabalhar com a geração 1 de armazenamento do Azure Data Lake.

* [Operações de gestão de conta na geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operações de dados na geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-data-operations-net-sdk.md)

