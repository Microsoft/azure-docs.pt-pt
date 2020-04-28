---
title: 'Autenticação do utilizador final: .NET SDK com Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como obter a autenticação de utilizador final com o Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure com .NET SDK
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "66243717"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 usando .NET SDK
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Com a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, aprende-se a utilizar o .NET SDK para fazer a autenticação de utilizador final com o Azure Data Lake Storage Gen1. Para autenticação de serviço a serviço com Data Lake Storage Gen1 utilizando .NET SDK, consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Estúdio Visual 2013 ou superior.** As instruções abaixo utilizam o Visual Studio 2019.

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de Diretório Ativo Azure "Nativa".** Deve ter concluído os passos de [autenticação do utilizador final com data Lake Storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-end-user-authenticate-using-active-directory.md).

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
Adicione este corte na sua aplicação de cliente .NET. Substitua os valores do espaço reservado com os valores recuperados de uma aplicação nativa azure AD (listada como pré-requisito). Este corte permite-lhe autenticar a sua aplicação **interativamente** com data Lake Storage Gen1, o que significa que é solicitado a introduzir as suas credenciais Azure.

Para facilitar a utilização, o seguinte snippet utiliza valores predefinidos para ID do cliente e redirecionamento URI que são válidos para qualquer subscrição azure. No seguinte corte, você só precisa fornecer o valor para o seu ID de inquilino. Você pode recuperar o ID do inquilino usando as instruções fornecidas no [Get the Tenant ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    
- Substitua a função Principal pelo seguinte código:

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

* O corte anterior utiliza funções de `GetTokenCache` `GetCreds_User_Popup`ajudante e . O código para estas funções de ajudante está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Para ajudá-lo a completar o tutorial mais rapidamente, o snippet utiliza um ID de cliente de aplicação nativa que está disponível por padrão para todas as subscrições do Azure. Por isso, pode **utilizar este fragmento tal como está na sua aplicação**.
* No entanto, se pretender utilizar o seu próprio domínio do Azure AD e o ID de cliente de aplicação, tem de criar uma aplicação nativa do Azure AD e, depois, utilizar o ID de inquilino do Azure AD, o ID de cliente e o URI de redirecionamento da aplicação que criou. Consulte Criar uma aplicação de [diretório ativo para autenticação de utilizadores finais com data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) para obter instruções.

  
## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a autenticação do utilizador final para autenticar com o Azure Data Lake Storage Gen1 utilizando o .NET SDK. Pode agora ver os seguintes artigos que falam sobre como usar o .NET SDK para trabalhar com o Azure Data Lake Storage Gen1.

* [Operações de gestão de conta em Data Lake Storage Gen1 usando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operações de dados em Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-data-operations-net-sdk.md)

