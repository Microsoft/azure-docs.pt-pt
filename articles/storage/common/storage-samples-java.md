---
title: Exemplos de armazenamento do Azure usando Java | Microsoft Docs
description: Exiba, baixe e execute o código de exemplo e os aplicativos para o armazenamento do Azure. Descubra exemplos de introdução para BLOBs, filas, tabelas e arquivos usando as bibliotecas de cliente de armazenamento Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: dfdde1ae981dcd2d539dec3667e44e90cef4d1c8
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748040"
---
# <a name="azure-storage-samples-using-java"></a>Exemplos de armazenamento do Azure usando Java

A tabela a seguir fornece uma visão geral do nosso repositório de exemplos e dos cenários abordados em cada exemplo. Clique nos links para exibir o código de exemplo correspondente no GitHub.

> [!NOTE]
> Esses exemplos usam a biblioteca Java v11 do armazenamento do Azure. Para o código V12, consulte [exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob) no repositório github.

## <a name="blob-samples-v11"></a>Amostras de BLOB (v11)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Acrescentar blob | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blob de Blocos | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Encriptação do Lado do Cliente | [Introdução com a criptografia do lado do cliente do Azure em Java](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| Copiar Blob | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Criar contentor | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Eliminar Blob | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Eliminar contentor | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Metadados/propriedades/estatísticas de BLOB | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| ACL/metadados/Propriedades do contêiner | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Obter intervalos de página | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| Blob/contêiner de concessão | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Listar blob/contêiner | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blob de páginas | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [Exemplo de testes de SAS](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| Propriedades do Serviço | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Blob de instantâneo | [Introdução com o serviço blob do Azure em Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples-v11"></a>Amostras de arquivo (v11)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Criar compartilhamentos/diretórios/arquivos | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Excluir compartilhamentos/diretórios/arquivos | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Propriedades/metadados do diretório | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Baixar arquivos | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Propriedades/metadados/métricas de arquivo | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Propriedades do serviço de arquivo | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Listar diretórios e arquivos | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Listar compartilhamentos | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Compartilhar Propriedades/metadados/estatísticas | [Introdução com o serviço de arquivos do Azure em Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples-v11"></a>Amostras de fila (v11)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Adicionar Mensagem | [Introdução com o serviço fila do Azure em Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| Encriptação do Lado do Cliente | [Introdução com a criptografia do lado do cliente do Azure em Java](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| Criar filas | [Introdução com o serviço fila do Azure em Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Excluir mensagem/fila | [Introdução com o serviço fila do Azure em Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Inspecionar mensagem | [Introdução com o serviço fila do Azure em Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| ACL/metadados/estatísticas da fila | [Introdução com o serviço fila do Azure em Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Propriedades do serviço fila | [Introdução com o serviço fila do Azure em Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Atualizar mensagem | [Introdução com o serviço fila do Azure em Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples-v11"></a>Exemplos de tabela (v11)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Criar a Tabela | [Getting Started with Azure Table Service in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) (Introdução ao Serviço Tabela do Azure em Java) |
| Excluir entidade/tabela | [Getting Started with Azure Table Service in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) (Introdução ao Serviço Tabela do Azure em Java) |
| Inserir/mesclar/substituir entidade | [Getting Started with Azure Table Service in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) (Introdução ao Serviço Tabela do Azure em Java) |
| Consultar entidades | [Getting Started with Azure Table Service in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) (Introdução ao Serviço Tabela do Azure em Java) |
| Tabelas de consulta | [Getting Started with Azure Table Service in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) (Introdução ao Serviço Tabela do Azure em Java) |
| ACL/Propriedades de tabela | [Getting Started with Azure Table Service in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) (Introdução ao Serviço Tabela do Azure em Java) |
| Atualizar entidade | [Getting Started with Azure Table Service in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) (Introdução ao Serviço Tabela do Azure em Java) |
## <a name="azure-code-samples-library"></a>Biblioteca de exemplos de código do Azure

Para exibir a biblioteca de exemplo completa, vá para a biblioteca de [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage) , que inclui exemplos de armazenamento do Azure que você pode baixar e executar localmente. A biblioteca de exemplo de código fornece código de exemplo no formato. zip. Como alternativa, você pode procurar e clonar o repositório GitHub para cada exemplo.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os guias a seguir se você estiver procurando instruções sobre como instalar e começar a usar as bibliotecas de cliente de armazenamento do Azure.

* [Introdução com o serviço blob do Azure em Java](../blobs/storage-quickstart-blobs-java.md)
* [Introdução com o serviço fila do Azure em Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Getting Started with Azure Table Service in Java](../../cosmos-db/table-storage-how-to-use-java.md) (Introdução ao Serviço Tabela do Azure em Java)
* [Introdução com o serviço de arquivos do Azure em Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre exemplos de outros idiomas:

* .NET: [exemplos de armazenamento do Azure usando .net](storage-samples-dotnet.md)
* JavaScript/node. js: [exemplos de armazenamento do Azure usando JavaScript](storage-samples-javascript.md)
* Python: [exemplos de armazenamento do Azure usando Python](storage-samples-python.md)
* Todos os outros idiomas: [exemplos de armazenamento do Azure](storage-samples.md)
