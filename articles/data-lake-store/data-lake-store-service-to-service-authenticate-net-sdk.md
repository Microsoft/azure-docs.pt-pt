---
title: .NET - Autenticação de serviço-a-serviço - Data Lake Storage Gen1
description: Saiba como obter a autenticação de serviço-a-serviço com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory usando .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 19b4ac619ec3e72c787efc8e9f043f42dbd8b09b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85505159"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1 usando .NET SDK
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilização de REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Neste artigo, você aprende sobre como usar o .NET SDK para fazer autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1. Para autenticação do utilizador final com data lake storage gen1 usando .NET SDK, consulte [a autenticação do utilizador final com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013 ou superior**. As instruções abaixo utilizam o Visual Studio 2019.

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Web" do Diretório Ativo Azure**. Deve ter completado os passos na [autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando o Azure Ative Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

## <a name="service-to-service-authentication-with-client-secret"></a>Autenticação de serviço-a-serviço com segredo do cliente
Adicione este corte na sua aplicação de cliente .NET. Substitua os valores de espaço reservado pelos valores obtidos a partir de uma aplicação web AD AZure (listada como pré-requisito). Este snippet permite-lhe autenticar a sua aplicação **não interativamente** com a Data Lake Storage Gen1 utilizando o segredo/chave do cliente para a aplicação web AD AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

O corte anterior utiliza uma função de ajudante `GetCreds_SPI_SecretKey` . O código para esta função de ajudante está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Autenticação de serviço-a-serviço com certificado

Adicione este corte na sua aplicação de cliente .NET. Substitua os valores de espaço reservado pelos valores obtidos a partir de uma aplicação web AD AZure (listada como pré-requisito). Este snippet permite-lhe autenticar a sua aplicação **não interativamente** com a Data Lake Storage Gen1 utilizando o certificado para uma aplicação web AZure AD. Para obter instruções sobre como criar uma aplicação AD Azure, consulte [Criar o principal do serviço com certificados](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

O corte anterior utiliza uma função de ajudante `GetCreds_SPI_Cert` . O código para esta função de ajudante está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a autenticação de serviço-a-serviço para autenticar com a Data Lake Storage Gen1 utilizando .NET SDK. Agora pode olhar para os seguintes artigos que falam sobre como usar o .NET SDK para trabalhar com a Data Lake Storage Gen1.

* [Operações de gestão de conta na Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operações de dados em Data Lake Storage Gen1 usando .NET SDK](data-lake-store-data-operations-net-sdk.md)
