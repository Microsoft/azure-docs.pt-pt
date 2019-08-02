---
title: Exemplos de armazenamento do Azure usando Java | Microsoft Docs
description: Exiba, baixe e execute o código de exemplo e os aplicativos para o armazenamento do Azure. Descubra exemplos de introdução para BLOBs, filas, tabelas e arquivos usando as bibliotecas de cliente de armazenamento Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 4348400e66e4b975d1eeb899cab8bb5024260149
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721841"
---
# <a name="azure-storage-samples-using-java"></a>Exemplos de armazenamento do Azure usando Java

## <a name="java-sample-index"></a>Índice de exemplo de Java

A tabela a seguir fornece uma visão geral do nosso repositório de exemplos e dos cenários abordados em cada exemplo. Clique nos links para exibir o código de exemplo correspondente no GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Ponto Final</th><th style="font-size:110%">Cenário</th><th style="font-size:110%">Código de exemplo</th></tr></thead><tbody>
<tr>
<td rowspan="16"><b>Blob</b></td>
<td>Acrescentar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Blob de Blocos</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Encriptação do Lado do Cliente</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Introdução com a criptografia do lado do cliente do Azure em Java</a></td>
</tr>
<tr>
<td>Copiar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Criar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Eliminar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Eliminar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Metadados/propriedades/estatísticas de BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>ACL/metadados/Propriedades do contêiner</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Obter intervalos de página</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Blob/contêiner de concessão</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Listar blob/contêiner</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Blob de Página</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>RÍGIDO</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">Exemplo de testes de SAS</a></td>
</tr>   
<tr>
<td>Propriedades do Serviço</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td>Blob de instantâneo</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução com o serviço blob do Azure em Java</a></td>
</tr>
<tr>
<td rowspan="9"><b>Ficheiro</b></td>
<td>Criar compartilhamentos/diretórios/arquivos</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td>Excluir compartilhamentos/diretórios/arquivos</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td>Propriedades/metadados do diretório</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td>Baixar arquivos</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td>Propriedades/metadados/métricas de arquivo</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td>Propriedades do serviço de arquivo</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td>Listar diretórios e arquivos</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td>Listar compartilhamentos</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td>Compartilhar Propriedades/metadados/estatísticas</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução com o serviço de arquivos do Azure em Java</a></td>
</tr>
<tr>
<td rowspan="8"><b>Espera</b></td>
<td>Adicionar mensagem</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63">Introdução com o serviço fila do Azure em Java</a></td>
</tr>
<tr>
<td>Encriptação do Lado do Cliente</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java">Introdução com a criptografia do lado do cliente do Azure em Java</a></td>
</tr>
<tr>
<td>Criar filas</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução com o serviço fila do Azure em Java</a></td>
</tr>
<tr>
<td>Excluir mensagem/fila</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução com o serviço fila do Azure em Java</a></td>
</tr>
<tr>
<td>Inspecionar mensagem</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução com o serviço fila do Azure em Java</a></td>
</tr>
<tr>
<td>ACL/metadados/estatísticas da fila</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Introdução com o serviço fila do Azure em Java</a></td>
</tr>
<tr>
<td>Propriedades do serviço fila</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Introdução com o serviço fila do Azure em Java</a></td>
</tr>
<tr>
<td>Atualizar mensagem</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução com o serviço fila do Azure em Java</a></td>
</tr>
<tr>
<td rowspan="7"><b>Tabela</b></td>
<td>Criar tabela</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>Excluir entidade/tabela</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>Inserir/mesclar/substituir entidade</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>Consultar entidades</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>Tabelas de consulta</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>ACL/Propriedades de tabela</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>Atualizar entidade</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
</tbody>
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteca de exemplos de código do Azure

Para exibir a biblioteca de exemplo completa, vá para a biblioteca de [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage) , que inclui exemplos de armazenamento do Azure que você pode baixar e executar localmente. A biblioteca de exemplo de código fornece código de exemplo no formato. zip. Como alternativa, você pode procurar e clonar o repositório GitHub para cada exemplo.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os guias a seguir se você estiver procurando instruções sobre como instalar e começar a usar as bibliotecas de cliente de armazenamento do Azure.

* [Introdução com o serviço blob do Azure em Java](../blobs/storage-quickstart-blobs-java.md)
* [Introdução com o serviço fila do Azure em Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Getting Started with Azure Table Service in Java](../../cosmos-db/table-storage-how-to-use-java.md) (Introdução ao Serviço Tabela do Azure em Java)
* [Introdução com o serviço de arquivos do Azure em Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre exemplos de outros idiomas:

* .NET: [Exemplos de Armazenamento do Microsoft Azure com o .NET](storage-samples-dotnet.md)
* Todos os outros idiomas: [Exemplos de armazenamento do Azure](storage-samples.md)
