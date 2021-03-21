---
title: .NET SDK - Operações de sistema de ficheiros em Data Lake Storage Gen1 - Azure
description: Utilize o Azure Data Lake Storage Gen1 .NET SDK para operações de sistema de ficheiros na Data Lake Storage Gen1, como criar pastas, etc.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.custom: devx-track-dotnet
ms.openlocfilehash: faa539119bbb8d73e66788b8ba472cf0db26a2e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431742"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Operações de sistema de ficheiros na Data Lake Storage Gen1 utilizando o .NET SDK

> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Neste artigo, aprende-se a realizar operações de sistema de ficheiros na Data Lake Storage Gen1 utilizando o .NET SDK. As operações do sistema de ficheiros incluem a criação de pastas numa conta Gen1 de armazenamento de dados, o upload de ficheiros, o descarregamento de ficheiros, etc.

Para obter instruções sobre como fazer operações de gestão de conta na Data Lake Storage Gen1 utilizando o .NET SDK, consulte [as operações de gestão de conta na Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio 2013 ou superior**. As instruções deste artigo utilizam o Visual Studio 2019.

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar uma conta, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Criar uma aplicação .NET

O exemplo de código disponível no [GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) explica-lhe o processo de criação de ficheiros no arquivo, de concatenação de ficheiros, de transferência de um ficheiro e de eliminação de alguns ficheiros do arquivo. Esta secção do artigo explica-lhe as partes principais do código.

1. No Estúdio Visual, selecione o menu **'Ficheiro',** **Novo** e, em seguida, **Project**.
1. Escolha **a App consola (.NET Framework)** e, em seguida, selecione **Seguinte**.
1. No **nome do Projeto,** insira `CreateADLApplication` e, em seguida, selecione **Criar**.
1. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
   1. No separador **NuGet Package Manager,** certifique-se de que a **fonte do Pacote** está definida para **nuget.org**. Além disso, certifique-se de que a caixa de verificação **de pré-lançamento** inclui ser selecionada.
   1. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.DataLake.Store` - Este artigo usa v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este artigo utiliza v2.3.1.

      Feche o **Gestor de Pacotes NuGet**.

1. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Declare as variáveis conforme mostrado abaixo e forneça os valores para os marcadores de posição. Além disso, certifique-se de que o caminho local e o nome de ficheiro fornecidos aqui existem no computador.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para fazer operações como autenticação, upload de ficheiros, etc.

## <a name="authentication"></a>Autenticação

* Para autenticação do utilizador final para a sua aplicação, consulte [a autenticação do utilizador final com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Para autenticação de serviço-a-serviço para a sua aplicação, consulte [a autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Criar o objeto de cliente

O seguinte snippet cria o objeto cliente do sistema de ficheiros Data Lake Storage Gen1, que é usado para emitir pedidos ao serviço.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Criar um ficheiro e um diretório

Adicione o fragmento seguinte à sua aplicação. Este snippet adiciona um ficheiro e qualquer diretório-mãe que não exista.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Acrescentar a um ficheiro

O seguinte snippet anexa dados a um ficheiro existente na conta da Data Lake Storage Gen1.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Ler um ficheiro

O seguinte snippet lê o conteúdo de um ficheiro na Data Lake Storage Gen1.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Obter as propriedades do ficheiro

O fragmento seguinte devolve as propriedades associadas a um ficheiro ou diretório.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

A definição do `PrintDirectoryEntry` método está disponível como parte da amostra [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro

O seguinte snippet renomea um ficheiro existente numa conta da Data Lake Storage Gen1.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Enumerar um diretório

Os seguintes snippet enumeram diretórios numa conta da Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

A definição do `PrintDirectoryEntry` método está disponível como parte da amostra [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Eliminar diretórios recursivamente

O seguinte corte elimina um diretório, e todas as suas subdireções, recursivamente.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Amostras

Aqui estão algumas amostras que mostram como usar o Sistema de Ficheiros Gen1 de Armazenamento de Dados SDK.

* [Amostra básica no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Amostra avançada no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Ver também

* [Operações de gestão de conta na Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK Referência](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Passos seguintes

* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)