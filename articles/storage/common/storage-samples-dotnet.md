---
title: Exemplos de armazenamento do Azure usando .NET | Microsoft Docs
description: Exiba, baixe e execute o código de exemplo e os aplicativos para o armazenamento do Azure. Descubra exemplos de introdução para BLOBs, filas, tabelas e arquivos, usando as bibliotecas de cliente de armazenamento do .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: cec6b0498adf55428fade7ae00db6550496a20bd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721843"
---
# <a name="azure-storage-samples-using-net"></a>Exemplos de armazenamento do Azure usando .NET

## <a name="net-sample-index"></a>Índice de exemplo do .NET

A tabela a seguir fornece uma visão geral do nosso repositório de exemplos e dos cenários abordados em cada exemplo. Clique nos links para exibir o código de exemplo correspondente no GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Ponto Final</th><th style="font-size:110%">Cenário</th><th style="font-size:110%">Código de exemplo</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Acrescentar Blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144">Introdução com BLOBs</a></td> 
</tr> 
<tr> 
<td>Blob de Blocos</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicativo Web da Galeria de fotos do armazenamento de BLOBs do Azure</a></td>
</tr> 
<tr> 
<td>Encriptação do Lado do Cliente</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Amostras de criptografia de BLOB</a></td>
</tr> 
<tr> 
<td>Copiar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com BLOBs</a></td>
</tr> 
<tr> 
<td>Criar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicativo Web da Galeria de fotos do armazenamento de BLOBs do Azure</a></td>
</tr> 
<tr> 
<td>Eliminar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicativo Web da Galeria de fotos do armazenamento de BLOBs do Azure</a></td>
</tr> 
<tr> 
<td>Eliminar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com BLOBs</a></td>
</tr> 
<tr> 
<td>Metadados/propriedades/estatísticas de BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com BLOBs</a></td>
</tr> 
<tr> 
<td>ACL/metadados/Propriedades do contêiner</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicativo Web da Galeria de fotos do armazenamento de BLOBs do Azure</a></td>
</tr> 
<tr> 
<td>Obter intervalos de página</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com BLOBs</a></td>
</tr> 
<tr> 
<td>Blob/contêiner de concessão</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com BLOBs</a></td>
</tr> 
<tr> 
<td>Listar blob/contêiner</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Introdução com BLOBs</a></td>
</tr> 
<tr> 
<td>Blob de Página</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Introdução com BLOBs</a></td>
</tr>
<tr> 
<td>RÍGIDO</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com BLOBs</a></td>
</tr>   
<tr> 
<td>Propriedades do Serviço</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução com BLOBs</a></td>
</tr>           
<tr> 
<td>Blob de instantâneo</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Fazer backup de discos de máquina virtual do Azure com instantâneos incrementais</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Ficheiro</b></td>
<td>Criar compartilhamentos/diretórios/arquivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de armazenamento de arquivos .NET do armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Excluir compartilhamentos/diretórios/arquivos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Introdução com o serviço de arquivos do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Propriedades/metadados do diretório</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de arquivos .NET do armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Baixar arquivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de armazenamento de arquivos .NET do armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Propriedades/metadados/métricas de arquivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de arquivos .NET do armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço de arquivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de arquivos .NET do armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Listar diretórios e arquivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de armazenamento de arquivos .NET do armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Listar compartilhamentos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de arquivos .NET do armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Compartilhar Propriedades/metadados/estatísticas</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de armazenamento de arquivos .NET do armazenamento do Azure</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Espera</b></td>
<td>Adicionar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução com o serviço fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Encriptação do Lado do Cliente</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Criptografia do lado do cliente da fila .NET do armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Criar filas</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução com o serviço fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Excluir mensagem/fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução com o serviço fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Inspecionar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução com o serviço fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>ACL/metadados/estatísticas da fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Introdução com o serviço fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Introdução com o serviço fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Atualizar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução com o serviço fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Criar tabela</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerenciando a simultaneidade usando o armazenamento do Azure-aplicativo de exemplo</a></td> 
</tr> 
<tr> 
<td>Excluir entidade/tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introdução ao Armazenamento de Tabelas do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Inserir/mesclar/substituir entidade</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerenciando a simultaneidade usando o armazenamento do Azure-aplicativo de exemplo</a></td> 
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
<td>ACL/Propriedades de tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Introdução ao Armazenamento de Tabelas do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Atualizar entidade</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerenciando a simultaneidade usando o armazenamento do Azure-aplicativo de exemplo</a></td> 
</tr> 
</tbody> 
</table>
<br/>

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

* Java: [Exemplos de Armazenamento do Microsoft Azure com o Java](storage-samples-java.md)
* Todos os outros idiomas: [Exemplos de armazenamento do Azure](../storage-samples.md)
