---
title: Exemplos de armazenamento do Azure com Java | Documentos da Microsoft
description: Veja, transfira e execute o código de exemplo e aplicações para o armazenamento do Azure. Descubra exemplos para blobs, filas, tabelas e ficheiros, usando as bibliotecas de cliente de armazenamento de Java de introdução.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/03/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: 3d241f1905244d3a8039372262f84ba0fd25220d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209781"
---
# <a name="azure-storage-samples-using-java"></a>Exemplos de armazenamento do Azure com Java

## <a name="java-sample-index"></a>Índice de exemplo de Java

A tabela seguinte fornece uma visão geral do nosso repositório de exemplos e os cenários abordados em cada exemplo. Clique nos links para ver o código de exemplo correspondente no GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Ponto Final</th><th style="font-size:110%">Cenário</th><th style="font-size:110%">Código de Exemplo</th></tr></thead><tbody>
<tr>
<td rowspan="16"><b>Blob</b></td>
<td>Acrescentar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Blob de Blocos</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Encriptação do Lado do Cliente</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Introdução a encriptação do lado do cliente do Azure em Java</a></td>
</tr>
<tr>
<td>Copiar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Criar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Eliminar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Eliminar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Metadados de blob/propriedades/estatísticas</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>ACL/metadados/as propriedades do contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Os intervalos obter páginas</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Concessão/contentor de BLOBs</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Listar Blob/contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Blob de Página</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">Exemplo de testes SAS</a></td>
</tr>   
<tr>
<td>Propriedades do Serviço</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td>Blob de instantâneo</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço de Blobs do Azure em Java</a></td>
</tr>
<tr>
<td rowspan="9"><b>Ficheiro</b></td>
<td>Criar partilhas/diretórios/ficheiros</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td>Eliminar partilhas/diretórios/ficheiros</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td>Propriedades/metadados do diretório</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td>Transferir ficheiros</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td>Propriedades/metadados/métricas de ficheiros</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td>Propriedades do serviço de arquivo</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td>Lista de diretórios e arquivos</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td>Listar partilhas</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td>Partilhar propriedades/metadados/estatísticas</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao serviço de ficheiros do Azure em Java</a></td>
</tr>
<tr>
<td rowspan="8"><b>fila</b></td>
<td>Adicionar mensagem</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63">Introdução ao serviço de fila do Azure em Java</a></td>
</tr>
<tr>
<td>Encriptação do Lado do Cliente</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java">Introdução a encriptação do lado do cliente do Azure em Java</a></td>
</tr>
<tr>
<td>Criar filas</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao serviço de fila do Azure em Java</a></td>
</tr>
<tr>
<td>Eliminar a fila de mensagens /</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao serviço de fila do Azure em Java</a></td>
</tr>
<tr>
<td>Observar mensagem</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao serviço de fila do Azure em Java</a></td>
</tr>
<tr>
<td>ACL/metadados/estatísticas da fila</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Introdução ao serviço de fila do Azure em Java</a></td>
</tr>
<tr>
<td>Propriedades do serviço fila</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Introdução ao serviço de fila do Azure em Java</a></td>
</tr>
<tr>
<td>Atualizar mensagem</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao serviço de fila do Azure em Java</a></td>
</tr>
<tr>
<td rowspan="7"><b>Tabela</b></td>
<td>Criar tabela</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>Eliminar entidade/tabela</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>Entidade de inserção/intercalação/substituir</td>
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
<td>ACL/propriedades da tabela</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
<tr>
<td>Atualizar a entidade</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Getting Started with Azure Table Service in Java</a> (Introdução ao Serviço Tabela do Azure em Java)</td>
</tr>
</tbody>
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteca de exemplos de código do Azure

Para ver a biblioteca de exemplo completo, vá para o [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage) biblioteca, que inclui exemplos de armazenamento do Azure que pode transferir e executar localmente. A biblioteca de exemplo de código fornece código de exemplo no formato. zip. Em alternativa, pode procurar e clone o repositório do GitHub para cada exemplo.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os seguintes guias se estiver à procura para obter instruções sobre como instalar e começar a utilizar com as bibliotecas de cliente de armazenamento do Azure.

* [Introdução ao serviço de Blobs do Azure em Java](../blobs/storage-quickstart-blobs-java.md)
* [Introdução ao serviço de fila do Azure em Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Getting Started with Azure Table Service in Java](../../cosmos-db/table-storage-how-to-use-java.md) (Introdução ao Serviço Tabela do Azure em Java)
* [Introdução ao serviço de ficheiros do Azure em Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Passos Seguintes

Para informações sobre exemplos para outros idiomas:

* .NET: [Exemplos de Armazenamento do Microsoft Azure com o .NET](storage-samples-dotnet.md)
* Todos os outros idiomas: [Exemplos de armazenamento do Azure](storage-samples.md)
