---
title: Exemplos de armazenamento do Azure usando .NET | Microsoft Docs
description: Exiba, baixe e execute o código de exemplo e os aplicativos para o armazenamento do Azure. Descubra exemplos de introdução para BLOBs, filas, tabelas e arquivos, usando as bibliotecas de cliente de armazenamento do .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 50c5067c3db2f07da225b72d9ba0a8f0bdc44368
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748132"
---
# <a name="azure-storage-samples-using-net"></a>Exemplos de armazenamento do Azure usando .NET

A tabela a seguir fornece uma visão geral do nosso repositório de exemplos e dos cenários abordados em cada exemplo. Clique nos links para exibir o código de exemplo correspondente no GitHub.

> [!NOTE]
> Esses exemplos usam a biblioteca .NET v11 do armazenamento do Azure. Para o código V12, consulte [exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples) no repositório github.

## <a name="blob-samples-v11"></a>Amostras de BLOB (v11)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Acrescentar blob | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Blob de Blocos | [Aplicativo Web da Galeria de fotos do armazenamento de BLOBs do Azure](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Encriptação do Lado do Cliente | [Amostras de criptografia de BLOB](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Copiar Blob | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Criar contentor | [Aplicativo Web da Galeria de fotos do armazenamento de BLOBs do Azure](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Eliminar Blob | [Aplicativo Web da Galeria de fotos do armazenamento de BLOBs do Azure](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Eliminar contentor | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Metadados/propriedades/estatísticas de BLOB | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| ACL/metadados/Propriedades do contêiner | [Aplicativo Web da Galeria de fotos do armazenamento de BLOBs do Azure](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Obter intervalos de página | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Blob/contêiner de concessão | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Listar blob/contêiner | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Blob de páginas | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Propriedades do Serviço | [Introdução com BLOBs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Blob de instantâneo | [Fazer backup de discos de máquina virtual do Azure com instantâneos incrementais](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples-v11"></a>Amostras de arquivo (v11)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Criar compartilhamentos/diretórios/arquivos | [Exemplo de armazenamento de arquivos .NET do armazenamento do Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Excluir compartilhamentos/diretórios/arquivos | [Introdução com o serviço de arquivos do Azure no .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Propriedades/metadados do diretório | [Exemplo de armazenamento de arquivos .NET do armazenamento do Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Baixar arquivos | [Exemplo de armazenamento de arquivos .NET do armazenamento do Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Propriedades/metadados/métricas de arquivo | [Exemplo de armazenamento de arquivos .NET do armazenamento do Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Propriedades do serviço de arquivo | [Exemplo de armazenamento de arquivos .NET do armazenamento do Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Listar diretórios e arquivos | [Exemplo de armazenamento de arquivos .NET do armazenamento do Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Listar compartilhamentos | [Exemplo de armazenamento de arquivos .NET do armazenamento do Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Compartilhar Propriedades/metadados/estatísticas | [Exemplo de armazenamento de arquivos .NET do armazenamento do Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples-v11"></a>Amostras de fila (v11)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Adicionar Mensagem | [Introdução com o serviço fila do Azure no .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Encriptação do Lado do Cliente | [Criptografia do lado do cliente da fila .NET do armazenamento do Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Criar filas | [Introdução com o serviço fila do Azure no .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Excluir mensagem/fila | [Introdução com o serviço fila do Azure no .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Inspecionar mensagem | [Introdução com o serviço fila do Azure no .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| ACL/metadados/estatísticas da fila | [Introdução com o serviço fila do Azure no .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Propriedades do serviço fila | [Introdução com o serviço fila do Azure no .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Atualizar mensagem | [Introdução com o serviço fila do Azure no .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples-v11"></a>Exemplos de tabela (v11)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Criar a Tabela | [Gerenciando a simultaneidade usando o armazenamento do Azure-aplicativo de exemplo](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Excluir entidade/tabela | [Introdução ao Armazenamento de Tabelas do Azure no .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Inserir/mesclar/substituir entidade | [Gerenciando a simultaneidade usando o armazenamento do Azure-aplicativo de exemplo](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Consultar entidades | [Introdução ao Armazenamento de Tabelas do Azure no .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Tabelas de consulta | [Introdução ao Armazenamento de Tabelas do Azure no .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| ACL/Propriedades de tabela | [Introdução ao Armazenamento de Tabelas do Azure no .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| Atualizar entidade | [Gerenciando a simultaneidade usando o armazenamento do Azure-aplicativo de exemplo](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Biblioteca de exemplos de código do Azure

Para exibir a biblioteca de exemplo completa, vá para a biblioteca de [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage) , que inclui exemplos de armazenamento do Azure que você pode baixar e executar localmente. A biblioteca de exemplo de código fornece código de exemplo no formato. zip. Como alternativa, você pode procurar e clonar o repositório GitHub para cada exemplo.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os guias a seguir se você estiver procurando instruções sobre como instalar e começar a usar as bibliotecas de cliente de armazenamento do Azure.

* [Introdução com o serviço blob do Azure no .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Introdução com o serviço fila do Azure no .NET](../storage-dotnet-how-to-use-queues.md)
* [Introdução com o serviço tabela do Azure no .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introdução com o serviço de arquivos do Azure no .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre exemplos de outros idiomas:

* Java: [exemplos de armazenamento do Azure usando Java](storage-samples-java.md)
* JavaScript/node. js: [exemplos de armazenamento do Azure usando JavaScript](storage-samples-javascript.md)
* Python: [exemplos de armazenamento do Azure usando Python](storage-samples-python.md)
* Todos os outros idiomas: [exemplos de armazenamento do Azure](../storage-samples.md)
