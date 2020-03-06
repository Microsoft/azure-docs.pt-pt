---
title: Ferramentas de ingestão de dados
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as ferramentas e utilitários de ingestão de dados que estão pré-instalados na Máquina Virtual da Ciência dos Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390603"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados de Máquina Virtual de ciência de dados

Como um dos primeiros passos técnicos de um projeto de ciência de dados ou IA, você deve identificar os conjuntos de dados a serem usados e trazê-los para o seu ambiente de análise. A Máquina Virtual da Ciência dos Dados (DSVM) fornece ferramentas e bibliotecas para trazer dados de diferentes fontes para armazenamento de dados analíticos localmente no DSVM, ou para uma plataforma de dados, seja na nuvem ou no local.

Aqui estão algumas ferramentas de movimento de dados que estão disponíveis no DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados do armazenamento da Azure Blob na Azure Data Lake Store. Também pode copiar dados entre duas contas de Azure Data Lake Store.      |
| Versões DSVM suportadas      | Windows      |
| Utilizações típicas      | Importando várias bolhas do armazenamento azure blob para a Loja de Lagos De Dados Azure.      |
|  Como utilizar / executá-lo?    |   Abra um pedido de comando e escreva `adlcopy` para obter ajuda.    |
| Links para amostras      | [Utilizar o AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Ferramentas relacionadas no DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>CLI do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta de gerenciamento para o Azure. Também contém verbos de comando para mover dados de plataformas de dados azure como o armazenamento Azure Blob e a Azure Data Lake Store.     |
| Versões DSVM suportadas      | Windows, Linux     |
| Utilizações típicas      | Dados de importação e exportação de e para o Armazenamento Azure e loja de datas azure.      |
|  Como utilizar / executá-lo?    |   Abra um pedido de comando e escreva `az` para obter ajuda.    |
| Links para amostras      | [Utilizar a CLI do Azure](https://docs.microsoft.com/cli/azure)     |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados de e para ficheiros locais, armazenamento, ficheiros e tabelas da Azure Blob.      |
| Versões DSVM suportadas      | Windows      |
| Utilizações típicas      | Copiar ficheiros para armazenamento de Blob Azure e copiar bolhas entre contas.      |
|  Como utilizar / executá-lo?    |   Abra um pedido de comando e escreva `azcopy` para obter ajuda.    |
| Links para amostras      | [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Ferramentas relacionadas no DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Ferramenta de migração de dados do Cosmos DB do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta para importar dados de várias fontes para o Azure Cosmos DB, uma base de dados NoSQL na nuvem. Estas fontes incluem ficheiros JSON, ficheiros CSV, SQL, MongoDB, armazenamento de mesa Azure, Amazon DynamoDB e coleções API Da API Da Amazon Cosmos DB SQL.      |
| Versões DSVM suportadas      | Windows      |
| Utilizações típicas      | Importar ficheiros de um VM para cosmosDB, importar dados do armazenamento de mesa Azure para cosmosDB, e importar dados de uma base de dados do Microsoft SQL Server para a CosmosDB.     |
|  Como utilizar / executá-lo?    |   Para utilizar a versão da linha de comando, abra um pedido de comando e escreva `dt`. Para utilizar a ferramenta GUI, abra um pedido de comando e escreva `dtui`.    |
| Links para amostras      | [Dados de importação da CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Explorador do Storage do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Interface gráfica do utilizador para interagir com ficheiros armazenados na nuvem Azure. |
| Versões DSVM suportadas      | Windows      |
| Utilizações típicas      | Dados de importação e exportação do DSVM.    |
|  Como utilizar / executá-lo?    | Procure "Azure Storage Explorer" no menu Iniciar. |
| Links para amostras      | [Explorador do Armazenamento do Azure](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta de SQL Server para copiar dados entre o SQL Server e um ficheiro de dados.      |
| Versões DSVM suportadas      | Windows      |
| Utilizações típicas      | Importar um ficheiro CSV numa tabela SQL Server e exportar uma tabela SQL Server para um ficheiro.      |
|  Como utilizar / executá-lo?    |   Abra um pedido de comando e escreva `bcp` para obter ajuda.    |
| Links para amostras      | [utilidade bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Ferramentas relacionadas no DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para montar um recipiente de armazenamento Azure Blob no sistema de ficheiros Linux.      |
| Versões DSVM suportadas      | Linux      |
| Utilizações típicas      | Ler e escrever a bolhas num recipiente.      |
|  Como usá-lo e executá-lo?    |   Corra _blobfuse_ num terminal.    |
| Links para amostras      | [blobfuse no GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Ferramentas relacionadas no DSVM      | CLI do Azure      |
