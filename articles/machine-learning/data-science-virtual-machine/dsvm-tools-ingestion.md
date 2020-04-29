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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270059"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados da Máquina Virtual da Ciência dos Dados

Como um dos primeiros passos técnicos de um projeto de ciência de dados ou IA, você deve identificar os conjuntos de dados a serem usados e trazê-los para o seu ambiente de análise. A Máquina Virtual da Ciência dos Dados (DSVM) fornece ferramentas e bibliotecas para trazer dados de diferentes fontes para armazenamento de dados analíticos localmente no DSVM, ou para uma plataforma de dados, seja na nuvem ou no local.

Aqui estão algumas ferramentas de movimento de dados que estão disponíveis no DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados do armazenamento da Azure Blob na Azure Data Lake Store. Também pode copiar dados entre duas contas da Azure Data Lake Store.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importando várias bolhas do armazenamento azure blob para a Loja de Lagos De Dados Azure.      |
|  Como usá-lo/ executá-lo?    |   Abra um pedido `adlcopy` de comando e escreva para obter ajuda.    |
| Ligações a amostras      | [Utilizar o AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Ferramentas relacionadas no DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>CLI do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta de gestão para o Azure. Também contém verbos de comando para mover dados de plataformas de dados azure como o armazenamento Azure Blob e a Azure Data Lake Store.     |
| Versões DSVM suportadas      | Windows, Linux     |
| Usos típicos      | Dados de importação e exportação de e para o Armazenamento Azure e loja de datas azure.      |
|  Como usá-lo/ executá-lo?    |   Abra um pedido `az` de comando e escreva para obter ajuda.    |
| Ligações a amostras      | [Utilizar a CLI do Azure](https://docs.microsoft.com/cli/azure)     |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados de e para ficheiros locais, armazenamento, ficheiros e tabelas da Azure Blob.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Copiar ficheiros para armazenamento de Blob Azure e copiar bolhas entre contas.      |
|  Como usá-lo/ executá-lo?    |   Abra um pedido `azcopy` de comando e escreva para obter ajuda.    |
| Ligações a amostras      | [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Ferramentas relacionadas no DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Ferramenta de migração de dados da Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta para importar dados de várias fontes para o Azure Cosmos DB, uma base de dados NoSQL na nuvem. Estas fontes incluem ficheiros JSON, ficheiros CSV, SQL, MongoDB, armazenamento de mesa Azure, Amazon DynamoDB e coleções API Da API Da Amazon Cosmos DB SQL.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importar ficheiros de um VM para cosmosDB, importar dados do armazenamento de mesa Azure para cosmosDB, e importar dados de uma base de dados do Microsoft SQL Server para a CosmosDB.     |
|  Como usá-lo/ executá-lo?    |   Para utilizar a versão da linha de `dt`comando, abra um pedido de comando e escreva . Para utilizar a ferramenta GUI, abra `dtui`um pedido de comando e escreva .    |
| Ligações a amostras      | [Dados de importação da CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Explorador do Storage do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Interface gráfica do utilizador para interagir com ficheiros armazenados na nuvem Azure. |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Dados de importação e exportação do DSVM.    |
|  Como usá-lo/ executá-lo?    | Procure "Azure Storage Explorer" no menu Iniciar. |
| Ligações a amostras      | [Explorador do Storage do Azure](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta SQL Server para copiar dados entre o Servidor SQL e um ficheiro de dados.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importar um ficheiro CSV numa tabela SQL Server e exportar uma tabela SQL Server para um ficheiro.      |
|  Como usá-lo/ executá-lo?    |   Abra um pedido `bcp` de comando e escreva para obter ajuda.    |
| Ligações a amostras      | [utilidade bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Ferramentas relacionadas no DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para montar um recipiente de armazenamento Azure Blob no sistema de ficheiros Linux.      |
| Versões DSVM suportadas      | Linux      |
| Usos típicos      | Ler e escrever a bolhas num recipiente.      |
|  Como usá-lo e executá-lo?    |   Corra _blobfuse_ num terminal.    |
| Ligações a amostras      | [blobfuse no GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Ferramentas relacionadas no DSVM      | CLI do Azure      |
