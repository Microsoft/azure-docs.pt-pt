---
title: Amostras de armazenamento Azure usando | C++ Microsoft Docs
description: Ver, descarregar e executar código de amostra e aplicações para Azure Storage. Descubra a obtenção de amostras para bolhas, filas, mesas e ficheiros, utilizando as bibliotecas de clientes de armazenamento C++.
author: twooley
ms.author: twooley
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: d82b31cde0e8df5db2d073abcec8ea44f13bd0f0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276676"
---
# <a name="azure-storage-samples-using-v12-c-client-libraries"></a>Amostras de armazenamento Azure usando bibliotecas de clientes V12 C++

A tabela seguinte fornece uma visão geral do repositório de amostras e dos cenários cobertos por cada amostra. Clique nos links para ver o código de amostra correspondente no GitHub.

> [!NOTE]
> Estas amostras utilizam a mais recente biblioteca Azure Storage C++ v12.

## <a name="blob-samples"></a>Amostras de bolhas

:::row:::
   :::column:::
        [Autenticar usando uma cadeia de ligação](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L18)
   :::column-end:::
   :::column:::
        [Criar um contentor de blobs](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L20)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Arranja um cliente blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Carregar um blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L32)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Definir metadados numa bolha](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L34)
   :::column-end:::
   :::column:::
        [Obtenha propriedades blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
        [Transferir um blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L44)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Amostras gen2 de armazenamento de dados do lago de dados

:::row:::
   :::column:::
        [Criar um cliente de serviço usando uma cadeia de ligação](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L22)
   :::column-end:::
   :::column:::
        [Criar um cliente do sistema de ficheiros usando uma cadeia de ligação](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L25)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Criar um sistema de ficheiros](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Criar um diretório](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Criar um ficheiro](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L52)
   :::column-end:::
   :::column:::
        [Dados do apêndice a um ficheiro](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Flush dados de ficheiros](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L77)
   :::column-end:::
   :::column:::
        [Ler um ficheiro](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Listar todos os sistemas de ficheiros](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L88)
   :::column-end:::
   :::column:::
        [Eliminar sistema de ficheiros](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L102)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Amostras de Arquivos Azure

:::row:::
    :::column:::
        [Criar um cliente de partilha usando uma cadeia de conexão](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L18)
    :::column-end:::
    :::column:::
        [Criar uma partilha de ficheiros](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L21)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Obter um cliente de arquivo](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L29)
    :::column-end:::
    :::column:::
        [Carregar um ficheiro](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L31)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Definir metadados num ficheiro](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L33)
    :::column-end:::
    :::column:::
        [Obter as propriedades do ficheiro](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L36)
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        [Transferir um ficheiro](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L43)
    :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Bibliotecas de amostras de código azul

Para ver as bibliotecas completas da amostra C++, aceda a:

* [Amostras de código Azure Blob](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
* [Amostras de código do Lago de Dados Azure](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-datalake/sample)
* [Amostras de código de arquivos Azure](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-shares/sample)

Pode navegar e clonar o repositório GitHub para cada biblioteca.

## <a name="getting-started-guides"></a>Guias de introdução

Consulte os seguintes guias se estiver à procura de instruções sobre como instalar e começar com as Bibliotecas do Cliente de Armazenamento Azure.

* [Quickstart: Biblioteca de armazenamento Azure Blob v12 - C++](../blobs/quickstart-blobs-c-plus-plus.md)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre amostras para outras línguas:

* .NET: [Amostras de armazenamento Azure usando .NET](storage-samples-dotnet.md)
* Java: [Amostras de armazenamento Azure usando Java](storage-samples-java.md)
* Python: [Amostras de armazenamento Azure usando Python](storage-samples-python.md)
* JavaScript/Node.js: [Amostras de armazenamento de Azure usando JavaScript](storage-samples-javascript.md)
* Todas as outras línguas: [amostras de armazenamento Azure](storage-samples.md)
