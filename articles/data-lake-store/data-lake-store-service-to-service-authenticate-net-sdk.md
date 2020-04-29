---
title: 'Autenticação serviço-a-serviço: .NET SDK com Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure [ Azure Ative Directory] Microsoft Docs'
description: Saiba como obter a autenticação de serviço-a-serviço com o Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory utilizando .NET SDK
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
ms.openlocfilehash: 96c496ef67e26a3079577bf52e9d019d963467b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265535"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticação serviço-a-serviço com Azure Data Lake Storage Gen1 usando .NET SDK
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Com a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Neste artigo, você aprende sobre como usar o .NET SDK para fazer a autenticação serviço-a-serviço com Azure Data Lake Storage Gen1. Para autenticação de utilizador final com Data Lake Storage Gen1 utilizando .NET SDK, consulte a [autenticação do utilizador final com data lake storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Estúdio Visual 2013 ou superior.** As instruções abaixo utilizam o Visual Studio 2019.

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de Diretório Ativo Azure .** Deve ter concluído os passos de [autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

## <a name="service-to-service-authentication-with-client-secret"></a>Autenticação serviço-a-serviço com segredo do cliente
Adicione este corte na sua aplicação de cliente .NET. Substitua os valores do espaço reservado com os valores recuperados de uma aplicação web Azure AD (listada como pré-requisito). Este corte permite-lhe autenticar a sua aplicação **de forma não interativa** com data Lake Storage Gen1 utilizando o segredo/chave do cliente para a aplicação web Azure AD.

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

O corte anterior utiliza uma função `GetCreds_SPI_SecretKey`de ajudante . O código para esta função de ajudante está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Autenticação serviço-a-serviço com certificado

Adicione este corte na sua aplicação de cliente .NET. Substitua os valores do espaço reservado com os valores recuperados de uma aplicação web Azure AD (listada como pré-requisito). Este corte permite-lhe autenticar a sua aplicação **de forma não interativa** com data Lake Storage Gen1 utilizando o certificado para uma aplicação web Azure AD. Para obter instruções sobre como criar uma aplicação Azure AD, consulte [Criar o diretor de serviço com certificados](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

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

O corte anterior utiliza uma função `GetCreds_SPI_Cert`de ajudante . O código para esta função de ajudante está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a autenticação serviço-a-serviço para autenticar com data Lake Storage Gen1 usando .NET SDK. Pode agora ver os seguintes artigos que falam sobre como usar o SDK .NET para trabalhar com data Lake Storage Gen1.

* [Operações de gestão de conta em Data Lake Storage Gen1 usando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operações de dados em Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-data-operations-net-sdk.md)
