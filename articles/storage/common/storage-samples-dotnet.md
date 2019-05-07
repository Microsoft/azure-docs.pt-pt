---
title: Exemplos de armazenamento do Azure com .NET | Documentos da Microsoft
description: Veja, transfira e execute o código de exemplo e aplicações para o armazenamento do Azure. Descubra exemplos para blobs, filas, tabelas e ficheiros, utilizar as bibliotecas de cliente de armazenamento de .NET de introdução.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: mhopkins
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 3874bf4e4b51354f14c9aa6357ee1c0d9033e545
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142560"
---
# <a name="azure-storage-samples-using-net"></a>Exemplos de armazenamento do Azure com .NET

## <a name="net-sample-index"></a>Índice de exemplo do .NET

A tabela seguinte fornece uma visão geral do nosso repositório de exemplos e os cenários abordados em cada exemplo. Clique nos links para ver o código de exemplo correspondente no GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Ponto Final</th><th style="font-size:110%">Cenário</th><th style="font-size:110%">Código de Exemplo</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Acrescentar Blob</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">Exemplo do método CloudBlobContainer.GetAppendBlobReference</a></td> 
</tr> 
<tr> 
<td>Blob de Blocos</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicação Web de Galeria de fotos de armazenamento de Blobs do Azure</a></td>
</tr> 
<tr> 
<td>Encriptação do Lado do Cliente</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Exemplos de encriptação de BLOBs</a></td>
</tr> 
<tr> 
<td>Copiar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com Blobs</a></td>
</tr> 
<tr> 
<td>Criar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicação Web de Galeria de fotos de armazenamento de Blobs do Azure</a></td>
</tr> 
<tr> 
<td>Eliminar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicação Web de Galeria de fotos de armazenamento de Blobs do Azure</a></td>
</tr> 
<tr> 
<td>Eliminar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com Blobs</a></td>
</tr> 
<tr> 
<td>Metadados de blob/propriedades/estatísticas</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com Blobs</a></td>
</tr> 
<tr> 
<td>ACL/metadados/as propriedades do contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicação Web de Galeria de fotos de armazenamento de Blobs do Azure</a></td>
</tr> 
<tr> 
<td>Os intervalos obter páginas</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com Blobs</a></td>
</tr> 
<tr> 
<td>Concessão/contentor de BLOBs</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com Blobs</a></td>
</tr> 
<tr> 
<td>Listar Blob/contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Introdução com Blobs</a></td>
</tr> 
<tr> 
<td>Blob de Página</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Introdução com Blobs</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com Blobs</a></td>
</tr>   
<tr> 
<td>Propriedades do Serviço</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com Blobs</a></td>
</tr>           
<tr> 
<td>Blob de instantâneo</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Discos de cópia de segurança Máquina Virtual do Azure com instantâneos incrementais</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Ficheiro</b></td>
<td>Criar partilhas/diretórios/ficheiros</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de armazenamento de ficheiros de .NET de armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Eliminar partilhas/diretórios/ficheiros</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Introdução ao serviço de ficheiros do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Propriedades/metadados do diretório</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de ficheiros de .NET de armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Transferir ficheiros</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de armazenamento de ficheiros de .NET de armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Propriedades/metadados/métricas de ficheiros</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de ficheiros de .NET de armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço de arquivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de ficheiros de .NET de armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Lista de diretórios e arquivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de armazenamento de ficheiros de .NET de armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Listar partilhas</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de ficheiros de .NET de armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Partilhar propriedades/metadados/estatísticas</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de ficheiros de .NET de armazenamento do Azure</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>fila</b></td>
<td>Adicionar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao serviço de fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Encriptação do Lado do Cliente</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Encriptação do lado do cliente de fila de .NET de armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Criar filas</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao serviço de fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Eliminar a fila de mensagens /</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao serviço de fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Observar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao serviço de fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>ACL/metadados/estatísticas da fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Introdução ao serviço de fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Introdução ao serviço de fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Atualizar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao serviço de fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Criar tabela</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerir a simultaneidade com o armazenamento do Azure - aplicação de exemplo</a></td> 
</tr> 
<tr> 
<td>Eliminar entidade/tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introdução ao Armazenamento de Tabelas do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Entidade de inserção/intercalação/substituir</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerir a simultaneidade com o armazenamento do Azure - aplicação de exemplo</a></td> 
</tr> 
<tr> 
<td>Consultar entidades</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introdução ao Armazenamento de Tabelas do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Tabelas de consulta</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introdução ao Armazenamento de Tabelas do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>ACL/propriedades da tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Introdução ao Armazenamento de Tabelas do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Atualizar a entidade</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerir a simultaneidade com o armazenamento do Azure - aplicação de exemplo</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteca de exemplos de código do Azure

Para ver a biblioteca de exemplo completo, vá para o [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage) biblioteca, que inclui exemplos de armazenamento do Azure que pode transferir e executar localmente. A biblioteca de exemplo de código fornece código de exemplo no formato. zip. Em alternativa, pode procurar e clone o repositório do GitHub para cada exemplo.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os seguintes guias se estiver à procura para obter instruções sobre como instalar e começar a utilizar com as bibliotecas de cliente de armazenamento do Azure.

* [Introdução ao serviço de Blobs do Azure no .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Introdução ao serviço de fila do Azure no .NET](../storage-dotnet-how-to-use-queues.md)
* [Introdução ao serviço de tabela do Azure no .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introdução ao serviço de ficheiros do Azure no .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Passos Seguintes

Para informações sobre exemplos para outros idiomas:

* Java: [Exemplos de Armazenamento do Microsoft Azure com o Java](storage-samples-java.md)
* Todos os outros idiomas: [Exemplos de armazenamento do Azure](../storage-samples.md)
