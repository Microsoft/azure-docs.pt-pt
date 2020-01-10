---
title: Exemplos de armazenamento do Azure usando Python | Microsoft Docs
description: Exiba, baixe e execute o código de exemplo e os aplicativos para o armazenamento do Azure. Descubra exemplos de introdução para BLOBs, filas, tabelas e arquivos usando as bibliotecas de cliente de armazenamento do Python.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 7f694ee51989023a3e7a72f40700edcbb6a97bae
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747982"
---
# <a name="azure-storage-samples-using-python"></a>Exemplos de armazenamento do Azure usando Python

As tabelas a seguir fornecem uma visão geral do nosso repositório de exemplos e dos cenários abordados em cada exemplo. Clique nos links para exibir o código de exemplo correspondente no GitHub.

> [!NOTE]
> Esses exemplos usam a Biblioteca Python v 2.1 do armazenamento do Azure. Para o código V12, consulte [exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples) no repositório github.

## <a name="blob-samples-v21"></a>Amostras de BLOB (v 2.1)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Acrescentar blob | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| Blob de Blocos | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| Encriptação do Lado do Cliente | [Gerenciando chaves de conta de armazenamento no valor de chave do Azure com Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Copiar Blob | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| Criar contentor | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| Eliminar Blob | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| Eliminar contentor | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| Metadados/propriedades/estatísticas de BLOB | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| ACL/metadados/Propriedades do contêiner | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| Obter intervalos de página | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| Blob/contêiner de concessão | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| Listar blob/contêiner | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| Blob de páginas | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Assinatura de acesso compartilhado no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| Propriedades do Serviço | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Blob de instantâneo | [Introdução com o serviço blob do Azure no Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples-v21"></a>Amostras de arquivo (v 2.1)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Criar compartilhamentos/diretórios/arquivos | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| Excluir compartilhamentos/diretórios/arquivos | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| Propriedades/metadados do diretório | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| Baixar arquivos | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| Propriedades/metadados/métricas de arquivo | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| Propriedades do serviço de arquivo | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| Listar diretórios e arquivos | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| Listar compartilhamentos | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| Compartilhar Propriedades/metadados/estatísticas | [Introdução com o serviço de arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples-v21"></a>Exemplos de fila (v 2.1)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Adicionar Mensagem | [Introdução com o serviço fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| Encriptação do Lado do Cliente | [Gerenciando chaves de conta de armazenamento em Azure Key Vault com Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Criar filas | [Introdução com o serviço fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| Excluir mensagem/fila | [Introdução com o serviço fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| Inspecionar mensagem | [Introdução com o serviço fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| ACL/metadados/estatísticas da fila | [Introdução com o serviço fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| Propriedades do serviço fila | [Introdução com o serviço fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| Atualizar mensagem | [Introdução com o serviço fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples-v21"></a>Exemplos de tabela (v 2.1)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Criar a Tabela | [Introdução com o serviço tabela do Azure no Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| Excluir entidade/tabela | [Introdução com o serviço tabela do Azure no Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| Inserir/mesclar/substituir entidade | [Introdução com o serviço tabela do Azure no Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| Consultar entidades | [Introdução com o serviço tabela do Azure no Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| Tabelas de consulta | [Introdução com o serviço tabela do Azure no Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| ACL/Propriedades de tabela | [Introdução com o serviço tabela do Azure no Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| Atualizar entidade | [Introdução com o serviço tabela do Azure no Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Biblioteca de exemplos de código do Azure

Para exibir a biblioteca de exemplo completa, vá para a biblioteca de [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage) , que inclui exemplos de armazenamento do Azure que você pode baixar e executar localmente. A biblioteca de exemplo de código fornece código de exemplo no formato. zip. Como alternativa, você pode procurar e clonar o repositório GitHub para cada exemplo.

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os guias a seguir se você estiver procurando instruções sobre como instalar e começar a usar as bibliotecas de cliente de armazenamento do Azure.

* [Introdução com o serviço blob do Azure no Python](../blobs/storage-quickstart-blobs-python.md)
* [Introdução com o serviço fila do Azure em Python](../queues/storage-python-how-to-use-queue-storage.md)
* [Introdução com o serviço tabela do Azure no Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Introdução com o serviço de arquivos do Azure no Python](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre exemplos de outros idiomas:

* .NET: [exemplos de armazenamento do Azure usando .net](storage-samples-dotnet.md)
* Java: [exemplos de armazenamento do Azure usando Java](storage-samples-java.md)
* JavaScript/node. js: [exemplos de armazenamento do Azure usando JavaScript](storage-samples-javascript.md)
* Todos os outros idiomas: [exemplos de armazenamento do Azure](storage-samples.md)
