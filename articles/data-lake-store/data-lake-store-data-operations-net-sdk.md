---
title: '.NET SDK: Operações de sistema de ficheiros em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft'
description: Utilização do Azure Data Lake Gen1 SDK .NET do armazenamento para executar operações de sistema de ficheiros no Data Lake Storage Gen1, como criar pastas, etc.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 0a6cfc5ca9a0b788075f4472649a6e1bcdf87637
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119501"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operações do sistema de ficheiros em Gen1 de armazenamento do Azure Data Lake com o .NET SDK
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Neste artigo, irá aprender a efetuar operações de sistema de ficheiros no Data Lake Storage Gen1 com o .NET SDK. Operações de sistema de ficheiros incluem criar pastas numa conta do Data Lake Storage Gen1, carregar ficheiros, transferir ficheiros, etc.

Para obter instruções sobre como efetuar operações de gestão de conta na geração 1 de armazenamento do Data Lake com o .NET SDK, consulte [operações de gestão no Gen1 de armazenamento do Data Lake com o .NET SDK da conta](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo utilizam o Visual Studio 2017.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure de geração 1 de armazenamento do Data Lake**. Para obter instruções sobre como criar uma conta, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Criar uma aplicação .NET
O exemplo de código disponível no [GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) explica-lhe o processo de criação de ficheiros no arquivo, de concatenação de ficheiros, de transferência de um ficheiro e de eliminação de alguns ficheiros do arquivo. Esta secção do artigo explica-lhe as partes principais do código.

1. Abra o Visual Studio e crie uma aplicação de consola.
2. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**.
3. A partir de **Novo Projeto**, escreva ou selecione os seguintes valores:

   | Propriedade | Value |
   | --- | --- |
   | Categoria |Templates/Visual C#/Windows |
   | Modelo |Aplicação de Consola |
   | Name |CreateADLApplication |

4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
   2. No separador **Gestor de Pacotes NuGet**, certifique-se de que a **Origem do pacote** está definida como **nuget.org** e que a caixa de verificação **Incluir pré-lançamento** está selecionada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.DataLake.Store` - este tutorial utiliza a v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - este tutorial utiliza a v2.3.1.
    
      Feche o **Gestor de Pacotes NuGet**.

6. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Declare as variáveis conforme mostrado abaixo e forneça os valores para os marcadores de posição. Além disso, certifique-se de que o caminho local e o nome de ficheiro fornecidos aqui existem no computador.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para executar operações, como a autenticação, carregamento de ficheiros, etc.

## <a name="authentication"></a>Authentication

* Para a autenticação de utilizador final para a sua aplicação, veja [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Para a autenticação de serviço a serviço para a sua aplicação, veja [autenticação de serviço para serviço com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Criar o objeto de cliente
O fragmento seguinte cria o objeto de cliente de sistema de ficheiros de geração 1 de armazenamento do Data Lake, que é utilizado para emitir pedidos ao serviço.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Criar um ficheiro e um diretório
Adicione o fragmento seguinte à sua aplicação. Este fragmento adiciona um ficheiro, bem como qualquer diretório principal que não exista.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Acrescentar a um ficheiro
O fragmento seguinte liga dados a um arquivo existente na conta de geração 1 de armazenamento do Data Lake.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Ler um ficheiro
O fragmento seguinte lê o conteúdo de um ficheiro na geração 1 de armazenamento do Data Lake.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Obter as propriedades do ficheiro
O fragmento seguinte devolve as propriedades associadas a um ficheiro ou diretório.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

A definição do `PrintDirectoryEntry` método está disponível como parte da amostra [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro
O fragmento seguinte muda o nome de um ficheiro existente numa conta de geração 1 de armazenamento do Data Lake.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Enumerar um diretório
O fragmento seguinte enumera os diretórios numa conta de geração 1 de armazenamento do Data Lake

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

A definição do `PrintDirectoryEntry` método está disponível como parte da amostra [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Eliminar diretórios recursivamente
O fragmento seguinte elimina um diretório e todos os respetivos sub-diretórios, recursivamente.

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Amostras
Aqui estão alguns exemplos sobre como utilizar o SDK de sistema de ficheiros de geração 1 do Data Lake Storage.
* [Exemplo básico no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Exemplo avançado no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Consulte também
* [Operações de gestão de conta na geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Referência do SDK de .NET do Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passos Seguintes
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
