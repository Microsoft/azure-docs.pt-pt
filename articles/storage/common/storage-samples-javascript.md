---
title: Exemplos de armazenamento do Azure usando JavaScript | Microsoft Docs
description: Exiba, baixe e execute o código de exemplo e os aplicativos para o armazenamento do Azure. Descubra exemplos de introdução para BLOBs, filas, tabelas e arquivos usando as bibliotecas de cliente de armazenamento do JavaScript/node. js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 44fe68b8b04a1192c928e04c7d2a9d147f400130
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748285"
---
# <a name="azure-storage-samples-using-javascript"></a>Exemplos de armazenamento do Azure usando JavaScript

As tabelas a seguir fornecem uma visão geral do nosso repositório de exemplos e dos cenários abordados em cada exemplo. Clique nos links para exibir o código de exemplo correspondente no GitHub.

> [!NOTE]
> Esses exemplos usam a biblioteca de V10 do JavaScript do armazenamento do Azure. Para o código V12, consulte [exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples) no repositório github.

## <a name="blob-samples-v10"></a>Amostras de BLOB (V10)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Blob de Blocos | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| Encriptação do Lado do Cliente | [Gerenciando chaves de conta de armazenamento no valor de chave do Azure com JavaScript](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Copiar Blob | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| Criar contentor | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| Eliminar Blob | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| Eliminar contentor | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| Metadados de BLOB | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| Propriedades do blob | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| ACL de contêiner | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| Metadados do contêiner | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| Propriedades do contêiner | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| Obter intervalos de página | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| Lease Blob (Blob de Concessão) | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| Contêiner de concessão | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| Listar blob/contêiner | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| Blob de páginas | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [Assinatura de acesso compartilhado em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| Propriedades do Serviço | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| Definir regras de CORS | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| Blob de instantâneo | [Introdução com o serviço blob do Azure em JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples-v10"></a>Amostras de arquivo (V10)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Criar compartilhamentos/diretórios/arquivos | [Introdução com o serviço de arquivos do Azure em JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| Excluir compartilhamentos/diretórios/arquivos | [Introdução com o serviço de arquivos do Azure em JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| Baixar arquivos | [Introdução com o serviço de arquivos do Azure em JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| Listar diretórios e arquivos | [Introdução com o serviço de arquivos do Azure em JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| Listar compartilhamentos | [Introdução com o serviço de arquivos do Azure em JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples-v10"></a>Amostras de fila (V10)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Adicionar Mensagem | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| Encriptação do Lado do Cliente | [Gerenciando chaves de conta de armazenamento no Azure Key Vault com JavaScript](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Criar filas | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| Excluir mensagem | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| Excluir fila | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| Listar filas | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| Inspecionar mensagem | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| ACL de fila | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| Regras de CORS da fila | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| Metadados da fila | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| Propriedades do serviço fila | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| Estatísticas de fila | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| Atualizar mensagem | [Introdução com o serviço fila do Azure em JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples-v10"></a>Exemplos de tabela (V10)

| **Cenário** | **Código de Exemplo** |
|--------------|-----------------|
| Entidades do lote | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| Criar a Tabela | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| Excluir entidade/tabela | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| Inserir/mesclar/substituir entidade | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| Listar tabelas | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| Consultar entidades | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| Tabelas de consulta | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| Consulta de intervalo | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [Assinatura de acesso compartilhado em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| ACL de tabela | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| Regras de CORS da tabela | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| Propriedades da Tabela | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| Estatísticas de tabela | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| Atualizar entidade | [Introdução com o serviço tabela do Azure em JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Biblioteca de exemplos de código do Azure

Para exibir a biblioteca de exemplo completa, vá para a biblioteca de [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage) , que inclui exemplos de armazenamento do Azure que você pode baixar e executar localmente. A biblioteca de exemplo de código fornece código de exemplo no formato. zip. Como alternativa, você pode procurar e clonar o repositório GitHub para cada exemplo.

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os guias a seguir se você estiver procurando instruções sobre como instalar e começar a usar as bibliotecas de cliente de armazenamento do Azure.

* [Introdução com o serviço blob do Azure em JavaScript](../blobs/storage-quickstart-blobs-nodejs.md)
* [Introdução com o serviço fila do Azure em JavaScript](../queues/storage-nodejs-how-to-use-queues.md)
* [Introdução com o serviço tabela do Azure em JavaScript](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre exemplos de outros idiomas:

* .NET: [exemplos de armazenamento do Azure usando .net](storage-samples-dotnet.md)
* Java: [exemplos de armazenamento do Azure usando Java](storage-samples-java.md)
* Python: [exemplos de armazenamento do Azure usando Python](storage-samples-python.md)
* Todos os outros idiomas: [exemplos de armazenamento do Azure](storage-samples.md)
